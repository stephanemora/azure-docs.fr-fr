---
title: Procédure de basculement HANA sur un site de reprise après sinistre pour SAP HANA sur Azure (grandes Instances) | Microsoft Docs
description: Comment effectuer un basculement vers un site de récupération d’urgence pour SAP HANA sur Azure (grandes Instances)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7d4f6216b4a57796ab5c0296713316dd97c47a8
ms.sourcegitcommit: 60606c5e9a20b2906f6b6e3a3ddbcb6c826962d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987884"
---
# <a name="disaster-recovery-failover-procedure"></a>Procédure de basculement en cas de récupération d’urgence


>[!IMPORTANT]
>Cet article n’est pas un remplacement pour la documentation d’administration de SAP HANA ou les Notes SAP. Nous espérons que vous avez une connaissance approfondie des et une expertise dans l’administration de SAP HANA et d’opérations, en particulier pour la sauvegarde, restauration, haute disponibilité et récupération d’urgence (DR). Dans cet article, des captures d’écran à partir de SAP HANA Studio sont affichés. Le contenu, la structure et la nature des écrans des outils d’administration de SAP, ainsi que les outils eux-mêmes, peuvent changer selon la version de SAP HANA.

Il existe deux cas à prendre en compte lorsque vous basculez vers un site de récupération d’urgence :

- Vous devez rétablir l’état le plus récent des données dans la base de données SAP HANA. Dans ce cas, il est un script en libre service avec lequel vous pouvez effectuer le basculement sans devoir contacter Microsoft. Pour la restauration automatique, vous devez collaborer avec Microsoft.
- Vous souhaitez restaurer sur une capture instantanée de stockage qui n’est pas la dernière capture instantanée répliquée. Dans ce cas, vous devez collaborer avec Microsoft. 

>[!NOTE]
>Les étapes suivantes doivent être effectués sur l’unité de grande Instance HANA, qui représente l’unité de récupération d’urgence. 
 
Pour restaurer les dernières captures instantanées de stockage, suivez les étapes décrites dans « Effectuer full basculement de récupération d’urgence - azure_hana_dr_failover » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Si vous souhaitez avoir plusieurs instances SAP HANA basculées, exécutez la commande azure_hana_dr_failover plusieurs fois. Lors de la demande, entrez le SID SAP HANA vous souhaitez basculer et restaurer. 


Vous pouvez tester le basculement de récupération d’urgence également sans impact sur la relation de réplication réelle. Pour effectuer un test de basculement, suivez les étapes décrites dans « Effectuer un test de basculement de récupération d’urgence - azure_hana_test_dr_failover » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

>[!IMPORTANT]
>Faire *pas* exécuter des transactions de production sur l’instance que vous avez créé dans le site de récupération d’urgence via le processus de **test de basculement**. La commande azure_hana_test_dr_failover crée un ensemble de volumes qui n’ont aucune relation vers le site principal. Par conséquent, une resynchronisation au site principal n’est *pas* possible. 

Si vous souhaitez avoir plusieurs instances de SAP HANA à tester, exécutez le script plusieurs fois. Lors de la demande, entrez le SID SAP HANA de l’instance que vous souhaitez tester le basculement. 

>[!NOTE]
>Si vous avez besoin de basculer vers le site de récupération d’urgence pour récupérer des données a été supprimé, il y a des heures et devez les volumes de récupération d’urgence à définir pour un instantané antérieur, cette procédure s’applique. 

1. Arrêtez l’instance de non-production de HANA sur l’unité de récupération d’urgence des grandes Instances HANA que vous exécutez. Une instance de production HANA dormante est préinstallée.
1. Assurez-vous qu’aucun processus SAP HANA n’est en cours d’exécution. Utilisez la commande suivante pour effectuer cette vérification :

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      La sortie doit indiquer que le processus **hdbdaemon** est arrêté et qu’aucun autre processus HANA n’est en cours d’exécution ou démarré.
1. Déterminez le nom de capture instantanée ou l’ID de sauvegarde SAP HANA dans lequel vous souhaitez restaurer le site de récupération d’urgence. Dans les cas réels de récupération d’urgence, cette capture instantanée est généralement la capture instantanée la plus récente. Si vous avez besoin de récupérer des données perdues, sélectionnez une capture instantanée antérieure.
1. Contactez le support Azure en envoyant une demande de support prioritaire. Demander la restauration de cet instantané portant le nom et date de l’instantané ou l’ID de sauvegarde HANA sur le site de récupération d’urgence. Par défaut, les opérations ne restaurent que le volume /hana/data. Si vous souhaitez avoir trop de volumes/hana/logbackups, vous devez préciser. *Ne restaurez pas le volume /hana/shared.* Au lieu de cela, choisissez les fichiers spécifiques tels que global.ini le **.snapshot** répertoire et ses sous-répertoires, après avoir remonté/hana/shared volume pour PRD. 

   Du côté des opérations, les étapes suivantes s’effectuent :

   a. La réplication des captures instantanées depuis le volume de production vers les volumes de récupération d’urgence est arrêtée. Cette interruption peut s’être déjà produite si la raison pour laquelle vous avez besoin d’effectuer une récupération d’urgence est une panne sur le site de production.
   
   b. La capture instantanée de stockage dont vous avez indiqué le nom ou qui est associée à l’ID de sauvegarde spécifié est restaurée sur les volumes de récupération d’urgence.
   
   c. Après la restauration, les volumes de récupération d’urgence peuvent être montés sur les unités de grande instance HANA dans la région de récupération d’urgence.
      
