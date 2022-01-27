# סיסקול 61 - waitpid
גם wait וגם waitpid פשוט קוראים לwait4 מבפנים (שהוא מספר הסיסקול  61 אז קצת שיקרתי בכותרת פה) אבל wait4 פחות פופולרי כי מה שהוא מוסיף זה את היכולת להחזיר גם סטטיסטיקות על השימוש במעבד ובזיכרון.

```c
pid_t wait(int *wstatus)
pid_t waitpid(pid_t pid, int *wstatus, int options)
```

ב-pid אפשר להעביר:
* מספר חיובי - לחכות לchild process שה-pid שלו שווה ל-pid.
* -1 - לחכות לאיזשהו child process. הסיסקול `wait(x)` שקול ל-`waitpid(-1, x, 0)`

יש [עמוד ב-man](https://www.man7.org/linux/man-pages/man2/getpgid.2.html#NOTES) על הבאים אבל הם לא מאוד רלוונטים (שימושי בשביל למממש פיצ'רים מתקדמים של job control בshell):
* 0 - אותו process group id כמו שלנו.
* שלילי - כל process עם אותו ה-process group id.

בoptions אפשר להעביר:
* WNOHANG - לחזור מיד אם שום פרוסס לא עצר
* WUNTRACED - לחזור גם אם פרוסס עצר אבל אין ptrace (עוד לא הגענו לזה, סיסקול מגניב).
* WCONTINUED - לחזור גם אם child חזר לתחיה בגלל SIGCONT שממשיך פרוסס שנעצר.

ואז waitpid מחזיר בסוף את ה-pid ואם wstatus הוא לא NULL אז הוא מחזיר אליו מידע על נסיבות השינוי במצב הפרוסס. את wstatus קוראים עם המקרואים הבאים:
* WIFEXITED(wstatus) - אם הchild עצר כרגיל
* WEXITSTATUS(wstatus) - להריץ רק אם WIFEXITED הוא true. זה הexit status.
* WIFSIGNALED(wstatus), WTERMSIG(wstatus) - אם הפרוסס נעצר בגלל סיגנל כמו SIGKILL.

**למתקדמים**:

* WCOREDUMP(wstatus) - האם נוצר coredump (להריץ רק אם WIFSIGNALED)
* WIFSTOPPED, WSTOPSIG - דורשים WUNTRACED או ptrace
* WIFCONTINUED - דורש WCONTINUED

[עמוד בman](https://man7.org/linux/man-pages/man2/waitpid.2.html)