# OpenIPC Wiki
[Table of Content](../README.zh.md)

访问 SSH、telnet、FTP 和其他服务 
---------------------------------------------

很多时候，库存固件会提供对其操作系统的访问权限，但访问权限会使用未公开的密码关闭。我们可以在提取固件映像副本时恢复该密码的加密哈希值。

### 密码哈希

```console
$1$bh2njiGH$4duacOMcXDh6myANzbZTf.
```

散列盐密码字符串由三部分组成：散列算法标识符、盐和密码哈希，每部分前面都有一个美元符号。第一部分 `$1` 是用一个（很少两个）字符编码的散列算法。它表示用于生成哈希的加密方法：

- `$1` - MD5 算法。
- `$2` - Blowfish 算法。
- `$2a` - eksblowfish 算法
- `$5` - SHA-256 算法
- `$6` - SHA-512 算法

第二部分 `$bh2njiGH` 是盐 - 在对明文密码进行哈希处理之前添加到其中的字符串，目的是对同一密码生成的哈希值进行随机化，并防止 [彩虹表][1] 攻击。

最后一部分"$4duacOMcXDh6myANzbZTf."是哈希值。当您输入密码时，它会与提供的盐连接起来，然后使用提供的哈希算法进行哈希处理，并将结果与​​哈希值进行比较。相同的密码、盐和哈希方法将始终产生相同的结果。

哈希算法是一种单向加密方法，这意味着哈希不能解密回明文密码，但可以对明文密码的可用变体进行哈希处理，直到找到匹配项。这种方法称为[暴力攻击][2]。

IP 摄像机倾向于使用相对简单且快速的 MD5 哈希算法，因此使用密码破解软件和强大的计算资源，可以在几周或几天甚至几小时内破解原始明文密码，尤其是使用高质量的字典。

在上面的例子中，我们使用了密码"openipc"。您可以使用"mkpasswd"或"openssl"检查密码的有效性：

```bash
$ mkpasswd -m md5crypt -S bh2njiGH openipc
$1$bh2njiGH$4duacOMcXDh6myANzbZTf.
$ openssl passwd -1 -salt bh2njiGH openipc
$1$bh2njiGH$4duacOMcXDh6myANzbZTf.
```

找到密码后，最好将其公开，以便该领域的其他研究人员可以投入他们的密码资源来发现更多未知的密码。分享就是关爱，孩子们！

### 我们在不同固件中找到的一些密码

```
| Hash                                  | Plain text |
|---------------------------------------|------------|
| $1$MoCJ1nRA$NfsI1wlYcWoF5MbU4t3Og0    | ivdev      |
| $1$ZebZnWdY$QZ1Aa.7hwBshCS5k40MUE1    | xc12345    |
| $1$d3VPdE0x$Ztn09cyReJy5Pyn           | runtop10   |
| $1$qFa2kfke$vJob19l64Q6n8FvP8/kvJ0    | wabjtam    |
| $1$rHWQwR5V$i4FVDvwhuzau8msvAfHEt.    | 2601hx     |
| $1$tiaLlxGM$byeTUfQgqyET5asfwwNjg0    | hichiphx   |
| $1$0Me7S3z5$.uQ4Pr/QjJQ/0JUZI0w4m.    |            |
| $1$4dAkkeWK$HCy0K1z8E.wAuwgLV8bWd/    |            |
| $1$7bfnUEjV$3ogadpYTDXtJPV4ubVaGq1    |            |
| $1$7BqzlCqK$nQXIfc53c1ACEwzNg7G3D.    |            |
| $1$cNGGWwI/$5/mZTMlcVfJlpE5DGrdsl/    |            |
| $1$FMNq4QIj$lJg6WzZxy1HWl3sL.YwIq1    |            |
| $1$IZfqary9$IrG6loat5pDTBLr6ksKTD0    |            |
| $1$ocmTTAhE$v.q2/jwr4BS.20KYshYQZ1    |            |
| $1$OIKWDzOV$WjZNcNtHSKVscbi9WQcpu/    |            |
| $1$rnjbbPTD$tR9oAIWgUp/jRrhjDuUwp0    |            |
| $1$RYIwEiRA$d5iRRVQ5ZeRTrJwGjRy.B0    | xmhdipc    |
| $1$uF5XC.Im$8k0Gkw4wYaZkNzuOuySIx/    |            |
| $1$vN9F.lHa$E09mbCRo70834AUfkytpX     |            |
| $1$wbAnPk8f$yz0PI9vnyLRmWbENUnce3/    |            |
| $1$ybdHbPDn$ii9aEIFNiolBbM9QxW9mr0    |            |
| $1$yq01TaSp$lkN/azu3IxE97owy27pve.    |            |
| $1$yFuJ6yns$33Bk0I91Ji0QMujkR/DPi1    |            |
| $1$yi$FS7W5j1RJmbRHDe0El/zX/          |            |
| $1$yi$MiivC6pLdwS0zp0pa0cUq1          | qw1234qw   |
| $Dg.cUjtWGTIVkuFS0ZYbN1               | fx1805     |
| $enWsv2cbxPCrd0WeXUXtX0               | nobody     |
| $qZV4X6DTqMHUDIyZG.8PH.               |            |
| $z2VkRbfNoE/xHLBj8i2cv.               | ftp        |
| 7wtxBdUGBnuoY                         | runtop10   |
| 9B60FC59706134759DBCAEA58CAF9068      | Fireitup   |
| LHjQopX4yjf1Q                         | ls123      |
| ab8nBoH3mb8.g                         | helpme     |
| absxcfbgXtb3o                         | xc3511     |
| xt5USRjG7rEDE                         | j1/_7sxw   |
| $1$EmcmB/9a$UrsXTlmYL/6eZ9A2ST2Yl/    |            |
| $1$soidjfoi$9klIbmCLq2JjYwKfEA5rH1    |            |
```