1. Montez les volumes de récupération d’urgence sur l’unité de grande instance HANA sur le site de récupération d’urgence. 
1. Démarrez l’instance de production SAP HANA dormante.
1. Si vous avez choisi de copier les journaux de sauvegarde du journal de transaction afin de réduire le délai RPO, fusionner les sauvegardes du journal des transactions dans le répertoire qui vient d’être monté de récupération d’urgence/hana/logbackups. Ne remplacez pas les sauvegardes existantes. Copiez les sauvegardes plus récentes qui n’ont pas été répliquées avec la dernière réplication d’une capture instantanée de stockage.
1. Vous pouvez également restaurer des fichiers uniques depuis les captures instantanées qui n’ont pas été répliquées sur le volume de /hana/shared/PRD dans la région de récupération d’urgence Azure.

Les étapes suivantes montrent comment récupérer l’instance de production SAP HANA en fonction de la capture instantanée de stockage restaurée et les sauvegardes de journal des transactions qui sont disponibles.

1. Modifiez l’emplacement de sauvegarde en le définissant sur **/hana/logbackups** à l’aide de SAP HANA Studio.

   ![Modifier l’emplacement de sauvegarde pour la récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA parcourt les emplacements de fichier de sauvegarde et propose la restauration de la sauvegarde de fichier journal la plus récente. L’analyse peut prendre quelques minutes jusqu'à ce qu’un écran comme s’affiche les éléments suivants :

   ![Liste des sauvegardes de journal des transactions pour la récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Modifiez certains paramètres par défaut :

      - Désactivez l’option **Use Delta Backups** (Utiliser les sauvegardes différentielles).
      - Sélectionnez l’option **Initialize Log Area** (Initialiser la zone de journalisation).

   ![Définir l’option Initialize Log Area (Initialiser la zone de journalisation)](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Sélectionnez **Terminer**.

   ![Terminer la restauration avec récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Une fenêtre de progression, comme celle illustrée ici, doit apparaître. Gardez à l’esprit qu’il s’agit d’un exemple de restauration avec récupération d’une configuration SAP HANA Scale-out à 3 nœuds.

![Progression de la restauration](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Si la restauration cesse de répondre à la **Terminer** et n’afficher l’écran de progression, vérifiez que toutes les instances de SAP HANA sur les nœuds de travail sont en cours d’exécution. Si nécessaire, démarrez manuellement les instances SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Restauration automatique depuis un site de récupération d’urgence vers un site de production
Vous pouvez effectuer une restauration automatique depuis un site de récupération d’urgence vers un site de production. Supposons que le basculement vers le site de récupération d’urgence a été provoqué par des problèmes dans la région Azure de production et non par votre besoin de récupérer des données perdues. 

Vous exécutez votre charge de travail de production SAP depuis un certain temps dans le site de récupération d’urgence. Les problèmes rencontrés sur le site de production étant résolus, vous souhaitez effectuer une restauration automatique sur votre site de production. Étant donné que vous ne pouvez pas perdre de données, le retour au site de production implique plusieurs étapes et une collaboration étroite avec l’équipe des opérations SAP HANA sur Azure. Il vous incombe d’autoriser l’équipe des opérations à lancer la resynchronisation avec le site de production, une fois les problèmes résolus.

Procédez comme suit :

1. L’équipe des opérations SAP HANA sur Azure reçoit l’autorisation de synchroniser les volumes de stockage de production à partir des volumes de stockage de récupération d’urgence, qui représentent désormais l’état de production. Dans cet état, l’unité de grande instance HANA sur le site de production est arrêtée.
1. L’équipe SAP HANA sur les opérations Azure surveille la réplication et permet de s’assurer qu’un rattrapage est effectué avant qu’ils vous informent.
1. Vous arrêtez les applications qui utilisent l’instance HANA de production sur le site de récupération d’urgence. Vous effectuez ensuite une sauvegarde de fichier journal HANA. Ensuite, vous arrêtez l’instance HANA qui s’exécute sur les unités de grande Instance HANA dans le site de récupération d’urgence.
1. Une fois que l’instance HANA qui s’exécute dans l’unité de grande Instance HANA dans le site de récupération d’urgence est arrêtée, l’équipe des opérations resynchronise manuellement les volumes de disque.
1. L’équipe SAP HANA sur les opérations Azure redémarre l’unité de grande Instance HANA dans le site de production. Ils repasse à vous. Vous vous assurer que l’instance SAP HANA est dans un état d’arrêt au moment du démarrage de l’unité de grande Instance HANA.
1. Vous effectuez les mêmes étapes de restauration de base de données que vous l’avez fait lorsque vous avez précédemment basculé vers le site de récupération d’urgence.

## <a name="monitor-disaster-recovery-replication"></a>Surveillance de la réplication de récupération d’urgence

Pour surveiller l’état de progression de la réplication de stockage, exécutez le script `azure_hana_replication_status`. Cette commande doit être exécutée à partir d’une unité qui s’exécute dans l’emplacement de récupération d’urgence de fonctionner comme prévu. La commande fonctionne quel que soit le si la réplication est active. La commande peut être exécutée pour chaque unité de grande Instance HANA de votre client à l’emplacement de récupération d’urgence. Il ne peut pas être utilisé pour obtenir des détails sur le volume de démarrage. 

Pour plus d’informations sur la commande et sa sortie, consultez « Obtenir l’état de réplication de récupération d’urgence - azure_hana_replication_status » dans [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


## <a name="next-steps"></a>Étapes suivantes
- Consultez [surveiller et résoudre les problèmes à partir de Hana](hana-monitor-troubleshoot.md).
