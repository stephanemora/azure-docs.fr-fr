---
title: Reprise d’activité avec l’outil Azure Application Consistent Snapshot Tool pour Azure NetApp Files | Microsoft Docs
description: Explique comment effectuer une reprise d’activité avec l’outil Azure Application Consistent Snapshot Tool, que vous pouvez utiliser avec Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 70e1823b30814d7dc29fef69215fcb53a2a2ab96
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730868"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>Reprise d’activité avec l’outil Azure Application Consistent Snapshot Tool (préversion)

Cet article explique comment effectuer une reprise d’activité avec l’outil Azure Application Consistent Snapshot Tool, que vous pouvez utiliser avec Azure NetApp Files.

> [!IMPORTANT]
> Cette opération s’applique uniquement aux systèmes **Azure (grande instance)** .

## <a name="introduction"></a>Introduction

La plateforme Azure (grande instance) peut également disposer d’un site de reprise d’activité configuré pour accueillir la réplication des instantanés de volume de stockage.  Si les instantanés ont été correctement configurés, il est possible d’effectuer une reprise d’activité sur ce site.  Ce document est destiné à vous aider à effectuer une reprise d’activité pour cette configuration.

## <a name="prerequisites-for-disaster-recovery-setup"></a>Prérequis de la configuration d’une reprise d’activité

Vous devez vérifier la conformité des prérequis suivants avant de planifier le basculement de reprise d’activité.

- Vous disposez d’un nœud DR provisionné sur le site DR. Il existe deux options pour un scénario DR. Le scénario DR normal et le scénario DR polyvalent.
- La réplication du stockage fonctionne. L’équipe des opérations Microsoft effectue la configuration de la réplication du stockage au moment du provisionnement automatique de la reprise d’activité DR. Vous pouvez superviser la réplication du stockage à l’aide de la commande `azacsnap -c details --details replication` sur le site DR.
- Vous avez créé et configuré des instantanés de stockage sur le site principal.
- Une instance HANA est installée sur le site DR pour le site principal. Elle utilise le même SID que celui de l’instance du site principal.
- Vous avez lu et compris la procédure de basculement DR décrite dans [Haute disponibilité et reprise d’activité de SAP HANA - Grandes instances sur Azure](../virtual-machines/workloads/sap/hana-failover-procedure.md)
- Vous avez créé et configuré des instantanés de stockage sur le site DR.
- Un fichier config (par exemple `DR.json`) a été créé avec les volumes de stockage DR et les informations associées sur le serveur DR.
- Vous avez effectué les étapes nécessaires sur le site DR pour :
  - Activer la communication avec le stockage.
  - Activer la communication avec SAP HANA.

## <a name="set-up-disaster-recovery"></a>Configurer une récupération d'urgence

Microsoft prend en charge la réplication au niveau du stockage pour la reprise d’activité DR. Il existe deux types de configuration de reprise d’activité DR.

La reprise d’activité **normale** et la reprise d’activité **polyvalente**. Dans le scénario DR **normal**, vous disposez d’une instance dédiée sur le site DR pour le basculement. Dans le scénario DR **polyvalent**, vous disposez d’une autre instance HANA pour l’AQ ou le développement, qui s’exécute dans l’unité de grande instance HANA sur le site DR. Toutefois, vous disposez également d’une instance HANA préinstallée, dormante, qui comporte un SID identique à celui de l’instance HANA dont vous souhaitez effectuer le basculement vers l’unité de grande instance HANA. L’équipe des opérations Microsoft configure l’environnement à votre place, notamment la réplication du stockage en fonction de l’entrée fournie dans le formulaire de demande de service au moment de l’intégration.

> [!IMPORTANT]
> Vérifiez que tous les prérequis sont remplis pour la configuration de la reprise d’activité DR.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>Superviser la réplication des données du site principal au site DR

L’équipe des opérations Microsoft gère et supervise déjà le lien DR entre le site principal et le site DR.
Vous pouvez superviser la réplication des données entre votre serveur primaire et le serveur DR à l’aide de la commande d’instantané `azacsnap -c details --details replication`.

## <a name="perform-a-failover-to-dr-site"></a>Effectuer un basculement vers le site DR

Exécutez la commande de basculement sur le site DR (`azacsnap -c restore --restore revertvolume`).

> [!IMPORTANT]
> La commande `azacsnap -c restore --restore revertvolume` interrompt la réplication du stockage entre le site de production et le site DR. Vous devez contacter l’équipe des opérations Microsoft pour reconfigurer la réplication. Une fois la réplication réactivée, toutes les données du stockage DR pour ce SID sont initialisées. La commande qui effectue le basculement met à disposition le dernier instantané de stockage répliqué. Si vous devez restaurer un instantané plus ancien, ouvrez une demande de support pour que l’équipe des opérations puisse vous aider à fournir un instantané antérieur restauré sur le site DR.

Voici globalement les étapes à suivre pour le basculement DR :

