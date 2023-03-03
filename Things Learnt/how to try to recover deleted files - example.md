When I file gets deleted, the structure of the filesystem removes the metadata about that file. That includes the timestamps, filename, and a pointer to where the raw file is on disk. The delete operation does not go to that point on the disk and do anything to clean up the data, like write all nulls over it.

That means there’s a good chance that the contents of `root.txt` are still there, even if the filesystem no longer knows of a file by that name.

The raw USB device is `/dev/sdb`, and I can interact with that just like any other file. I’ll show a couple different ways to recover the flag.

#### grep / strings

`grep` is made to pull strings of a given pattern out of a file (which I can treat the raw device as). I’ll call with the following arguments:

-   `-a` - Process a binary file as if it were text
-   `-P` - Interpret PATTERN as a Perl regular expression
-   `-o` - Print only the matched (non-empty) parts of a matching line, with each such part on a separate output line.

I’ll give it the pattern `[a-fA-F0-9]{32}`, which should find a 32-character hex string. It works:

```
root@raspberrypi:/# grep -aPo '[a-fA-F0-9]{32}' /dev/sdb
3d3e483143ff12ec505d026fa13e020b
```

Knowing that the flag is a string, I can also use `strings`:

```
root@raspberrypi:/# strings /dev/sdb -n 32
3d3e483143ff12ec505d026fa13e020b
Damnit! Sorry man I accidentally deleted your files off the USB stick.
Do you know if there is any way to get them back?
```