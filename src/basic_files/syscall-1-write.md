# סיסקול 1 - write
```c
ssize_t write(int fd, void *buf, size_t count)
```

כותב לכל היותר count בייטים מ-fd לתוך מ-buf ומחזיר את כמות הבייטים שהוא כתב. אין שום הבטחה שכש-write חוזר המידע באמת נכתב לדיסק, או אפילו שיש מקום פנוי לכתיבה הזו, אבל יש הבטחה שאם עושים read מיד אחר כך באותו הקובץ יהיה את אותו המידע.

הסיבה שזה "לכל היותר" היא אחת מהבאות:
* נגמר המקום על הדיסק
* סתם שגיאת I/O, לפעמים אפילו קשורה למשהו שקרה בכתיבה קודמת

[עמוד ב-man](https://man7.org/linux/man-pages/man2/write.2.html)

## דוגמה לשימוש נכון ב-read וב-write
```c
#include <stdio.h>
#include <errno.h>
#include <unistd.h>

int main() {
    char buf[1024];
    ssize_t cnt;
    while ((cnt = read(0, buf, sizeof(buf))) != 0) {
        if (cnt == -1 && errno != EINTR) {
            perror("read error");
            return 1;
        }

        char *bufp = buf;
        while (cnt > 0) {
            ssize_t written = write(1, bufp, cnt);
            if (written == -1 && errno != EINTR) {
                perror("write error");
                return 1;
            }
            // advance buffer by the amount of bytes written
            bufp += written;
            cnt -= written;
        }
    }

    return 0;
}
```