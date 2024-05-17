# Postmortem: The Great Downtime Debacle

### Issue Summary

- **Duration of Outage:** 3 hours (May 17, 2024, 6:00 AM - 9:00 AM WAT)
- **Impact:** The isolated Ubuntu 14.04 container running an Apache web server experienced 500 Internal Server Errors on GET requests, affecting all users attempting to access the service. Approximately 100% of the users were affected during this period.
- **Root Cause:** A misconfigured file path in the service configuration led to an incorrect file being referenced, causing the web server to fail when attempting to load the necessary file.

### Timeline

- **6:00 AM WAT:** Outage begins; GET requests to the server return 500 Internal Server Errors.
- **6:10 AM WAT:** Monitoring alert triggers due to a spike in 500 errors.
- **6:15 AM WAT:** I am notified and start investigating the issue.
- **6:20 AM WAT:** Checked running processes using `ps aux`; verified that `apache2` processes for `root` and `www-data` are running correctly.
- **6:30 AM WAT:** Investigated the `sites-available` folder in `/etc/apache2/`; confirmed the web server serves content from `/var/www/html/`.
- **6:40 AM WAT:** Ran `strace` on the PID of the `root` Apache process while curling the server; no useful information found.
- **7:00 AM WAT:** Repeated `strace` on the `www-data` process; identified an `-1 ENOENT (No such file or directory)` error for a key configuration file.
- **7:20 AM WAT:** Searched through `/var/www/html/` directory; found the erroneous file path in the main configuration file.
- **7:30 AM WAT:** Corrected the file path to reference the correct file.
- **7:45 AM WAT:** Tested with another `curl` command; confirmed 200 OK response.
- **8:00 AM WAT:** Created a Puppet manifest to automate the fix for future occurrences.
- **9:00 AM WAT:** Full service restoration confirmed.

### Root Cause and Resolution

- **Root Cause:** The outage was due to a misconfigured file path in the main configuration file of the service. This caused the Apache server to fail when attempting to load the required file.
- **Resolution:** The file path was corrected in the configuration file. This fix was then automated using a Puppet manifest to ensure the issue does not recur.

### Corrective and Preventative Measures

- **Improvements/Fixes:**
  - Conduct thorough testing of application configurations before deployment.
  - Implement automated code review processes to catch misconfigurations and similar errors.
  - Increase monitoring granularity to detect similar issues faster.
  - Introduce redundancy in the server setup to mitigate single points of failure.

- **Tasks:**
  1. **Automate Testing:** Implement automated testing for configuration files to catch errors before deployment.
  2. **Enhance Monitoring:** Set up enhanced monitoring with services like [UptimeRobot](https://uptimerobot.com/) for instant alerts.
  3. **Code Review:** Establish a code review process to catch and correct errors before they reach production.
  4. **Training:** Provide additional training for engineers on debugging and server management.
  5. **Disaster Recovery Drills:** Schedule regular disaster recovery drills to prepare for similar incidents.

### Conclusion

In conclusion, the outage was caused by a misconfiguration in the service's main configuration file. This incident highlights the importance of thorough testing, effective monitoring, and automation in preventing and quickly resolving outages. By implementing the corrective and preventative measures outlined above, we can reduce the likelihood of similar issues in the future.

To ensure such issues are promptly addressed, I have written a Puppet manifest which replaces any incorrect file paths in the configuration file.

Remember, while we aim for perfection, errors are inevitable. It's how we respond and learn from them that defines our success as engineers.


- [GitHub Repository Postmortem](https://github.com/Maxzeno/alx-system_engineering-devops/blob/main/0x19-postmortem/README.md)
