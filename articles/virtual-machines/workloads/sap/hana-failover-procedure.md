---
title: Procédure HANA de basculement vers le site de récupération d’urgence pour SAP HANA sur Azure (grandes Instances) | Microsoft Docs
description: Comment opérer un basculement vers le site de récupération d’urgence pour SAP HANA sur Azure (grandes Instances)
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
ms.openlocfilehash: 76d8bb816bdf229d13a49fa61337899a8bf29ecd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098284"
---
# <a name="disaster-recovery-failover-procedure"></a>Procédure de basculement en cas de récupération d’urgence


>[!IMPORTANT]
>Cette documentation n’est en aucun cas un remplacement de la documentation d’administration de SAP HANA ou des Notes SAP. Le lecteur doit avoir une bonne compréhension et une expertise approfondie des opérations et de l’administration SAP HANA, principalement sur la sauvegarde, la restauration, la haute disponibilité et la récupération d’urgence. Dans cette documentation, des captures d’écran à partir de SAP HANA Studio sont affichées. Le contenu, la structure et la nature des écrans des outils d’administration de SAP, ainsi que les outils eux-mêmes, peuvent changer selon la version de SAP HANA.

Il existe deux cas à prendre en compte lors du basculement vers le site de récupération d’urgence :

- Vous devez rétablir l’état le plus récent des données dans la base de données SAP HANA. Dans ce cas, il existe un script en libre service qui vous permet d’effectuer le basculement sans avoir à contacter Microsoft. Cependant, pour la restauration automatique, vous devez collaborer avec Microsoft.
- Vous voulez restaurer jusqu’à une capture instantanée de stockage qui n’est pas la dernière répliquée. Dans ce cas, vous devez collaborer avec Microsoft. 

>[!NOTE]
>Les étapes suivantes doivent être exécutées sur l’unité de grande instance HANA, qui représente l’unité de récupération d’urgence. 
 
