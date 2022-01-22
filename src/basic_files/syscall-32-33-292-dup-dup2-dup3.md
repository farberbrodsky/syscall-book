# סיסקול 32, 33, 292 - dup, dup2, dup3
**הקדמה**: כל file descriptor שאנחנו מכירים (בתור מספר שאנחנו מעבירים כל פעם ל-read ו-write ולבסוף ל-close) מצביע ל-file description בקרנל. אותו ה-file description יכול להיות בשימוש בכמה מספרים שונים ואפילו מכמה פרוססים שונים כפי שנראה מאוחר יותר. כל file description מחזיק מידע כמו איפה המצביע שיש לנו ל-read ו-write נמצא בקובץ וכו'. ה-file descriptor הוא סתם מספר שאנחנו משתמשים כדי לאמר לקרנל באיזה file description אנחנו מעוניינים.
```c
int dup(int oldfd)
int dup2(int oldfd, int newfd)
int dup3(int oldfd, int newfd, int flags)
```

הסיסקול dup יוצר file descriptor חדש שמתאר את אותו ה-file description, בלי close on execute (שנראה למה זה שימושי בפרוססים בסיסיים).

ו-dup2 מקבל גם את המספר של ה-file descriptor החדש שאנחנו רוצים. אם הוא כבר תפוס אז dup2 סוגר אותו ומחליף בקובץ שלנו.

ו-dup3 דומה ל-dup2 אבל הוא עושה שגיאה אם olfd == newfd, ואפשר להעביר אליו O_CLOEXEC.

[עמוד ב-man](https://man7.org/linux/man-pages/man2/dup.2.html)
