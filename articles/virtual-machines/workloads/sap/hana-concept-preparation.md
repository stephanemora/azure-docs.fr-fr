---
title: Principes de récupération d’urgence et de préparation sur SAP HANA sur Azure (grandes Instances) | Microsoft Docs
description: Familiarisez-vous avec les principes de la récupération d’urgence et sa préparation sur SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79991bcdd5174d2655f3c917d0898b0b87b0ca50
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217068"
---
# <a name="disaster-recovery-principles-and-preparation"></a>Principes et préparation de la récupération d’urgence

Dans cet article, nous aborderons les principes importants de la récupération d’urgence (DR) pour les grandes instances HANA (également connues sous le nom de BareMetal Infrastructure). Nous détaillerons les différentes étapes à suivre pour préparer la récupération d’urgence. Nous verrons également comment atteindre l’objectif de délai de récupération (RTO) et l’objectif de point de récupération (RPO) fixés. 

## <a name="dr-principles-for-hana-large-instances"></a>Principes de la récupération d’urgence (DR) pour les grandes instances HANA

Les grandes instances HANA offrent une fonctionnalité de récupération d’urgence entre les horodatages des grandes instances HANA dans différentes régions Azure. Par exemple, supposons que vous déployez des grandes instances HANA dans la région Azure USA Ouest. Vous pouvez ensuite utiliser les grandes instances HANA dans la région USA Est comme unités de récupération d’urgence. La récupération d’urgence n’est pas configurée automatiquement, car vous devez acheter une autre grande instance HANA dans la région de la récupération d’urgence. La configuration de la récupération d’urgence fonctionne pour les configurations avec scale-up et scale-out. 

La plupart des clients se servent de l’unité installée dans la région de la récupération d’urgence pour exécuter les systèmes hors production qui utilisent une instance HANA installée. La grande instance HANA doit être de la même référence SKU que celle utilisé à des fins de production. L’image suivante montre la configuration de disque entre l’unité du serveur dans la région de production Azure et la région de récupération d’urgence :

