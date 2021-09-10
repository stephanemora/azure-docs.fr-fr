---
title: Sauvegarde et restauration du système d’exploitation pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Découvrez comment effectuer la sauvegarde et la restauration du système d’exploitation pour SAP HANA sur Azure (grandes instances).
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
ms.date: 06/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5685f7932b49f8af57faf159a51a8cb634128337
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217482"
---
# <a name="os-backup-and-restore"></a>Sauvegarde et restauration du système d’exploitation

Cet article décrit les étapes à suivre pour effectuer une sauvegarde et une restauration au niveau des fichiers du système d’exploitation. La procédure diffère en fonction de paramètres tels que Type I ou Type II, Révision 3 ou ultérieure, l’emplacement, etc. Vérifiez auprès de Microsoft Operations pour obtenir les valeurs de ces paramètres pour vos ressources.

## <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Sauvegarde et restauration du système d’exploitation pour les références (SKU) de type II avec révision 3

Les informations ci-dessous décrivent les étapes à suivre pour effectuer une sauvegarde et une restauration au niveau des fichiers du système d’exploitation pour les **références SKU Type II** de HANA (grandes instances) Révision 3.

>[!Important]
> **Cet article ne s’applique pas aux déploiements de références SKU de type II dans les tampons de grande instance HANA de révision 4.** Les numéros d’unité logique de démarrage de HANA (grandes instances) Type II déployés dans les tampons HANA (grandes instances) Révision 4 peuvent être sauvegardés à l’aide d’instantanés de stockage, ce qui est vrai pour les références SKU Type I déjà dans les tampons de la révision 3.


>[!NOTE]
>Les scripts de sauvegarde du système d’exploitation utilisent le logiciel ReaR, qui est préinstallé sur le serveur.  

Une fois l’approvisionnement terminé par l’équipe Microsoft Service Management, le serveur est configuré par défaut avec deux planifications pour sauvegarder le système d’exploitation au niveau des fichiers. Vous pouvez vérifier la planification des tâches de sauvegarde avec la commande suivante :

```
#crontab –l
```
Vous pouvez modifier la planification de la sauvegarde à tout moment à l’aide de la commande suivante :
```
#crontab -e
```
### <a name="take-a-manual-backup"></a>Exécuter une sauvegarde manuelle

La sauvegarde du système d’exploitation est d’ores et déjà planifiée avec une **tâche Cron**. Toutefois, vous pouvez aussi effectuer la sauvegarde du système d’exploitation au niveau des fichiers manuellement. Pour effectuer une sauvegarde manuelle, exécutez la commande suivante :

```
#rear -v mkbackup
```
La capture d’écran suivante montre l’exemple d’une sauvegarde manuelle :

![procédure](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


### <a name="restore-a-backup"></a>Restaurer une sauvegarde

Vous pouvez restaurer une sauvegarde complète ou un fichier individuel à partir d’une sauvegarde. Pour effectuer la restauration, utilisez la commande suivante :

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Après la restauration, le fichier est récupéré dans le répertoire de travail actuel.

La commande suivante illustre la restauration du fichier */etc/fstab* à partir du fichier de sauvegarde *backup.tar.gz* :
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Vous devez copier le fichier dans l’emplacement souhaité après sa restauration à partir de la sauvegarde.

La capture d’écran suivante illustre la restauration d’une sauvegarde complète.

![Capture d'écran représentant une fenêtre d'invite de commandes avec la restauration.](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

### <a name="install-the-rear-tool-and-change-the-configuration"></a>Installer l’outil ReaR et modifier la configuration 

Les packages ReaR (Relax-and-Recover) sont **préinstallés** dans les **références SKU Type II** de HANA (grandes instances). Aucune action n’est nécessaire de votre côté. Vous pouvez directement commencer à utiliser l’outil ReaR pour la sauvegarde du système d’exploitation.

Toutefois, dans le cas où vous devez installer vous-même les packages, vous pouvez utiliser les étapes suivantes pour installer et configurer l’outil ReaR.

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

Les informations ci-dessous décrivent les étapes à suivre pour effectuer une sauvegarde et une restauration au niveau des fichiers du système d’exploitation pour toutes les références SKU de toutes les révisions, à l’exception des **références SKU Type II** de HANA (grandes instances) Révision 3.

### <a name="take-a-manual-backup"></a>Exécuter une sauvegarde manuelle

Obtenez la version la plus récente de Microsoft Snapshot Tools pour SAP HANA comme expliqué dans une série d’articles commençant par [Qu’est-ce que l’outil Azure Application Consistent Snapshot Tool ?](../../../azure-netapp-files/azacsnap-introduction.md) et configurez et testez-les comme décrit dans ces articles :

- [Configurer l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [Tester l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-test.md) 

à exécuter régulièrement via `crontab` comme décrit dans [Effectuer une sauvegarde à l’aide de l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md). 

Pour plus d’informations, consultez ces références :

- [Installer l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-installation.md)
- [Configurer l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [Tester l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-test.md)
- [Effectuer une sauvegarde à l’aide de l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)
- [Obtenir des détails avec l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-details.md)
- [Supprimer avec l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-delete.md)
- [Restaurer avec l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-restore.md)
- [Récupération d’urgence avec l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-disaster-recovery.md)
- [Dépanner l'outil Azure Application Consistent Snapshot](../../../azure-netapp-files/azacsnap-troubleshoot.md)
- [Conseils et astuces pour utiliser l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-tips.md)


### <a name="restore-a-backup"></a>Restaurer une sauvegarde

L’opération de restauration ne peut pas être effectuée à partir du système d’exploitation lui-même. Vous devez adresser un ticket de support à Microsoft Operations. L’opération de restauration nécessite que l’instance HANA (grandes instances) [HLI] soit hors tension, planifiez donc en conséquence.

### <a name="managed-os-snapshots"></a>Instantanés du système d’exploitation managés

Azure peut effectuer automatiquement des sauvegardes du système d’exploitation pour vos ressources HLI. Ces sauvegardes sont effectuées une fois par jour, et Azure conserve jusqu’aux trois dernières sauvegardes. Ces sauvegardes sont activées par défaut pour tous les clients des régions suivantes :
- USA Ouest
- Australie Est
- Sud-Australie Est
- États-Unis - partie centrale méridionale
- USA Est 2

Cette fonction est partiellement disponible dans les régions suivantes :
- USA Est
- Europe Nord
- Europe Ouest

La fréquence ou la période de rétention des sauvegardes effectuées par cette fonction ne peut pas être modifiée. Si une stratégie différente de sauvegarde du système d’exploitation est nécessaire pour vos ressources HLI, vous pouvez choisir de refuser cette fonction en créant un ticket de support auprès de Microsoft Operations. Configurez ensuite Microsoft Snapshot Tools pour SAP HANA afin d’effectuer des sauvegardes du système d’exploitation à l’aide des instructions fournies précédemment dans la section [Exécuter une sauvegarde manuelle](#take-a-manual-backup).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment activer kdump pour HANA (grandes instances).

> [!div class="nextstepaction"]
> [kdump pour SAP HANA sur Azure (grandes instances)](hana-large-instance-enable-kdump.md)
