# cyberpatriot-checklist-ubuntu

Ubuntu cyber-patriot checklist

1. Forensic questions

2. Secure root
        In /etc/ssh/sshd_config set "PermitRootLogin no"

3. Disable guest user
        In /etc/lightdm/lightdm.conf add the line "allow-guest=false"
        Restart with "sudo restart lightdm" (will log you out)

4. Check users
        In /etc/passwd check for users that
            Are uid 0 (root users)
            Are not allowed in the readme (comment them out)
        In /etc/group verify users are in the correct groups and that no groups have a GUD of 0
        Add any users specified in readme with "adduser [username]"

5. Secure sudo
        Check /etc/sudoers to verify only users from group sudo can sudo (do so with visudo)
        Verify only admins have access to /etc/sudoers and /etc/sudoers.d
        Check /etc/group and remove non-admins from sudo and admin groups
        Verify with the command "sudo -l -U [username]" to see sudo permissions

6. Check for unauthorized files/packages
        Use "cd /home" then "ls -Ra *"  to find unauthorized files (can also use tree for this)
            Can also use "ls *.[filetype]" to search by file types
        Check for unauthorized packages with "apt list --installed"
        Check for unauthorized services with "service --status-all" (can also use Synaptic or BUM for managing services)

7. Change password requirements
        In /etc/login.defs add
            PASS_MIN_DAYS 7
            PASS_MAX_DAYS 90
            PASS_WARN_AGE 14
        Use "apt-get install libpam-cracklib" then in /etc/pam.d/common-password add
            "minlen=8" and "remember=5" to the line with pam_unix.so in it
            Add "ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-" to the line with pam.cracklib.so in it
        In /etc/pam.d/common-auth add "deny=5 unlock_time=1800" to the end of the line with "pam_tally2.so" in it to add an account lockout policy

8. Change all passwords
        Use the command "passwd [user]" to change passwords to a secure password
        Use "passwd -a -S" to verify all passwords are set up correctly

9. Enable auto-updates + other small things
        In GUI go to settings and under updates set everything to the best available option
        In Firefox/Chrome/browser go to settings and read through and set everything to the most secure option (auto-updates, pop-up blocker, block dangerous downloads, display warning on known bad sites, etc.)
        Start updates with "apt-get update" and "apt-get upgrade"
        Set a message of the day in /etc/motd
        Disable sharing the screen by going to settings -> sharing then turn it off
        Use "apt-get autoremove --purge samba" to remove samba

10. Secure ports
        Use the command "ss -ln" to check for open ports that are not on the loopback
        For open ports that need to be closed
            Use "lsof -i :[port]" then copy the program listening on the port with "whereis [program]" then copy where the program is with "dpkg -S [location]" then remove the associated package with "apt-get purge [package]"
            Verify the removal with "ss -ln"

11. Secure the network
        Enable the firewall with "ufw enable"
        Enable syn cookie protection with "sysctl -n net.ipv4.tcp_syncookies"
        Disable IPv6 with "echo "net.ipv6.conf.all.disable_ipv6 = 1" | sudo tee -a /etc/sysctl.conf" (make sure it isn't needed in read-me)
        Disable IP forwarding with "echo 0 | sudo tee /proc/sys/net/ipv4/ip_forward"
        Prevent IP spoofing with "echo "nospoof on" | sudo tee -a /etc/host.conf"
        Disable ICMP responses with "echo “net.ipv4.icmp_echo_ignore_all = 1” >> /etc/sysctl.conf"
        Use "sysctl -p" then restart sysctl with "sysctl --system"
        Configure firewall (can also be done through Gufw)
            Check for rules with "ufw status numbered" and delete any with "ufw delete [number]"
            Add new rules with "ufw allow [port]"

12. Secure services
        Check config files for any services installed to secure them (PHP, SQL, WordPress, FTP, SSH, and Apache are common services that need to be secured)
            For hosting services such as WordPress, FTP, or websites verify the files are not sensitive or prohibited
            Google "how to secure [service] ubuntu"
        Verify all services are legitimate with "service --status-all" (can also use Synaptic or BUM)
        Verify the services do not use any default credentials

13. Check permissions for sensitive files
        Check the permissions of the files with "ls -al"
            Check /etc/passwd, /etc/group, /etc/shadow, /etc/sudoers, and /var/www
        The permissions should be "-rw-r----- root: shadow"
        Use "chmod -R 640 [path]" to modify the permissions

14. Check for malware
        Check /etc/rc.local to see if it contains anything other than "exit 0"
        Use "ps -aux" to list running services, check if lkl, uberkey, THC-vlogger, PyKeylogger, or logkeys are running
        Install rkhunter then update the properties with "rkhunter --propupd" then run with "rkhunter --checkall"

15. Install auditing tool (not sure if needed)
        Install with "apt-get install auditd"
        Run it with "auditctl -e 1"

16. Secure SSH (if needed in readme)
        In /etc/ssh/sshd_config
            Change the port from default
            Set LoginGraceTime to 20
            Set PermitRootLogin to no
            Set StrictModes to yes
            Set MaxAuthTries to 3
            Set PermitEmptyPasswords to no
            Change and uncomment protocol line to "Protocol 2"
            Optional: For keyless authentication set PasswordAuthentication to no
        Restart ssh with "service sshd restart"
