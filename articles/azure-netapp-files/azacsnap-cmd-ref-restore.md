---
title: Restaurer avec l’outil Azure Application Consistent Snapshot Tool pour Azure NetApp Files | Microsoft Docs
description: Guide d’exécution de la commande restore de l’outil Azure Application Consistent Snapshot Tool, que vous pouvez utiliser avec Azure NetApp Files.
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 1c6b7ec6c4ef24ec00fbfc55a65a968e00561c2e
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632106"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>Restaurer avec l’outil Azure Application Consistent Snapshot Tool (préversion)

Cet article sert de guide pour exécuter la commande restore de l’outil Azure Application Consistent Snapshot Tool, que vous pouvez utiliser avec Azure NetApp Files.

## <a name="introduction"></a>Introduction

Pour effectuer une restauration de volume à partir d’un instantané, utilisez la commande `azacsnap -c restore`.

> [!IMPORTANT]
> Cela n’entraîne pas une récupération de base de données, mais seulement une restauration de volume(s), comme indiqué pour chacune des options ci-dessous.

## <a name="command-options"></a>Options de commande

La commande `-c restore` comporte les options suivantes :

- `--restore snaptovol` crée un volume basé sur le dernier instantané sur le volume cible. Cette commande crée un volume « cloné » basé sur le volume cible configuré. Elle utilise le dernier instantané de volume en tant que base de création du volume.  Cette commande n’interrompt pas la réplication du stockage entre le site principal et le site secondaire. À la place, des clones du dernier instantané disponible sont créés sur le site DR, et les points de montage de système de fichiers recommandés des volumes clonés sont présentés. Cette commande doit être exécutée sur le système Azure (grande instance) **dans la région DR**, en d’autres termes, le système de basculement cible.

- `--restore revertvolume` rétablit le volume cible à un état antérieur en fonction de l’instantané le plus récent.  Cette commande est utilisée dans le cadre du basculement DR dans la région DR associée. Cette commande **arrête** la réplication du stockage du site principal au site secondaire. De plus, elle restaure le ou les volumes DR cibles en fonction de leur dernier instantané disponible sur les volumes DR ainsi que les points de montage de système de fichiers recommandés pour les volumes DR restaurés. Cette commande doit être exécutée sur le système Azure (grande instance) **dans la région DR**, en d’autres termes, le système de basculement cible.
    > [!NOTE]
    > La sous-commande (`--restore revertvolume`) est uniquement disponible pour Azure (grande instance) et n’est pas disponible pour Azure NetApp Files.
- `--hanasid <SAP HANA SID>` est le SID SAP HANA sélectionné dans le fichier config auquel appliquer les commandes de restauration de volume.

- `[--configfile <config filename>]` est un paramètre facultatif qui autorise les noms de fichiers config personnalisés.

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>Effectuer un test de basculement DR `azacsnap -c restore --restore snaptovol`

Cette commande est similaire à la commande de basculement DR « complète » (`--restore restorevolume`), mais au lieu d’arrêter la réplication entre le site principal et le site de reprise d’activité, un volume de clonage est créé à partir des volumes de reprise d’activité, ce qui permet la restauration de l’instantané le plus récent sur le site DR. Ces volumes clonés sont ensuite utilisables par le client pour tester la reprise d’activité sans devoir exécuter un basculement complet de l’environnement HANA, qui arrête l’accord de réplication entre le site principal et le site de reprise d’activité.

- Plusieurs points de restauration différents peuvent être testés de cette manière, chacun avec son propre point de restauration.
- Le clone est désigné par l’horodatage au moment de l’exécution de la commande. Il représente les données les plus récentes et les autres instantanés disponibles au moment de l’exécution.

> [!IMPORTANT]
> Cette opération s’applique uniquement à Azure (grande instance).
>
> - Quand cette commande est exécutée, l’e-mail de contact est obligatoire pour permettre une liaison des opérations avant la suppression des clones au-delà de 4 semaines.
> - Chaque exécution de cette commande entraîne la création d’un clone qui doit être supprimé par l’équipe des opérations Microsoft à la fin du test.
> - Tout volume de clonage créé est automatiquement supprimé après 4 semaines.

Le fichier config (par exemple `DR.json`) doit contenir uniquement les volumes DR, et non les volumes de production. Sinon, des clones des volumes de production risquent d’être créés.

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>Sortie de la commande `azacsnap -c restore --restore snaptovol` (pour le scénario à un seul nœud)

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --hanasid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> La sortie « Displaying Mount Points by Volume » (Affichage des points de montage par volume) varie en fonction des différents scénarios.

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>Effectuer un basculement DR complet `azacsnap -c restore --restore revertvolume`

Cette commande **arrête** la réplication du stockage du site principal au site secondaire. De plus, elle restaure le dernier instantané sur les volumes DR et fournit les points de montage pour les volumes DR.

Vous devez IMPÉRATIVEMENT exécuter cette commande sur le serveur DR en utilisant un fichier config (par exemple `DR.json`) avec des volumes DR uniquement !

Effectuez un basculement vers le site DR en exécutant la commande `azacsnap -c restore --restore revertvolume`.  Cette commande nécessite l’ajout d’un SID en tant que paramètre. Il s’agit du SID de l’instance HANA, que vous devez récupérer sur le site DR.

> [!IMPORTANT]
> Exécutez cette commande uniquement si vous comptez effectuer l’exercice DR ou un test. Cette commande arrête la réplication. Vous devez contacter l’équipe des opérations Microsoft pour réactiver la réplication.

Voici globalement les étapes d’exécution d’un basculement DR :

- Vous devez arrêter l’instance HANA sur le site **principal**. Cette action est nécessaire uniquement si vous effectuez réellement le basculement vers le site DR pour éviter les incohérences de données.
- Arrêtez l’instance HANA sur le nœud DR pour le SID de production.
- Exécutez la commande `azacsnap -c restore --restore revertvolume` sur le nœud DR avec le SID à récupérer.
  - La commande rompt le lien de réplication du stockage entre le site principal et le site DR
  - La commande restaure les volumes « data » et « other », tel que cela est configuré.  En règle générale, cette opération concerne les volumes des systèmes de fichiers `/hana/data` et `/hana/logbackups`.  La commande ne récupère PAS le système de fichiers `/hana/shared`. Toutefois, elle utilise le `/hana/shared` existant pour le SID sur le site DR.
  - Montez les volumes `/hana/data` et `/hana/logbackups`, puis vérifiez qu’ils sont ajoutés au fichier `/etc/fstab`
- Restaurez l’instantané HANA SYSTEMDB. HANA Studio vous montre uniquement le dernier instantané HANA disponible sous l’instantané de stockage restauré dans le cadre de l’exécution de la commande d’instantané `azacsnap -c restore --restore revertvolume`.
- Récupérez la base de données de locataire.
- Démarrez l’instance HANA sur le site DR pour le SID de production (exemple : H80, en l’occurrence).
- Effectuez les tests de base de données.

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer une sauvegarde](azacsnap-cmd-ref-backup.md)
- [Obtenir les détails de l’instantané](azacsnap-cmd-ref-details.md)
