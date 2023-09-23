
### Syntax

```
rsync://rsync-connect@127.0.0.1 with password Hcg3HP67@TW@Bc72v
```


https://www.tecmint.com/rsync-local-remote-file-synchronization-commands/

## Rsync Command Syntax

The **rsync** command follows the following syntax:

 ```
rsync [OPTIONS] SOURCE DESTINATION
```

Here’s an explanation of the different components and options used with **rsync** commands:

- `-v` – Verbose output, displaying detailed information about the transfer.
- `-r` – copies data recursively (but doesn’t preserve timestamps and permission while transferring data.
- `-a` – archive mode, which allows copying files recursively and it also preserves symbolic links, file permissions, user & group ownerships, and timestamps.
- `-z` – Compress files during transfer to reduce network usage.
- `-h` – human-readable, output numbers in a human-readable format.
- `-P` – Show progress during the transfer.
- `SOURCE` – Specifies the source file(s) or directory to be transferred, which can be a local or a remote location.
- `DESTINATION` – Specifies the destination path where the files or directories will be copied. Similar to the source, it can be a local path or a remote location.


- rsync command to download file to local machine using
```
rsync -av rsync://rsync-connect@10.10.146.124:873/files ./rsync
```


- rsync command to upload file to remote machine using

```
rsync -ahv ./id_rsa.pub rsync://rsync-connect@10.10.146.124:873/files/sys-internal/.ssh/authorized_keys --inplace --no-o --no-g
```