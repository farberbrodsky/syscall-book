# סיסקול 22, 293 - pipe, pipe2
```c
int pipe(int pipefd[2])
int pipe2(int pipefd[2], int flags)
```

הסיסקול הזה יוצר pipe, שהוא זוג file descriptors שמשומשים בשביל תקשורת בין פרוססים. מ-pipefd[0] אפשר רק לקרוא ול-pipefd[1] אפשר רק לכתוב - אם רוצים תקשורת דו צדדית אפשר להשתמש בsocketpair או פשוט בשני pipeים נפרדים. כל מה שכותבים ב-write end הולך ל-read end וזה בעצם הסיסקול שבו משתמשים בשביל ה-pipe שכולנו מכירים ב-shell: כשאנחנו עושים ls | grep hello, אנחנו:
* יוצרים pipe
* עושים fork בשביל ls שב-fork הזה אנחנו שמים ב-file descriptor 1 את ה-write end
* עושים fork בשביל grep hello שב-fork הזה אנחנו שמים ב-file descriptor 0 את ה-read end
* התוצאה: שני הפרוססים רצים וכותבים אחד לשני

לpipe יש מגבלת מקום - בדרך כלל הוא יכול לשמור לכל היותר מגאבייט אחד, ואם כותבים יותר אז write יחכה עד שיקראו בצד השני.

ואפשר לדעת מתי סוגרים את הצד השני של ה-pipe: מקבלים סיגנל SIGPIPE (עוד לא עברנו על מה זה סיגנל אבל תבינו אחר כך) ו-write יחזיר EPIPE

עם pipe2 אפשר להוסיף flags שהם: O_CLOEXEC, O_DIRECT, O_NONBLOCK.

עם O_DIRECT אפשר לגרום ל-pipe לכתוב בכל פעם רק PIPE_BUF בייטים, או לפצל את הכתיבה, וזה בעצם יחידה אחת שאי אפשר לפרק. אם ב-read מנסים לקרוא פחות מזה, אז כל הבייטים האחרים ביחידה הזו נמחקים. זה המצב הכי יעיל של pipe אם רוצים לעשות אותו הכי מהיר שאפשר.

[עמוד ב-man](https://man7.org/linux/man-pages/man2/pipe.2.html)