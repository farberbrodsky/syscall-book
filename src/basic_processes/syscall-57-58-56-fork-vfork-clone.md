# סיסקול 57 - fork
```c
pid_t fork(void)
```

יוצר פרוסס חדש לגמרי (שנקרא child process) שהוא עותק של הפרוסס המקורי - שמתחיל עם בדיוק אותו הזיכרון ובדיוק באותו המקום בקוד **ועם בדיוק אותם הקבצים** - רק שה-parent מקבל כערך החזרה את ה-pid של ה-child וה-child מקבל כערך החזרה את המספר 0 (או -1 אם הייתה שגיאה). הזיכרון של ה-child process הוא עותק של הזיכרון המקורי (למרות שיש אופטימיזציה שמעתיקה רק כשצריך שנקראת copy on write - איך שהיא עובדת זה שהיא אומרת למעבד שכל הכתובות של הפרוסס הן לקריאה בלבד ובכל פעם שיש interrupt כי אסור לכתוב הקרנל יוצר עותק חדש של האיזור הזה בזיכרון).

**הערות:**
* ה-child process נפרד לגמרי ומקבל אפילו pid משלו.
* אפשר לבקש שה-child process לא יקבל מיפויי זיכרון מסוימים עם MADV_DONTFORK או MADV_WIPEONFORK (שמאפס את הזיכרון אחרי fork).

[עמוד ב-man](https://man7.org/linux/man-pages/man2/fork.2.html)

# סיסקול 58 - vfork
```c
pid_t vfork(void)
```

דומה ל-fork, אבל הדבר היחיד שמותר ל-child process לעשות זה לקרוא ישר ל-`execve` או לקרוא ל-`_exit` (לא exit רגיל כי הוא יכול לעשות פעולות של io לפני היציאה). הפרוסס המקורי יחזור לרוץ רק אחרי שזה יקרה. הסיבה שאסור לעשות כלום היא כי ה-child process לא מקבל זיכרון משלו ולכן אם הוא היה קורא לפונקציה ומשנה את המחסנית אז יכולה להיות התנהגות לא מוגדרת. הסיבה שמשתמשים ב-vfork זה שהוא יכול להיות הרבה יותר מהיר.

[עמוד ב-man](https://man7.org/linux/man-pages/man2/vfork.2.html)

# סיסקול 56 - clone
```c
int clone(int (*fn)(void *), void *stack, int flags, void *arg, ...
/* pid_t *parent_tid, void *tls, pid_t *child_tid */ )
```

גרסה מוכללת של fork ו-vfork ועוד לעשות _הכל_ אבל ממש _הכל_ (כולל גם ת'רדים, [קונטיינרים](https://opensource.com/resources/what-are-linux-containers), ..., הכל בflagים). מעבירים ל-clone פונקציה שרצה עם הארגומנט arg ומשתמש במחסנית שב-stack (שזה כבר ממש לא נוח). מה ש-fn מחזיר הוא ה-exit status של ה-child process.

[עמוד ב-man](https://man7.org/linux/man-pages/man2/clone.2.html)