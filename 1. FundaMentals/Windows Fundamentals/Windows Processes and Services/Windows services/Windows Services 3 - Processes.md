Processes run in the background on Windows systems. They either run automatically as part of the Windows operating system or are started by other installed applications.

Processes associated with installed applications can often be terminated without causing a severe impact on the operating system. 

Certain processes are critical and, if terminated, will stop certain components of the operating system from running properly. 

Some examples include the Windows Logon Application, System, System Idle Process, Windows Start-Up Application, Client Server Runtime, Windows Session Manager, Service Host, and Local Security Authority Subsystem Service (LSASS) process.

## Local Security Authority Subsystem Service (LSASS)

`lsass.exe` is the process that is responsible for enforcing the security policy on Windows systems. When a user attempts to log on to the system, this process verifies their log on attempt and creates access tokens based on the user's permission levels. LSASS is also responsible for user account password changes. All events associated with this process (logon/logoff attempts, etc.) are logged within the Windows Security Log. LSASS is an extremely high-value target as several tools exist to extract both cleartext and hashed credentials stored in memory by this process.