### 劫持默认密码
> _在 Goke 上测试_

Over the UART interface, it is possible to temporarily interrupt the normal
booting sequence and drop into a limited Linux shell at early stage of
system startup.
```
setenv bootargs ${bootargs} single init=/bin/sh
boot
```
This shell won't load the full working system, so you have to amend it manually.
First, mount `/rom` filesystem:
```
mount -t jffs2 /dev/mtdblock3 /rom
```
Mount the rest of mounting points from `/etc/fstab`:
```
mount -a
```
Also mount the SD card to copy files to and from:
```
mount /dev/mmcblk0p1 on /mnt/s0
```
通过 UART 接口，可以暂时中断正常启动顺序，并在系统启动初期进入受限的 Linux shell。此 shell 不会加载完整的工作系统，因此您必须手动修改它。首先，挂载 `/rom` 文件系统：从 `/etc/fstab` 挂载其余挂载点：还要挂载 SD 卡以复制文件：在 `/rom` 文件系统上，您可以编辑 `/room/etc/passwd` 文件，但一旦设备重新启动，它将被重置为默认值。发生这种情况是因为每次启动时都有一个引导 bin 文件重新创建 `passwd` 文件，所以我们需要修改该可执行文件。

Copy `system.dat` to an SD card:
```
cp /rom/system.dat /mnt/s0
```
On a linux computer, unpack `system.dat` file using `unsquashfs`:
```
mkdir squashfs-temp
cd squashfs-temp
unsquashfs system.dat
```
将"system.dat"复制到 SD 卡：在 Linux 计算机上，使用"unsquashfs"解压"system.dat"文件：找到指南文件并在十六进制编辑器中编辑其内容，以修改每次重启时写入密码的文件的名称。搜索"/etc/passwd"并将其名称中的字母更改为其他字母，例如"/etc/passwT"。

使用 `mksquashfs` 打包 squash 文件系统：

```bash
mksquashfs ./squashfs-root ./file -comp xz -no-xattrs -noappend -no-exports -all-root -quiet -b 131072
```
并将其从 SD 卡复制回相机上的"/rom"目录。

现在您可以用自己的密码替换"/rom/etc/passwd"中的密码，当您重新启动设备时，您将拥有使用自己密码的完整工作系统。


### 软件

- [Hashcat](https://hashcat.net/)
- [John The Ripper](https://www.openwall.com/john/)
- [Hydra](https://github.com/vanhauser-thc/thc-hydra)

[1]: https://en.wikipedia.org/wiki/Rainbow_table
[2]: https://en.wikipedia.org/wiki/Brute-force_attack


-------------------------------------------------- -

