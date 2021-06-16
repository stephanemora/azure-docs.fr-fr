---
title: Sauvegarde et restauration du système d’exploitation pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Effectuer la sauvegarde et la restauration du système d’exploitation pour SAP HANA sur Azure (grandes instances)
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7cd7210ceec1297431ab5929d2733c8b4d164d71
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110575251"
---
# <a name="os-backup-and-restore"></a>Sauvegarde et restauration du système d’exploitation

Ce document décrit les étapes permettant d’effectuer une sauvegarde et une restauration au niveau des fichiers du système d’exploitation. La procédure diffère selon certains paramètres, tels que le type I ou II, la révision 3 ou ultérieure, l’emplacement, etc. Veuillez vérifier auprès du service des opérations de Microsoft pour obtenir des valeurs précises pour ces paramètres pour vos ressources.

## <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Sauvegarde et restauration du système d’exploitation pour les références (SKU) de type II avec révision 3

Les informations ci-dessous décrivent les étapes permettant d’effectuer une sauvegarde et une restauration au niveau des fichiers du système d’exploitation pour les **références SKU de type II** des grandes instances HANA de la révision 3.

>[!Important]
> **Cet article ne s’applique pas aux déploiements de références SKU de type II dans les tampons de grande instance HANA de révision 4.** Les numéros d’unité logique de démarrage des unités de grande instance HANA de type II déployées dans les tampons de grande instance HANA de révision 4 peuvent être sauvegardés avec des captures instantanées de stockage, comme c’est le cas avec les références SKU de type I déjà dans les tampons de révision 3.


>[!NOTE]
>Les scripts de sauvegarde du système d’exploitation utilisent le logiciel ReaR, qui est préinstallé sur le serveur.  

Une fois l’approvisionnement effectué par l’équipe de gestion `Service Management` Microsoft, le serveur est par défaut configuré avec une planification de deux sauvegardes pour préserver la sauvegarde du système de fichiers du système d’exploitation. Vous pouvez vérifier la planification des tâches de sauvegarde avec la commande suivante :
```
#crontab –l
```
Vous pouvez modifier la planification de la sauvegarde à tout moment avec la commande suivante :
```
#crontab -e
```
### <a name="how-to-take-a-manual-backup"></a>Comment effectuer une sauvegarde manuelle ?

La sauvegarde du système d’exploitation est d’ores et déjà planifiée avec une **tâche Cron**. Toutefois, vous pouvez aussi effectuer la sauvegarde du système de fichiers du système d’exploitation manuellement. Pour effectuer une sauvegarde manuelle, exécutez la commande suivante :

```
#rear -v mkbackup
```
La capture d’écran suivante montre l’exemple d’une sauvegarde manuelle :

![procédure](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


### <a name="how-to-restore-a-backup"></a>Comment restaurer une sauvegarde ?

Vous pouvez restaurer une sauvegarde complète ou un fichier individuel à partir de la sauvegarde. Pour effectuer la restauration, utilisez la commande suivante :

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Après la restauration, le fichier est récupéré dans le répertoire de travail actuel.

La commande suivante illustre la restauration d’un fichier */etc/fstab* à partir du fichier de sauvegarde *backup.tar.gz*.
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Vous devez copier le fichier dans l’emplacement souhaité après sa restauration à partir de la sauvegarde.

La capture d’écran suivante illustre la restauration d’une sauvegarde complète :

![Capture d'écran représentant une fenêtre d'invite de commandes avec la restauration.](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

### <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Comment installer l’outil ReaR et modifier la configuration ? 

Les packages ReaR (Relax-and-Recover) sont **préinstallés** dans les **références (SKU) de type II** des grandes instances HANA, et aucune action de votre part n’est nécessaire. Vous pouvez commencer directement à utiliser ReaR pour la sauvegarde du système d’exploitation.
Toutefois, dans le cas où vous devez installer vous-même les packages, vous pouvez suivre les étapes répertoriées pour installer et configurer l’outil ReaR.

Pour installer les packages de sauvegarde **ReaR**, utilisez les commandes suivantes :

Pour le système d’exploitation **SLES**, utilisez la commande suivante :
```
#zypper install <rear rpm package>
```
Pour le système d’exploitation **RHEL**, utilisez la commande suivante : 
```
#yum install rear -y
```
Pour configurer l’outil ReaR, vous devez mettre à jour les paramètres **OUTPUT_URL** et **BACKUP_URL** dans le *fichier /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

La capture d’écran suivante illustre la restauration d’une sauvegarde complète : ![Capture d'écran représentant une fenêtre d'invite de commandes avec la restauration à l'aide de l'outil ReaR.](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)


## <a name="os-backup-and-restore-for-all-other-skus"></a>Sauvegarde et restauration du système d’exploitation pour les autres références SKU

Les informations ci-dessous décrivent les étapes permettant d’effectuer une sauvegarde et une restauration au niveau des fichiers du système d’exploitation pour toutes les références SKU de toutes les révisions, à l’exception des **références SKU de type II** des grandes instances HANA de la révision 3.

### <a name="how-to-take-a-manual-backup"></a>Comment effectuer une sauvegarde manuelle ?

Obtenez la version la plus récente de Microsoft Snapshot Tools pour SAP HANA à partir de [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) et configurez-la pour qu’elle s’exécute régulièrement via `crontab` avec l’indicateur `--type=boot`. Cela garantit des sauvegardes régulières du système d’exploitation. L’exemple suivant montre une planification de cron dans `/etc/crontab` pour une sauvegarde du système d’exploitation de la référence SKU de type I :

```
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```

L’exemple suivant montre une planification de cron dans `/etc/crontab` pour une sauvegarde du système d’exploitation de la référence SKU de type II :

```
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Références supplémentaires :
- [Configuration des captures instantanées de stockage](hana-backup-restore.md#set-up-storage-snapshots)
- Guide de Microsoft Snapshot Tools pour SAP HANA sur [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md).

### <a name="how-to-restore-a-backup"></a>Comment restaurer une sauvegarde ?

L’opération de restauration ne peut pas être effectuée à partir du système d’exploitation lui-même. Pour ce faire, vous devez adresser un ticket de support au service des opérations de Microsoft. L’opération de restauration nécessite que l’instance HLI soit hors tension, planifiez donc en conséquence.

### <a name="managed-os-snapshots"></a>Gestion des instantanés du système d’exploitation

Azure peut effectuer automatiquement des sauvegardes du système d’exploitation pour vos ressources HLI. Ces sauvegardes sont effectuées une fois par jour, et Azure conserve jusqu’aux trois dernières sauvegardes. Cette option est activée par défaut pour tous les clients dans les régions suivantes :
- USA Ouest
- Australie Est
- Sud-Australie Est
- États-Unis - partie centrale méridionale
- USA Est 2

Cette fonction est partiellement disponible dans les régions suivantes :
- USA Est
- Europe Nord
- Europe Ouest

La fréquence ou la période de rétention des sauvegardes effectuées par cette fonction ne peut pas être modifiée. Si une autre stratégie de sauvegarde du système d’exploitation est nécessaire pour vos ressources HLI, vous pouvez choisir de refuser cette fonction en adressant un ticket de support au service des opérations de Microsoft, puis configurer Microsoft Snapshot Tools pour SAP HANA afin d’effectuer des sauvegardes du système d’exploitation à l’aide des instructions fournies dans la section précédente de ce document.