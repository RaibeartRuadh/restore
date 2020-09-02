1. ������� � ������� ��� ������ ����������� ���������

������ � 1
��� �������� ������� ���������� �� ���������� ������ e (����������), ����� ������� � ���� ���������� �������� ����.
����� �������, ������� ���������� �� linux16 /vmlinuz
� ����� ������� �������� rw (��� ���� ����������� ���������� �������� � ������ ������/������) � init=/bin/bash
��� ��������� ������� � ������ bash. ������������� init=/bin/sh
������ Ctrl+x ��� ����������� �������� ����.
����� �������� � ����������� �������� ������ bash, ������ � ������, � ����� ���� � ����� root
�������� ����������� ����������� 1.png 2.png 3.png

������ � 2
��� �������� ������� ���������� �� ���������� ������ e (����������), ����� ������� � ���� ���������� �������� ����.
����� �������, ������� ���������� �� linux16 /vmlinuz
�� �������� ����� LANG ������� �������� rd.break
������ Ctrl+x ��� ����������� �������� ����.
�����, 
$ mount -o remount,rw /sysroot/
$ chroot /sysroot passwd root
����� ����� ������ ��� root
��� ������������� SELinux ��������� ���� .autorelabel
$touch /.autorelabel
����� ���� ����� ������������ ��������� autorelabeling
���������: �� ����� � �������, ������� ������ ��� root ��������� rd.break � chroot
�������� ����������� ����������� 4.png 5.png 6.png 9.png

������ � 3
��� �������� ������� ���������� �� ���������� ������ e (����������), ����� ������� � ���� ���������� �������� ����.
����� �������, ������� ���������� �� linux16 /vmlinuz
������ �������� ro �� rw � ���������� ����� ������ init=/sysroot/bin/sh
������ Ctrl+x ��� ����������� �������� ����.
������ � ��������� ����� ����������. 
�����? ������ � ����� chroot � ������ ������ root:
$ chroot /sysroot
$ passwd root
��� ������������� SELinux ��������� ���� .autorelabel
$ touch /.autorelabel
����� ���� ����� ������������ ��������� autorelabeling
��������� reboot
���������: �� ����� � �������, ������� ������ ��� root ��������� init=/sysroot/bin/sh � chroot
�������� ����������� ����������� 10.png 11.png

2. ���������� ������� � LVM, ����� ���� ������������� VG

��������� Vagrantfile � ����������� LVM
����� ����������� ������� �������

$ sudo vgs
������������ �������� � ������� �����. � ���� ������
VolGroup00

��������� ��������������
$ sudo vgrename VolGroup00 NewName
������� ���������� ��������� �� �������� �������������� ������
�������� 12.png


���������� ��������� � ������ ����������� �����

/etc/fstab  �����. 13.png
/etc/default/grub   14.png
/boot/grub2/grub.cfg   15.png

����� �������� ������ �������� VolGroup00 �� ����� NewName

����������� initrd image, ����� �� ���� ����� �������� Volume Group:

$ mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
����������� ��� ���� �������� ������ ����� ����������� �������������

����� 16.png

��������� ������������ ������� �, ���� �� ���� ��������� ���������, �� �������� ����� �������� Volume Groiup  ��� ������� �������� vgs
����� 17.png

�������� � ��������� ����������� ����������� � ������� 
- fstab
- grub
- grub.cfg

3. �������� ������ � initrd

������ ���������� � �������� /usr/lib/dracut/modules.d/

$ mkdir /usr/lib/dracut/modules.d/01test

������� ���� 

module-setup.sh

� ����������:
#!/bin/bash

check() {
    return 0
}
depends() {
    return 0
}
install() {
    inst_hook cleanup 00 "${moddir}/test.sh"
}


� ����:
test.sh

� ����������
#!/bin/bash

exec 0<>/dev/console 1<>/dev/console 2<>/dev/console
cat <<'msgend'

Wassup! 
< I'm dracut module >
 -------------------
(\__/)
(='.'=)
(").|.(")

msgend
sleep 20
echo " continuing...."


�������� ����� ��� �����������

sudo chmod +x /usr/lib/dracut/modules.d/test/init.sh /usr/lib/dracut/modules.d/test/logo.sh

������������ ������ initrd
mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)

����� ���������� ����������� /boot/grub2/grub.cfg �������� �����
rghb � quiet, ����� ������� ����� ��� ��������

����� 22.png