- Vous devez arrêter l’instance HANA sur le site **principal**. Cette action est nécessaire uniquement si vous effectuez réellement le basculement vers le site DR pour éviter les incohérences de données.
- Arrêtez l’instance HANA sur le nœud DR pour le SID de production.
- Exécutez la commande `azacsnap -c restore --restore revertvolume` sur le nœud DR avec le SID à récupérer
  - La commande rompt le lien de réplication du stockage entre le site principal et le site DR
  - La commande restaure uniquement le volume /data et le volume /logbackups mais PAS le volume /shared. Toutefois, elle utilise le volume /shared existant pour le SID sur le site DR.
  - Montez le volume /data et le volume /logbackups en veillant à les ajouter au fichier fstab
- Restaurez l’instantané HANA SYSTEMDB. HANA Studio vous montre uniquement le dernier instantané HANA disponible sous l’instantané de stockage restauré dans le cadre de l’exécution de la commande `azacsnap -c restore --restore revertvolume`.
- Récupérez la base de données de locataire.
- Démarrez l’instance HANA sur le site DR pour le SID de production (exemple : H80, en l’occurrence).
- Effectuez des tests.

### <a name="example-performing-disaster-recovery"></a>Exemple de reprise d’activité

Cette sous-section décrit les étapes détaillées d’un basculement vers le site de reprise d’activité.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>Étape 1 : Obtenir les détails du volume du nœud DR

Exécutez la commande `df –h` pour lister les systèmes de fichiers et les volumes associés auxquels se référer après le basculement.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>Étape 2 : Arrêter HANA sur le site principal

Si vous effectuez un basculement complet des charges de travail de production, et s’il est possible de se connecter au site de production principal, arrêtez la ou les instances de SAP HANA dont le basculement est effectué vers le site DR.

Ainsi, si vous êtes connecté en tant qu’utilisateur root, l’exemple suivant montre comment arrêter SAP HANA.  Remplacez <sid> par votre SID SAP HANA.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>Étape 3 : Arrêter HANA sur le site DR

Il est important d’arrêter SAP HANA sur le site DR avant de restaurer les volumes.

Ainsi, si vous êtes connecté en tant qu’utilisateur root, l’exemple suivant montre comment arrêter SAP HANA.  Remplacez <sid> par votre SID SAP HANA.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> Vérifiez que les instances HANA sur le site DR sont hors connexion avant de restaurer des volumes.

#### <a name="step-4-restore-the-volumes"></a>Étape 4 : Restaurer les volumes

```bash
azacsnap -c restore --restore revertvolume --hanasid H80
```

**_Sortie de la commande de basculement DR_**.

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --hanasid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> Vous devez suivre les étapes à la fin de l’affichage de la console pour préparer le stockage à un basculement DR.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>Étape 5 : Démonter les systèmes de fichiers inutiles

Exécutez la commande `umount` pour démonter les systèmes de fichiers/volumes qui ne sont pas nécessaires.

```bash
umount <Mount point>
```

Démontez les points de montage des données et des sauvegardes de fichiers journaux. Vous pouvez avoir plusieurs points de montage de données dans le scénario de scale-out.

#### <a name="step-6-configure-the-mount-points"></a>Étape 6 : Configurer les points de montage

Modifiez le fichier `/etc/fstab` pour commenter les entrées de données et de sauvegardes des fichiers journaux pour le SID principal (dans cet exemple, SID=H80), puis ajoutez les nouvelles entrées de point de montage créées à partir des volumes DR du site principal. Les nouvelles entrées de point de montage sont fournies dans la sortie de la commande.

- Commentez les points de montage existants exécutés sur le site DR avec le caractère `#` :

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- Ajoutez les lignes suivantes à `/etc/fstab`
  > cette sortie doit être la même que celle de la commande

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>Étape 7 : Monter les volumes de récupération

Exécutez la commande `mount –a` pour monter tous les points de montage.

```bash
mount -a
```

À présent, si vous exécutez `df –h` vous devez voir les volumes `*_dp` montés.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>Étape 8 : Récupérer SYSTEMDB

À partir d’HANA Studio, cliquez avec le bouton droit sur instance de SYSTEMDB, choisissez « Backup and Recovery » (Sauvegarde et récupération), puis « Recover System Database » (Récupérer la base de données système)

Consultez le guide pour récupérer une base de données à partir d’un instantané, en particulier SYSTEMDB.

#### <a name="step-9-recover-the-tenant-database"></a>Étape 9 : Récupérer la base de données de locataire

À partir d’HANA Studio, cliquez avec le bouton droit sur instance de SYSTEMDB, choisissez « Backup and Recovery » (Sauvegarde et récupération), puis « Recover Tenant Database » (Récupérer la base de données de locataire).

Consultez le guide pour récupérer une base de données à partir d’un instantané, en particulier la ou les bases de données de locataire.

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>Exécuter `azacsnap -c backup` sur le site DR

Si vous exécutez des sauvegardes basées sur des instantanés sur le site DR, le nom du serveur HANA configuré dans le fichier config `azacsnap` sur le site DR doit être identique au nom du serveur de production.

> [!IMPORTANT]
> L’exécution de `azacsnap -c backup` peut entraîner la création d’instantanés de stockage sur le site DR. Ceux-ci ne sont pas automatiquement répliqués vers un autre site.  Collaborez avec l’équipe des opérations Microsoft pour mieux comprendre comment restaurer les fichiers ou les données sur le site de production d’origine.

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir les détails de l’instantané](azacsnap-cmd-ref-details.md)
- [Effectuer une sauvegarde](azacsnap-cmd-ref-backup.md)
