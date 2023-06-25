The [Registry](https://en.wikipedia.org/wiki/Windows_Registry) is a hierarchical database in Windows critical for the operating system.

It stores low-level settings for the Windows operating system and applications that choose to use it. 

It is divided into computer-specific and user-specific data. 

We can open the Registry Editor by typing `regedit` from the command line or Windows search bar.

![image](https://academy.hackthebox.com/storage/modules/49/regedit.png)

The tree-structure consists of main folders (root keys) in which subfolders (subkeys) with their entries/files (values) are located. 

There are 11 different types of values that can be entered in a subkey.

Source: [https://docs.microsoft.com/en-us/windows/win32/sysinfo/registry-value-types](https://docs.microsoft.com/en-us/windows/win32/sysinfo/registry-value-types)


Each folder under `Computer` is a key. 

The root keys all start with `HKEY`. 

A key such as `HKEY-LOCAL-MACHINE` is abbreviated to `HKLM`. 

HKLM contains all settings that are relevant to the local system. 

This root key contains six subkeys like `SAM`, `SECURITY`, `SYSTEM`, `SOFTWARE`, `HARDWARE`, and `BCD`, loaded into memory at boot time (except `HARDWARE` which is dynamically loaded).

![image](https://academy.hackthebox.com/storage/modules/49/regedit2.png)

The entire system registry is stored in several files on the operating system. You can find these under `C:\Windows\System32\Config\`.

```powershell-session
PS C:\htb> ls

    Directory: C:\Windows\system32\config
```

The user-specific registry hive (HKCU) is stored in the user folder (i.e., `C:\Windows\Users\<USERNAME>\Ntuser.dat`).

```powershell-session
PS C:\htb> gci -Hidden

    Directory: C:\Users\bob
```

