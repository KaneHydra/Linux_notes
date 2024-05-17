---
id: prevent_non_root_user_shutdown_reboot
aliases: []
tags: []
---

# prevent non-root users's shutdown or reboot commands in Linux

To prevent non-root users from issuing shutdown or reboot commands in Linux, you have a few options:

1. **PolicyKit Rules**:

    - **ConsoleKit** manages shutdown functions, and **PolicyKit** handles these actions. You can adjust the policies by creating a rule file. Here's how:

        - Edit the file `/etc/polkit-1/rules.d/20-disallow-shutdown.rules`.
        - Add the following rule to disallow shutdown and restart for users in the "users" group:
            ```c
            polkit.addRule(function(action, subject) {
                if ((action.id == "org.freedesktop.consolekit.system.stop" || action.id == "org.freedesktop.consolekit.system.restart") && subject.isInGroup("users")) {
                    return subject.active ? polkit.Result.AUTH_ADMIN : polkit.Result.NO;
                }
            });
            ```

    - Alternatively, you can modify the file `/etc/polkit-1/localauthority/50-local.d/20-disallow-shutdown.pkla` with the following content:
        ```ini
        [Disallow shutdown]
        Identity=unix-group:users
        Action=org.freedesktop.consolekit.system.stop;org.freedesktop.consolekit.system.restart
        ResultAny=no
        ResultInactive=no
        ResultActive=auth_admin
        ```

2. **File Permissions**:

    - Remove the executable permission for shutdown and reboot commands for all users except the root user:
        ```sh
        sudo chmod o-x /sbin/shutdown
        sudo chmod o-x /sbin/reboot
        sudo chmod o-x /sbin/halt
        sudo chmod o-x /sbin/poweroff
        ```

3. **Systemd and Logind**:
    - If you're using **systemd**, you can create a rule to control shutdown actions:
        - Monitor system messages with `journalctl -f`.
        - Create the file `/etc/polkit-1/rules.d/60-noreboot_norestart.rules` (in JavaScript).
        - Add logic to allow users in the "power" group or require superuser authorization for specific actions (e.g., reboot):
            ```js
            const power_actions = [
                "org.freedesktop.login1.reboot",
                "org.freedesktop.login1.reboot-multiple-sessions",
                // Add other actions as needed
            ];
            ```

Remember that these methods help restrict shutdown and reboot commands, but physical access to the server (such as pulling the plug) is still a consideration. Choose the approach that best fits your system's requirements! 🚀🔒

For more details, you can refer to the Super User discussion [here](https://superuser.com/questions/354678/what-is-the-correct-way-to-prevent-non-root-users-from-issuing-shutdowns-or-rebo) and the Fedingo guide [here](https://fedingo.com/how-to-disable-shutdown-reboot-commands-in-linux/).¹²

來源: 與 Bing 的交談， 2024/3/18
(1) [linux - What is the correct way to prevent non-root users from issuing shutdowns or reboots - Super User.](https://superuser.com/questions/354678/what-is-the-correct-way-to-prevent-non-root-users-from-issuing-shutdowns-or-rebo.)
(2) [How to Disable Shutdown & Reboot Commands in Linux - Fedingo.](https://fedingo.com/how-to-disable-shutdown-reboot-commands-in-linux/.)
(3) [Linux shutdown permission - Super User.](https://superuser.com/questions/666687/linux-shutdown-permission.)
(4) [How to prevent poweroff / shutdown / reboot / suspend / hibernate by non-root users in Ubuntu Xenial 16.04?.](https://askubuntu.com/questions/989523/how-to-prevent-poweroff-shutdown-reboot-suspend-hibernate-by-non-root-us.)
