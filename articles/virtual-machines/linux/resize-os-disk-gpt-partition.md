---
title: Redimensionner un disque de système d’exploitation ayant une partition GPT | Microsoft Docs
description: Cet article fournit des instructions sur le redimensionnement d’un disque de système d’exploitation ayant une partition GPT.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 99b723322ce7636edce3ae5b59a69b96e288ca24
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392688"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Redimensionner un disque de système d’exploitation ayant une partition GPT

> [!NOTE]
> Ce scénario s’applique uniquement aux disques de système d’exploitation ayant une partition GPT (table de partition GUID).

Cet article explique comment augmenter la taille d’un disque de système d’exploitation ayant une partition GPT dans Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Déterminer si le disque du système d’exploitation a une partition MBR ou GPT

Pour déterminer si la partition de disque a été créée à l’aide d’une partition d’enregistrement de démarrage principal (MBR) ou d’une partition GPT, utilisez la commande `parted`.

### <a name="mbr-partition"></a>Partition MBR

Dans la sortie suivante, **Partition Table** affiche une valeur de **msdos**. Cette valeur identifie une partition MBR.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>Partition GPT

Dans la sortie suivante, **Partition Table** affiche la valeur de **gpt**. Cette valeur identifie une partition GPT.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Si votre machine virtuelle a une partition GPT sur votre disque de système d’exploitation, augmentez la taille du disque du système d’exploitation.

## <a name="increase-the-size-of-the-os-disk"></a>Augmenter la taille du disque du système d’exploitation

Les instructions suivantes s’appliquent aux distributions approuvées par Linux.

> [!NOTE]
> Avant de continuer, effectuez une copie de sauvegarde de votre machine virtuelle, ou prenez un instantané de votre disque de système d’exploitation.

### <a name="ubuntu"></a>Ubuntu

Pour augmenter la taille du disque du système d’exploitation dans Ubuntu 16.x et 18.x :

1. Arrêtez la machine virtuelle.
1. Augmentez la taille du disque du système d’exploitation à partir du portail.
1. Redémarrez la machine virtuelle, puis connectez-vous à la machine virtuelle en tant qu'utilisateur **racine**.
1. Vérifiez que le disque du système d’exploitation affiche désormais une taille de système de fichiers accrue.

Comme indiqué dans l’exemple suivant, le disque du système d’exploitation a été redimensionné à 100 Go à partir du portail. Le système de fichiers **/dev/sda1** monté sur **/** affiche désormais 97 Go.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

Pour augmenter la taille du disque du système d’exploitation dans SUSE 12 SP4, SUSE SLES 12 pour SAP, SUSE SLES 15 et SUSE SLES 15 pour SAP :

1. Arrêtez la machine virtuelle.
1. Augmentez la taille du disque du système d’exploitation à partir du portail.
1. Redémarrez la machine virtuelle.

Une fois que la machine virtuelle a redémarré, procédez comme suit :

1. Accédez à votre machine virtuelle en tant qu’utilisateur **racine** à l’aide de la commande suivante :

   ```
   # sudo -i
   ```

1. Utilisez la commande suivante pour installer le package **growpart** qui sera utilisé pour redimensionner la partition :

   ```
   # zypper install growpart
   ```

1. Utilisez la commande `lsblk` pour rechercher la partition montée sur la racine du système de fichiers (« / »). Dans ce cas, nous voyons que la partition 4 du d’appareil est montée sur/ :

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Redimensionnez la partition requise à l’aide de la commande `growpart` en utilisant le numéro de partition trouvé à l’étape précédente.

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Exécutez de nouveau la commande `lsblk` pour vérifier si la partition a été augmentée.

   La sortie suivante montre que la partition **/dev/sda4** a été redimensionnée à 46.5 Go :
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Identifiez le type de système de fichiers sur le disque du système d’exploitation à l’aide de la commande `lsblk` avec l’indicateur `-f` :

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. Selon le type de système de fichiers, utilisez les commandes appropriées pour le redimensionner.
   
   Pour **xfs**, utilisez la commande suivante :
   
   ```
   #xfs_growfs /
   ```
   
   Exemple de sortie :
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Pour **ext4**, utilisez la commande suivante :
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Vérifiez la taille du système de fichiers augmentée pour **df -Th** à l’aide de la commande suivante :
   
   ```
   #df -Thl
   ```
   
   Exemple de sortie :
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   Dans l’exemple précédent, nous pouvons voir que la taille du système de fichiers pour le disque du système d’exploitation a été augmentée.