![Configuration de la récupération d’urgence du point de vue du disque](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Comme le montre cette vue d’ensemble, vous devrez commander un deuxième ensemble de volumes de disque. Les volumes de disque cibles associés au serveur grande instance HANA sur le site de récupération d’urgence sont de la même taille que les volumes de production. 

Les volumes suivants sont répliqués de la région de production sur le site de récupération d’urgence :

- /hana/data
- /hana/logbackups 
- /hana/shared (/usr/sap compris)

Le volume /hana/log n’est pas répliqué. Le journal des transactions SAP HANA n’est pas nécessaire pour une restauration à partir de ces volumes.

## <a name="hana-large-instance-storage-replication"></a>Réplication du stockage de grandes instances HANA 

La réplication du stockage est à la base de la fonctionnalité de récupération d’urgence dans l’infrastructure de grandes instances HANA. La fonctionnalité utilisée côté stockage n’est pas un flux constant de modifications répliquées de manière asynchrone à mesure que des changements sont apportés au volume de stockage. Il s’agit plutôt d’un mécanisme reposant sur la création régulière de captures instantanées de ces volumes. Les données différentielles entre une capture instantanée déjà répliquée et une nouvelle capture instantanée non encore répliquée sont ensuite transférées vers le site de récupération d’urgence dans les volumes de disque cibles. Ces captures instantanées sont stockées sur les volumes. Si un basculement se produit en cas de récupération d’urgence, elles doivent être restaurées sur ces volumes.  

Le premier transfert de la totalité des données du volume doit avoir lieu avant que la quantité de données ne devienne inférieure aux données différentielles entre les captures instantanées. Ainsi, les volumes sur le site de récupération d’urgence contiennent toutes les captures instantanées de volumes effectuées sur le site de production. Au final, vous pouvez utiliser ce système de récupération d’urgence pour revenir à un état antérieur et récupérer des données perdues, sans restaurer le système de production.

Dans le cadre d’un déploiement MCOD avec plusieurs instances SAP HANA indépendantes sur une seule grande instance HANA, toutes les instances SAP HANA doivent avoir leur stockage répliqué du côté de la récupération d’urgence.

Si vous utilisez la réplication de système HANA pour garantir la haute disponibilité dans votre site de production, et que vous utilisez la réplication basée sur le stockage pour le site de récupération d’urgence, les volumes des deux nœuds sont répliqués du site principal vers l’instance de récupération d’urgence. Achetez un stockage supplémentaire (de taille identique à celle du nœud principal) sur le site de récupération d’urgence pour permettre la réplication vers ce site à partir des nœuds principal et secondaire. 

>[!NOTE]
>La fonctionnalité de réplication du stockage des grandes instances HANA met en miroir et réplique les captures instantanées du stockage. Si vous n’effectuez pas de captures instantanées comme cela est décrit dans [Sauvegarde et restauration](hana-backup-restore.md), il n’y a pas de réplication possible vers le site de récupération d’urgence. L’exécution de captures instantanées de stockage est un prérequis pour la réplication de stockage vers le site de récupération d’urgence.

## <a name="preparation-of-the-disaster-recovery-scenario"></a>Préparation du scénario de récupération d’urgence
Dans ce scénario de récupération d’urgence, vous utilisez un système de production qui s’exécute sur de grandes instances HANA dans la région de production Azure. Pour les étapes qui suivent, nous supposons que le SID de ce système HANA est « PRD ». Vous disposez aussi d’un système hors production qui s’exécute sur de grandes instances HANA dans la région Azure de récupération d’urgence. Son SID est « TST ». L’image suivante montre cette configuration :

![Première étape de la configuration de la récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Supposons que l’instance de serveur n’a pas encore été achetée avec l’ensemble de volumes de stockage supplémentaire. L’équipe de gestion des services SAP HANA sur Azure attache les volumes ajoutés. Ces volumes sont une cible pour le réplica de production vers la grande instance HANA sur laquelle vous exécutez l’instance HANA « TST ». Vous devez alors fournir le SID de votre instance HANA de production. Une fois que l’équipe de gestion des services SAP HANA sur Azure a confirmé l’attachement de ces volumes, vous devez monter ces derniers sur la grande instance HANA.

![Étape suivante de la configuration de la récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

L’étape suivante consiste à installer la deuxième instance SAP HANA sur la grande instance HANA dans la région Azure de récupération d’urgence, où vous exécutez l’instance HANA « TST ». La nouvelle instance SAP HANA installée doit avoir le même SID. Les utilisateurs créés doivent avoir les mêmes UID et ID de groupe que l’instance de production. Pour plus d’informations, lisez [Sauvegarde et restauration](hana-backup-restore.md). Quand l’installation est terminée, vous devez effectuer les étapes suivantes :

- Exécutez l’étape 2 de la préparation de capture instantanée de stockage décrite dans [Sauvegarde et restauration](hana-backup-restore.md).
- Créez une clé publique pour l’unité de récupération d’urgence de la grande instance HANA, si ce n’est déjà fait. Consultez l’étape 3 de la préparation de capture instantanée de stockage décrite dans [Sauvegarde et restauration](hana-backup-restore.md).
- Maintenez *HANABackupCustomerDetails.txt* avec la nouvelle instance HANA et testez si la connectivité au stockage fonctionne correctement.  
- Arrêtez l’instance SAP HANA nouvellement installée sur la grande instance HANA dans la région Azure de récupération d’urgence.
- Démonter ces volumes PRD et contacter l’équipe de gestion des services SAP HANA sur Azure. Les volumes ne peuvent pas rester montés sur l’unité, car ils ne sont pas accessibles tant qu’ils fonctionnent en tant que cible de réplication de stockage.  

![Diagramme illustrant la relation de réplication entre les volumes PRD dans la région Azure de production et les volumes PRD dans la région Azure de récupération d’urgence.](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

L’équipe des opérations établit la relation de réplication entre les volumes PRD dans la région de production et les volumes PRD dans la région de récupération d’urgence.

>[!IMPORTANT]
>Le volume /hana/log n’est pas répliqué, car il n’est pas nécessaire pour restaurer la base de données SAP HANA répliquée dans un état cohérent sur le site de récupération d’urgence.

Ensuite, définissez la planification de sauvegarde des captures instantanées de stockage de façon à remplir vos objectifs RTO et RPO en cas de sinistre. Pour diminuer l’objectif RPO, définissez les intervalles de réplication suivants dans le service de grandes instances HANA :
- Pour les volumes couverts par la capture instantanée combinée (type **hana**), configurez la réplication toutes les 15 minutes vers les cibles de volume de stockage équivalents sur le site de récupération d’urgence.
- Pour le volume de sauvegarde de fichier journal (type **logs** de capture instantanée), configurez la réplication toutes les 3 minutes vers les cibles de volume de stockage équivalents sur le site de récupération d’urgence.

Pour diminuer l’objectif RPO :
- Effectuez une capture instantanée de stockage de type **hana** toutes les 30 à 60 minutes. Pour plus d’informations, consultez [Effectuer une sauvegarde à l’aide de l’outil Azure Application Consistent Snapshot Tool](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md).
- Effectuez des sauvegardes des journaux des transactions SAP HANA toutes les 5 minutes.
- Effectuez une capture instantanée de stockage de type **logs** toutes les 5 à 15 minutes. Avec cet intervalle, vous obtenez un RPO d’environ 15 à 25 minutes.

Dans cette configuration, la séquence des sauvegardes de fichier journal, des captures instantanées de stockage, ainsi que la réplication du volume de sauvegarde de fichier journal HANA et des volumes /hana/data et /hana/shared (/usr/sap compris), pourraient ressembler aux données ci-dessous :

 ![Relation entre une capture instantanée de sauvegarde de fichier journal et un miroir de capture instantanée sur un axe temporel](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Pour améliorer le RPO dans le cadre d’une récupération d’urgence, vous pouvez copier les sauvegardes de fichier journal HANA depuis SAP HANA sur Azure (grandes instances) vers l’autre région Azure. Pour diminuer encore l’objectif RPO, effectuez les étapes suivantes :

1. Sauvegardez le journal des transactions HANA aussi souvent que possible dans /hana/logbackups.
1. Utilisez rsync pour copier les sauvegardes de fichier journal sur les machines virtuelles Azure hébergées sur le partage NFS. Les machines virtuelles se trouvent dans des réseaux virtuels Azure dans la région Azure de production et dans la région de récupération d’urgence. Connectez les deux réseaux virtuels Azure au circuit reliant les grandes instances HANA de production à Azure. Pour plus d’informations, consultez [Considérations sur le réseau pour la récupération d’urgence avec de grandes instances HANA](hana-overview-high-availability-disaster-recovery.md#network-considerations-for-disaster-recovery-with-hana-large-instances). 
1. Conservez les sauvegardes des fichiers journaux dans la région de la machine virtuelle qui est attachée au stockage NFS exporté.
1. Dans le cas d’un basculement d’urgence, complétez les sauvegardes de fichiers journaux disponibles sur le volume /hana/logbackups avec des sauvegardes de fichiers journaux plus récentes sur le partage NFS sur le site de récupération d’urgence. 
1. Lancez une sauvegarde de fichier journal pour restaurer jusqu’à la dernière sauvegarde enregistrée dans la région de la récupération d’urgence.

Lorsque l’équipe des opérations des grandes instances HANA confirme la relation de réplication configurée et que vous exécutez des sauvegardes de captures instantanées de stockage, la réplication des données commence.

![Étape de configuration de la récupération d’urgence avant d’établir la réplication](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Durant la réplication, les captures instantanées sur les volumes PRD dans les régions Azure de récupération d’urgence ne sont pas restaurées. Les captures instantanées sont stockées uniquement. Si les volumes sont montés dans cet état, ils reflètent l’état dans lequel vous les avez démontés après l’installation de l’instance SAP HANA PRD sur le serveur de la région Azure de récupération d’urgence. Ils reflètent également les sauvegardes de stockage qui ne sont pas encore restaurées.

En cas de basculement, vous pouvez aussi opter pour une restauration à partir d’une capture instantanée de stockage plus ancienne au lieu de la dernière capture instantanée de stockage.

## <a name="next-steps"></a>Étapes suivantes

Découvrez la procédure de basculement en cas de récupération d’urgence.

> [!div class="nextstepaction"]
> [Procédure de basculement en cas de récupération d’urgence](hana-failover-procedure.md)
