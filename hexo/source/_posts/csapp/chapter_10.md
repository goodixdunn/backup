---
title: 系统级I/O
---
所有的编程语言都提供了执行I/O的较高级别的工具。例如，ANSI C提供了标准的I/O库；C++提供重载的<<以及>>提供类似的功能。 在Unix系统中，是通过使用系统级的I/O来实现这些较高级别的I/O函数的，一般情况下，高级别的I/O工作良好，那为什么还需要学习系统级的I/O呢？
1. 了解系统级I/O有助于了解系统概念。在unix中，“一切皆文件”，了解了系统的I/O，可以帮助了解进程、文件
2. 标准的I/O也有不适用的范围，例如网络编程；

## 系统级函数接口
> [open](http://man7.org/linux/man-pages/man2/open.2.html)  [read](http://man7.org/linux/man-pages/man2/read.2.html)  [write](http://man7.org/linux/man-pages/man2/write.2.html)  [close](http://man7.org/linux/man-pages/man2/close.2.html) 
 
```C
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int open(const char *pathname, int flags);
int open(const char *pathname, int flags, mode_t mode);

#include <unistd.h>
ssize_t read(int fd, void *buf, size_t count);
	/* 成功返回读取的字节数，EOF则为0；若出错则为-1 */
ssize_t write(int fd, const void *buf, size_t count);
	/* 成功返回写的字节数，若出错则为-1 */
```
read/write 若返回-1.则errno会被设置，其中，关于errno多线程的问题，后续需要在关注（是否是每个线程都有自己的errno？？）
***注意：***
1. ssize_t 被定义为 int，而size_t被定义为 unsigned int；
2. 某些情况下可能会出现read/write传送的字节比要求的要少，原因可能为：
	- read时遇到EOF
	- read从终端
	- read/write网络套接字
	实际上，除了EOF，若是在读取**磁盘**文件或者写**磁盘**文件时不会遇到不足值；
正式因为可能遇到不足值，因此想要编写robust的网络应用程序，就必须通过反复调用read/write来处理不足值，因此才有了接下来的RIO包

## RIO(robust I/O)
该包主要应用于网络应用程序
**无缓冲的*输入输出*函数 ** ：对讲二进制数据读写到网络或者从网络读写二进制数据非常有用；能够处理不足值
**带缓冲的*输入*函数** ：高效的从文件中读取文本行和二进制数据，而且是线程安全的，它可以在同一个文件描述符上交错的调用，例如，可以从描述符上读一些文本行，然后读取一些二进制数据，在接着读取一些文本行。为什么会有带缓冲的呢？加入我们要读取文本行，如果我们一个字节一个字节读取然后去判断是否是‘\n'，那每次都要陷入内核，那效率就太低了，解决方法就是增加内部缓冲区，具体实现见代码。
robustio.c
```C
#include "robustio.h"

ssize_t rio_read(int fd, void *usrbuf, size_t count)
{
    size_t nleft = count;
    ssize_t nread = 0;
    char *bufp = usrbuf;

    while (nleft > 0) {
        if ((nread = read(fd, bufp, nleft)) < 0) {
            if (EINTR == errno) {    /* Interrupt by sig handler return */
                nread = 0;           /* and call read() again */
            } else {
                return -1;           /* errno set by read */
            }
        } else if (0 == nread) {
            break;
        } else {
            nleft -= nread;
            bufp += nread;
        }

    }

    return (count - nleft);
}

ssize_t rio_write(int fd, void *usrbuf, size_t count)
{
    size_t nleft = count;
    ssize_t nwrite = 0;
    char *bufp = usrbuf;

    while(nleft > 0) {
        if ((nwrite = write(fd, bufp, nleft)) <= 0) {
            if (EINTR == errno) {        /* Interrupt by sig handler return */
                nwrite = 0;              /* and call write() again */
            } else {
                return -1;               /* errno set by read */
            }
        } else {
            nleft -= nwrite;
            bufp += nwrite;
        }
    }

    return count;
}
```
这里注意一点，read 返回值有三种处理方法， 大于0，等于0，小于0，其中大于0为真真读取的字节个数，等于0说明遇到EOF，可以返回了，小于0，又要看是否是被信号中断了，若中断，则继续，否则返回-1；而write函数的返回值，若大于0，则返回实际写的字节数，小于等于0，则要看是否是被中断信号打断，若被打断，则继续写，否则返回-1
robustio.h
```C
#ifndef _ROBUSTIO_H_
#define _ROBUSTIO_H_

#include <unistd.h>     /* for read/write */
#include <errno.h>      /* for EINTR and errno */

ssize_t rio_read(int fd, void *usrbuf, size_t count);
ssize_t rio_write(int fd, void *usrbuf, size_t count);

#endif
```

test.c
```C
#include <stdio.h>
#include <sys/types.h>
#include <fcntl.h>

#include "robustio.h"

#define BUF_SIZE    4096
static char bufp[BUF_SIZE + 1] = { 0 };

int main(int argc, char **argv)
{
    char *filename = "testfile";
    int fd = 0;
    ssize_t nread = 0;
    ssize_t nwrite = 0;
    ssize_t nseek = 0;

    do {
        fd = open(filename, O_RDWR | O_CREAT);
        if (fd < 0) {
            printf("open [%s] file failed\n", filename);
            break;
        }
        printf("open [%s] file success\n", filename);

/* rio_write test */
        nseek = lseek(fd, 0, SEEK_END);
        nwrite = rio_write(fd, filename, sizeof(filename));

/* rio_read test */
        nseek = lseek(fd, 0, SEEK_SET);
        nread = rio_read(fd, bufp, BUF_SIZE);
        printf("nread=%zd\n", nread);
        if (nread > 0) {
            bufp[nread] = '\0';
            printf("%s\n", bufp);
        }
        close(fd);
    } while (0);
    return 0;
}

```
在命令行运行 gcc -o test robustio.c test.c 就会生成 test，然后执行 ./test
这个测试程序就是将字符串testfile写入到testfile文件中，并读取打印出来