### <a name="rhel-with-lvm"></a>RHEL avec LVM

1. Accédez à votre machine virtuelle en tant qu’utilisateur **racine** à l’aide de la commande suivante :

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Utilisez la commande `lsblk` pour rechercher le volume logique (LV) monté sur la racine du système de fichiers (« / »). Dans ce cas, nous voyons que **_rootvg-rootlv_ *_ est monté sur _* /** .  Si vous souhaitez un autre système de fichiers, remplacez le volume logique et le point de montage dans ce document.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. Vérifiez s’il y a de l’espace libre dans le groupe de volumes LVM contenant la partition racine.  S’il y a de l’espace libre, passez à l’étape **12**.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   Dans cet exemple, la ligne **Free PE / Size** indique qu’il existe 38,02 Go d’espace libre dans le groupe de volumes.  Aucun redimensionnement de disque n’est requis avant l’ajout d’espace au groupe de volumes.

1. Pour augmenter la taille du disque du système d’exploitation dans RHEL 7.x avec LVM :

   1. Arrêtez la machine virtuelle.
   1. Augmentez la taille du disque du système d’exploitation à partir du portail.
   1. Démarrez la machine virtuelle.

1. Une fois que la machine virtuelle a redémarré, procédez comme suit :

   1. Installez le package **cloud-utils-growpart** pour fournir la commande **growpart**, qui est nécessaire pour augmenter la taille du disque du système d’exploitation.

      Ce package est préinstallé sur la plupart des images de Place de marché Azure.

      ```bash
      [root@dd-rhel7vm ~]# yum install cloud-utils-growpart
      ```

1. Déterminez le disque et la partition contenant le ou les volumes physiques (PV) LVM dans le groupe de volumes (VG) nommé rootvg à l’aide de la commande **pvscan**.  Prenez note de la taille et de l’espace libre figurant entre les crochets **[]** .

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Vérifiez la taille de la partition avec **lsblk**.  Voir ci-dessous. 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Augmentez la partition contenant ce PV en utilisant **growpart**, le nom de l’appareil et le numéro de partition.  Cela augmentera la partition spécifiée pour utiliser tout l’espace contigu libre sur l’appareil.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. Vérifiez que la partition a été redimensionnée à la taille attendue à l’aide de la commande **lsblk**.  Notez que, dans l’exemple sda4, 63G a été remplacé par 95G.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Augmentez le PV pour utiliser le reste de la partition récemment développée.

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. Vérifiez que la nouvelle taille du PV correspond à la taille attendue, en comparant les valeurs **[taille / libre]** d’origine.

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. Augmentez le volume logique souhaité de la quantité désirée, qui ne doit pas nécessairement être tout l’espace libre dans le groupe de volumes.  Dans l’exemple suivant, **/dev/mapper/rootvg-rootlv** est redimensionné de 2 Go à 12 Go (soit une augmentation de 10 Go) à l’aide de la commande suivante. Cette commande redimensionne également le système de fichiers.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Exemple de sortie :

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```

1. La commande lvresize appelle automatiquement la commande de redimensionnement appropriée pour le système de fichiers dans le volume logique. Vérifiez si **/dev/mapper/rootvg-rootlv**, qui est monté sur **/** , a une taille de système de fichiers augmentée à l’aide de la commande suivante :

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   Exemple de sortie :

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> Afin d’utiliser la même procédure pour redimensionner tout autre volume logique, modifiez le nom **lv** à l’étape **12**.

### <a name="rhel-raw"></a>RHEL RAW
>[!NOTE]
>Prenez toujours un instantané de la machine virtuelle avant d’agrandir la taille du disque du système d’exploitation.

Pour augmenter la taille du disque du système d’exploitation dans RHEL avec une partition RAW :

Arrêtez la machine virtuelle.
Augmentez la taille du disque du système d’exploitation à partir du portail.
Démarrez la machine virtuelle.
Une fois que la machine virtuelle a redémarré, procédez comme suit :

1. Accédez à votre machine virtuelle en tant qu’utilisateur **racine** à l’aide de la commande suivante :
 
   ```
   sudo su
   ```

1. Installez le package **gptfdisk** requis pour augmenter la taille du disque du système d’exploitation.

   ```
   yum install gdisk -y
   ```

1.  Pour voir tous les secteurs disponibles sur le disque, exécutez la commande suivante :
    ```
    gdisk -l /dev/sda
    ```

1. Vous verrez les détails indiquant le type de partition. Assurez-vous qu’il s’agit d’une partition GPT. Identifiez la partition racine. Ne modifiez pas ou ne supprimez pas la partition de démarrage (partition de démarrage du BIOS) et la partition système (« partition système EFI »)

1. Utilisez la commande suivante pour démarrer le partitionnement pour la première fois. 
    ```
    gdisk /dev/sda
    ```

1. Un message devrait s’afficher, vous demandant de confirmer la prochaine commande ('Command: ? for help'). 

   ```
   w
   ```

1. Vous recevrez un avertissement indiquant « Warning! Secondary header is placed too early on the disk! Do you want to correct this problem? (Y/N): ». Vous devez appuyer sur « Y » (Oui)

   ```
   Y
   ```

1. Vous devriez voir un message indiquant que les contrôles finaux sont terminés et demandant une confirmation. Appuyez sur « Y » (Oui)

   ```
   Y
   ```

1. Vérifiez si tout s’est correctement déroulé à l’aide de la commande partprobe

   ```
   partprobe
   ```

1. Les étapes ci-dessus ont permis de s’assurer que l’en-tête GPT secondaire est placé à la fin. La prochaine étape consiste à démarrer le processus de redimensionnement à l’aide de l’outil gdisk. Utilisez la commande suivante.

   ```
   gdisk /dev/sda
   ```
1. Dans le menu de commande, appuyez sur « p » pour afficher la liste des partitions. Identifiez la partition racine (dans les étapes, sda2 est considéré comme la partition racine) et la partition de démarrage (dans les étapes, sda3 est considéré comme la partition de démarrage) 

   ```
   p
   ```
    ![Partition racine et partition de démarrage](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. Appuyez sur « d »pour supprimer la partition et sélectionnez le numéro de partition affecté au démarrage (dans cet exemple, il s’agit de « 3 »)
   ```
   d
   3
   ```
1. Appuyez sur « d »pour supprimer la partition et sélectionnez le numéro de partition affecté au démarrage (dans cet exemple, il s’agit de « 2 »)
   ```
   d
   2
   ```
    ![Supprimer la partition racine et la partition de démarrage](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. Pour recréer la partition racine avec une taille augmentée, appuyez sur « n », entrez le numéro de la partition précédemment supprimée pour la racine (« 2 » dans cet exemple), choisissez le premier secteur comme « valeur par défaut », le dernier secteur comme « valeur du dernier secteur - secteur de démarrage » (dans ce cas, la valeur « 4096 » correspond à un secteur de démarrage de 2 Mo), puis le code hexadécimal « 8300 ».
   ```
   n
   2
   (Enter default)
   (Calculateed value of Last sector value - 4096)
   8300
   ```
1. Pour recréer une partition de démarrage, appuyez sur « n », entrez le numéro de la partition précédemment supprimée pour le démarrage (« 3 » dans cet exemple), choisissez le premier secteur comme « valeur par défaut », le dernier secteur comme « valeur par défaut », puis le code hexadécimal « EF02 ».
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. Inscrivez les modifications à l’aide de la commande « w » et appuyez sur « Y » pour confirmer
   ```
   w
   Y
   ```
1. Exécutez la commande « partprobe » pour vérifier la stabilité du disque
   ```
   partprobe
   ```
1. Redémarrez la machine virtuelle : la taille de la partition racine aura augmentée
   ```
   reboot
   ```

   ![Nouvelle partition racine et partition de démarrage](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. Exécutez la commande xfs_growfs sur la partition pour la redimensionner
   ```
   xfs_growfs /dev/sda2
   ```

   ![XFS Grow FS](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)

## <a name="next-steps"></a>Étapes suivantes

- [Redimensionner le disque](expand-disks.md)