Pour restaurer les dernières captures instantanées de stockage, procédez comme indiqué dans la section **'Effectuer le basculement de récupération d’urgence complet - azure_hana_dr_failover'** du document [outils de capture instantanée Microsoft pour SAP HANA sur Azure ](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Si vous souhaitez avoir plusieurs instances SAP HANA basculées, vous devez exécuter la commande azure_hana_dr_failover plusieurs fois. Lorsque vous y êtes invité, entrez le SID SAP HANA que vous souhaitez faire basculer et restaurer. 


Vous pouvez également tester le basculement de récupération d’urgence sans impacter la relation de réplication réelle. Pour effectuer un test de basculement, suivez les étapes de **« Effectuer un test de basculement de récupération d’urgence - azure_hana_test_dr_failover »** du document [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

>[!IMPORTANT]
>Faire *pas* exécuter des transactions de production sur l’instance que vous avez créé dans le site de récupération d’urgence via le processus de **test de basculement**. Azure_hana_test_dr_failover de cette commande crée un ensemble de volumes qui n’ont aucune relation vers le site principal. Par conséquent, une resynchronisation au site principal n’est *pas* possible. 

Si vous souhaitez avoir plusieurs instances SAP HANA à tester, vous devez exécuter le script plusieurs fois. Lorsque vous y êtes invité, tapez le SID SAP HANA de l’instance dont vous souhaitez tester le basculement. 

>[!NOTE]
>Si vous avez besoin de basculer vers le site de récupération d’urgence pour récupérer des données a été supprimé, il y a des heures et devez les volumes de récupération d’urgence à définir pour un instantané antérieur, cette procédure s’applique. 

1. Arrêtez l’instance de non-production HANA sur l’unité de récupération d’urgence des grandes instances HANA que vous exécutez. Ceci, parce qu’une instance de production HANA dormante est préinstallée.
1. Assurez-vous qu’aucun processus SAP HANA n’est en cours d’exécution. Utilisez la commande suivante pour effectuer cette vérification : `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. La sortie doit indiquer que le processus **hdbdaemon** est arrêté et qu’aucun autre processus HANA n’est en cours d’exécution ou démarré.
1. Déterminez le nom de capture instantanée ou l’ID de sauvegarde SAP HANA dans lequel vous souhaitez restaurer le site de récupération d’urgence. Dans les cas réels de récupération d’urgence, cette capture instantanée est généralement la capture instantanée la plus récente. Si vous avez besoin de récupérer des données perdues, sélectionnez une capture instantanée antérieure.
1. Contactez le support Azure en envoyant une demande de support prioritaire. Demandez la restauration de cette capture instantanée (avec son nom et sa date) ou l’ID de sauvegarde HANA sur le site de récupération d’urgence. Par défaut, les opérations ne restaurent que le volume /hana/data. Si vous souhaitez également restaurer les volumes /hana/logbackups, vous devez le préciser. *Ne restaurez pas le volume /hana/shared.* Sélectionnez plutôt des fichiers spécifiques, tels que global.ini, dans le répertoire **.snapshot** et ses sous-répertoires, après avoir remonté le volume /hana/shared pour PRD. 

   Du côté des opérations, les étapes suivantes s’effectuent :

   a. La réplication des captures instantanées depuis le volume de production vers les volumes de récupération d’urgence est arrêtée. Cette interruption peut s’être déjà produite si la raison pour laquelle vous avez besoin d’effectuer une récupération d’urgence est une panne sur le site de production.
   
   b. La capture instantanée de stockage dont vous avez indiqué le nom ou qui est associée à l’ID de sauvegarde spécifié est restaurée sur les volumes de récupération d’urgence.
   
   c. Après la restauration, les volumes de récupération d’urgence peuvent être montés sur les unités de grande instance HANA dans la région de récupération d’urgence.
      
1. Montez les volumes de récupération d’urgence sur l’unité de grande instance HANA sur le site de récupération d’urgence. 
1. Démarrez l’instance de production SAP HANA dormante.
1. Si vous avez choisi de copier les journaux d’activité de sauvegarde de fichier journal pour réduire le délai RPO, vous devez fusionner ces sauvegardes de fichier journal dans le répertoire de récupération d’urgence /hana/logbackups qui vient d’être monté. Ne remplacez pas les sauvegardes existantes. Copiez les sauvegardes plus récentes qui n’ont pas été répliquées avec la réplication la plus récente d’une capture instantanée de stockage.
1. Vous pouvez également restaurer des fichiers uniques à partir des captures instantanées qui ont été répliquées sur le volume /hana/shared/PRD dans la région Azure de récupération d’urgence.

Les étapes suivantes concernent la récupération de l’instance de production SAP HANA en fonction de la capture instantanée de stockage restaurée et des sauvegardes de fichier journal qui sont disponibles :

1. Modifiez l’emplacement de sauvegarde en le définissant sur **/hana/logbackups** à l’aide de SAP HANA Studio.
   ![Modifier l’emplacement de sauvegarde pour la récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA parcourt les emplacements de fichier de sauvegarde et propose la restauration de la sauvegarde de fichier journal la plus récente. L’analyse peut prendre quelques minutes jusqu'à ce qu’un écran comme s’affiche les éléments suivants : ![Liste des sauvegardes de journal des transactions pour la récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Modifiez certains paramètres par défaut :

      - Désactivez l’option **Use Delta Backups** (Utiliser les sauvegardes différentielles).
      - Sélectionnez l’option **Initialize Log Area** (Initialiser la zone de journalisation).

   ![Définir l’option Initialize Log Area (Initialiser la zone de journalisation)](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Sélectionnez **Terminer**.

   ![Terminer la restauration avec récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Une fenêtre de progression, comme celle illustrée ici, doit apparaître. Gardez à l’esprit qu’il s’agit d’un exemple de restauration avec récupération d’une configuration SAP HANA Scale-out à 3 nœuds.

![Progression de la restauration](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Si la restauration semble cesser de répondre à la **Terminer** et ne pas afficher l’écran de progression, vérifiez que toutes les instances de SAP HANA sur les nœuds de travail sont en cours d’exécution. Si nécessaire, démarrez manuellement les instances SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Restauration automatique depuis un site de récupération d’urgence vers un site de production
Vous pouvez effectuer une restauration automatique depuis un site de récupération d’urgence vers un site de production. Supposons que le basculement vers le site de récupération d’urgence a été provoqué par des problèmes dans la région Azure de production et non par votre besoin de récupérer des données perdues. Vous exécutez votre charge de travail de production SAP depuis un certain temps sur le site de récupération d’urgence. Les problèmes rencontrés sur le site de production étant résolus, vous souhaitez effectuer une restauration automatique sur votre site de production. Étant donné que vous ne pouvez pas perdre de données, le retour au site de production implique plusieurs étapes et une collaboration étroite avec l’équipe des opérations SAP HANA sur Azure. Il vous incombe d’autoriser l’équipe des opérations à lancer la resynchronisation avec le site de production, une fois les problèmes résolus.

Voici les étapes à effectuer :

1. L’équipe des opérations SAP HANA sur Azure reçoit l’autorisation de synchroniser les volumes de stockage de production à partir des volumes de stockage de récupération d’urgence, qui représentent désormais l’état de production. Dans cet état, l’unité de grande instance HANA sur le site de production est arrêtée.
1. L’équipe des opérations SAP HANA sur Azure surveille la réplication et s’assure qu’un rattrapage est effectué avant de vous informer.
1. Vous arrêtez les applications qui utilisent l’instance HANA de production sur le site de récupération d’urgence. Vous effectuez ensuite une sauvegarde de fichier journal HANA. Puis vous arrêtez l’instance HANA en cours d’exécution sur les unités de grande instance HANA dans le site de récupération d’urgence.
1. Une fois que l’instance HANA en cours d’exécution dans l’unité de grande instance HANA sur le site de récupération d’urgence est arrêtée, l’équipe des opérations resynchronise manuellement les volumes de disque.
1. L’équipe des opérations SAP HANA sur Azure redémarre l’unité de grande instance HANA sur le site de production et vous la repasse. Vous vérifiez que l’instance SAP HANA est arrêtée au moment du démarrage de l’unité de grande instance HANA.
1. Vous effectuez les mêmes étapes de restauration de base de données que celles que vous avez réalisées pour le basculement vers le site de récupération d’urgence.

## <a name="monitor-disaster-recovery-replication"></a>Surveillance de la réplication de récupération d’urgence

Vous pouvez surveiller l’état de la progression de la réplication de stockage en exécutant le script `azure_hana_replication_status`. Cette commande doit être exécutée à partir d’une unité en cours d’exécution dans l’emplacement de récupération d’urgence de fonctionner comme prévu. La commande fonctionne indépendamment de si la réplication est active. La commande peut être exécutée pour chaque unité de grande Instance HANA de votre client à l’emplacement de récupération d’urgence. Vous ne pouvez pas l’utiliser pour obtenir des détails sur le volume de démarrage. Pour plus d’informations de la commande et sa sortie lire **'Obtenir l’état de réplication de récupération d’urgence - azure_hana_replication_status'** du document [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


## <a name="next-steps"></a>Étapes suivantes
- Reportez-vous à [analyse et dépannage à partir de Hana](hana-monitor-troubleshoot.md).
