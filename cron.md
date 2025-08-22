# Cron Cheater

`cron` is a time-based job scheduler in Unix-like operating systems. Users who set up and maintain software environments use `cron` to schedule jobs (commands or shell scripts) to run periodically at fixed times, dates, or intervals.

---

## 1. Cron Job Syntax Explained

A `cron` job entry typically consists of six fields: five for specifying the time and date, and one for the command to be executed.

```
* * * * * command_to_execute
- - - - -
| | | | |
| | | | ----- Day of week (0 - 7) (Sunday is 0 or 7)
| | | ------- Month (1 - 12)
| | --------- Day of month (1 - 31)
| ----------- Hour (0 - 23)
------------- Minute (0 - 59)
```

**Special Characters:**

*   `*` (Asterisk): Matches any value for the field.
*   `,` (Comma): Specifies a list of values (e.g., `1,15` for the 1st and 15th).
*   `-` (Hyphen): Specifies a range of values (e.g., `9-17` for hours 9 through 17).
*   `/` (Slash): Specifies step values (e.g., `*/15` for every 15 minutes).

---

## 2. Managing Cron Jobs (`crontab` command)

The `crontab` command is used to create, view, and modify your personal cron jobs.

*   **Edit your crontab**: Opens your crontab file in a text editor (usually `vi` or `nano`). This is where you add or modify cron job entries.
    ```bash
    crontab -e
    ```
*   **List your current cron jobs**: Displays all entries in your crontab.
    ```bash
    crontab -l
    ```
*   **Remove all your cron jobs**: **Use with caution!** This deletes your entire crontab without confirmation.
    ```bash
    crontab -r
    ```
*   **Remove with confirmation**: A safer way to remove all cron jobs.
    ```bash
    crontab -i
    ```

---

## 3. Self-explanatory crontab syntax examples:

Your original list of self-explanatory cron job examples is preserved below.

*   Every day at 4:30 AM:
    ```cron
    30 4 * * * echo "It is now 4:30 am."
    ```
*   Every day at 10:00 PM:
    ```cron
    0 22 * * * echo "It is now 10 pm."
    ```
*   At 3:30 PM on December 25th (Christmas Day), every year:
    ```cron
    30 15 25 12 * echo "It is 3:30pm on Christmas Day."
    ```
*   At 3:30 AM every day:
    ```cron
    30 3 * * * echo "Remind me that it's 3:30am every day."
    ```
*   At the start of every hour (e.g., 1:00, 2:00, 3:00, etc.):
    ```cron
    0 * * * * echo "It is the start of a new hour."
    ```
*   At 6:00 AM on the 1st and 15th of every month:
    ```cron
    0 6 1,15 * * echo "At 6am on the 1st and 15th of every month."
    ```
*   At 6:00 AM on Tuesday, Wednesday, and Friday:
    ```cron
    0 6 * * 2,3,5 echo "At 6am on Tuesday, Wednesday and Friday."
    ```
*   Just before midnight (11:59 PM) on weekdays (Monday-Friday):
    ```cron
    59 23 * * 1-5 echo "Just before midnight on weekdays."
    ```
*   Every two hours (e.g., 0:00, 2:00, 4:00, etc.):
    ```cron
    0 */2 * * * echo "Every two hours."
    ```
*   At 8:00 PM on Thursday:
    ```cron
    0 20 * * 4 echo "8pm on a Thursday."
    ```
*   At 8:00 PM on Thursday (using day name):
    ```cron
    0 20 * * Thu echo "8pm on a Thursday."
    ```
*   Every 15 minutes from 9:00 AM to 5:00 PM on weekdays (Tuesday-Friday):
    ```cron
    */15 9-17 * * 2-5 echo "Every 15 minutes from 9am-5pm on weekdays."
    ```

---

## 4. Special Time Strings

`cron` supports several shortcuts for common schedules.

*   `@reboot`    : Run once after every reboot.
*   `@yearly`    : Run once a year, `0 0 1 1 *`.
*   `@annually`  : Same as `@yearly`.
*   `@monthly`   : Run once a month, `0 0 1 * *`.
*   `@weekly`    : Run once a week, `0 0 * * 0`.
*   `@daily`     : Run once a day, `0 0 * * *`.
*   `@midnight`  : Same as `@daily`.
*   `@hourly`    : Run once an hour, `0 * * * *`.

**Example:**
```cron
@reboot /path/to/script/startup.sh
@hourly /usr/bin/some_command --clean-logs
@yearly echo "Happy New Year!"
```

---

## 5. Environment Variables & Output Redirection

Cron jobs run in a minimal environment. It's crucial to set up the necessary environment variables and handle command output.

*   **PATH and SHELL**: Always specify full paths for commands or set `PATH` at the top of your crontab.
    ```cron
    SHELL=/bin/bash
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    * * * * * /usr/bin/my_script.sh
    ```
*   **MAILTO**: By default, `cron` emails the output of commands to the user who owns the crontab. You can change this or disable it.
    ```cron
    MAILTO="your_email@example.com" # Send email to this address
    MAILTO="" # Disable email for this crontab
    ```
*   **Output Redirection**: To avoid getting emails for every cron job or to log output, redirect `stdout` and `stderr`.
    *   **Send all output to a log file:**
        ```cron
        0 1 * * * /path/to/my_script.sh >> /var/log/my_script.log 2>&1
        ```
    *   **Discard all output (silence the command):**
        ```cron
        0 1 * * * /path/to/my_script.sh > /dev/null 2>&1
        ```

---

## 6. Best Practices

*   **Use Full Paths**: Always specify the full path to executables and scripts.
*   **Test Commands First**: Run your command directly in the shell before adding it to `crontab`.
*   **Log Output**: Redirect output to a log file for debugging and monitoring.
*   **Separate Scripts**: For complex tasks, write a shell script and call it from `cron`. This makes debugging easier.
*   **Permissions**: Ensure your scripts have execute permissions (`chmod +x script.sh`).
*   **Comments**: Use `#` to add comments in your crontab file to explain complex jobs.
