---
title: Procédure HANA de basculement vers un site de récupération d’urgence pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Découvrez comment basculer vers un site de récupération d’urgence pour SAP HANA sur Azure (grandes instances).
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
ms.date: 6/16/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f5720d1ce2281e8984db98b0dc900c4b9de8a52a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284866"
---
# <a name="disaster-recovery-failover-procedure"></a>Procédure de basculement en cas de récupération d’urgence

>[!IMPORTANT]
>Cet article n’est en aucun cas un remplacement de la documentation d’administration de SAP HANA ou des Notes SAP. Nous assumons que vous avez une bonne compréhension et une expertise approfondie des opérations et de l’administration SAP HANA, principalement de la sauvegarde, de la restauration, de la haute disponibilité et de la récupération d’urgence (DR). Dans cet article, des captures d’écran de SAP HANA Studio sont affichées. Le contenu, la structure et la nature des écrans des outils d’administration de SAP, ainsi que les outils eux-mêmes, peuvent changer selon la version de SAP HANA.

Dans cet article, nous allons passer en revue les étapes de basculement vers un site de récupération d'urgence pour SAP HANA sur Azure (grandes instances), également appelé infrastructure BareMetal. 

## <a name="failover-scenarios-and-options"></a>Scénarios et options de basculement

Il existe deux cas à prendre en compte lorsque vous basculez vers un site de récupération d’urgence (DR) :

- Vous devez rétablir l’état le plus récent des données dans la base de données SAP HANA. Dans ce cas, il existe un script en libre-service qui vous pouvez utiliser pour effectuer le basculement sans avoir à contacter Microsoft. Pour la restauration automatique, vous devez collaborer avec Microsoft.
- Vous voulez restaurer jusqu’à une capture instantanée de stockage qui n’est pas la dernière répliquée. Dans ce cas, vous devez collaborer avec Microsoft. 

>[!NOTE]
>Les étapes suivantes doivent être effectuées sur l’unité de grande instance HANA dans le site de récupération d’urgence. 
 
Pour restaurer les dernières captures instantanées de stockage répliquées, suivez les étapes décrites dans la section « Exécution d’un basculement complet de récupération d’urgence - azure_hana_dr_failover » relative aux [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

Si vous souhaitez faire basculer plusieurs instances de SAP HANA, exécutez la commande azure_hana_dr_failover plusieurs fois. Lorsque vous y êtes invité, entrez le SID SAP HANA que vous souhaitez faire basculer et restaurer. 


Vous pouvez également tester le basculement de récupération d’urgence sans impacter la relation de réplication réelle. Pour effectuer un test de basculement, suivez les étapes décrites dans la section « Exécution d’un test de basculement - azure_hana_test_dr_failover » relative aux [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

>[!IMPORTANT]
>N’exécutez *pas* de transactions de production sur l’instance que vous avez créée sur le site de récupération d’urgence (DR), en suivant le processus de **test d’un basculement**. La commande azure_hana_test_dr_failover crée un ensemble de volumes sans aucune relation avec le site principal. Par conséquent, une resynchronisation au site principal n’est *pas* possible. 

Si vous souhaitez tester plusieurs instances SAP HANA, exécutez le script plusieurs fois. Lorsque vous y êtes invité, entrez le SID SAP HANA de l’instance dont vous souhaitez tester le basculement. 

## <a name="set-dr-volumes-to-an-earlier-snapshot"></a>Définir des volumes de récupération d’urgence sur un instantané antérieur

Supposons que vous avez besoin de basculer vers le site de récupération d’urgence pour récupérer des données supprimées quelques heures auparavant et que vous devez configurer les volumes de récupération d’urgence sur une capture instantanée plus ancienne. La procédure suivante s’applique alors : 

1. Arrêtez l’instance de non-production HANA sur la grande instance HANA de récupération d’urgence que vous exécutez. Une instance de production HANA dormante est préinstallée.
1. Assurez-vous qu’aucun processus SAP HANA n’est en cours d’exécution. Utilisez la commande suivante pour effectuer cette vérification :

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      La sortie doit indiquer que le processus **hdbdaemon** est arrêté et qu’aucun autre processus HANA n’est en cours d’exécution ou démarré.
1. Déterminez le nom de capture instantanée ou l’ID de sauvegarde SAP HANA dans lequel vous souhaitez restaurer le site de récupération d’urgence. Dans les cas réels de récupération d’urgence, cette capture instantanée est généralement la capture instantanée la plus récente. Si vous avez besoin de récupérer des données perdues, sélectionnez une capture instantanée antérieure.
1. Contactez le support Azure en envoyant une demande de support prioritaire. Demandez la restauration de cette capture instantanée avec son nom et sa date. Vous pouvez également l’identifier à l’aide de l’ID de sauvegarde HANA sur le site de récupération d’urgence. Par défaut, les opérations ne restaurent que le volume /hana/data. Si vous souhaitez également restaurer les volumes /hana/logbackups, vous devez le préciser. *Ne restaurez pas le volume /hana/shared.* Sélectionnez plutôt des fichiers spécifiques, tels que global.ini, dans le répertoire **.snapshot** et ses sous-répertoires, après avoir remonté le volume /hana/shared pour PRD. 

   Les opérations Microsoft effectuent les étapes suivantes :

   a. Arrêter la réplication des captures instantanées depuis le volume de production vers les volumes de récupération d’urgence. Cette interruption s’est peut-être déjà produite si une panne sur le site de production est la cause du sinistre.
   
   b. Restaurer le nom de la capture instantanée de stockage ou la capture instantanée avec l’ID de sauvegarde que vous avez choisi sur les volumes de récupération d’urgence.
   
Après la restauration, les volumes de récupération d’urgence peuvent être montés sur les grandes instances HANA dans la région de récupération d’urgence.
      
1. Montez les volumes de récupération d’urgence sur l’unité de grande instance HANA sur le site de récupération d’urgence. 
1. Démarrez l’instance de production SAP HANA dormante.
1. Supposons que vous avez choisi de copier les journaux de sauvegarde du journal des transactions pour réduire la durée de l’objectif de point de récupération (RPO). Fusionnez ensuite les sauvegardes du journal des transactions dans le répertoire de récupération d'urgence /hana/logbackups nouvellement monté. Ne remplacez pas les sauvegardes existantes. Copiez les sauvegardes plus récentes qui n’ont pas été répliquées avec la réplication la plus récente d’une capture instantanée de stockage.
1. Vous pouvez également restaurer des fichiers uniques à partir des captures instantanées qui ont été répliquées sur le volume /hana/shared/PRD dans la région Azure de récupération d’urgence (DR).

## <a name="recover-the-sap-hana-production-instance"></a>Récupérer l’instance de production SAP HANA

Les étapes suivantes montrent comment récupérer l’instance de production SAP HANA à partir de la capture instantanée de stockage restaurée et des sauvegardes du journal des transactions disponibles.

1. Modifiez l’emplacement de sauvegarde en le définissant sur **/hana/logbackups** à l’aide de SAP HANA Studio.

   ![Modifier l’emplacement de sauvegarde pour la récupération d’urgence (DR)](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA parcourt les emplacements de fichier de sauvegarde et propose la restauration de la sauvegarde de fichier journal la plus récente. L’analyse peut prendre quelques minutes jusqu’à ce qu’un écran comme ci-dessous s’affiche :

   ![Liste des sauvegardes de fichier journal pour la récupération d’urgence (DR)](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Modifiez certains paramètres par défaut :

      - Désactivez l’option **Use Delta Backups** (Utiliser les sauvegardes différentielles).
      - Sélectionnez l’option **Initialize Log Area** (Initialiser la zone de journalisation).

   ![Définir l’option Initialize Log Area (Initialiser la zone de journalisation)](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Sélectionnez **Terminer**.

   ![Terminer la restauration avec récupération d’urgence](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Une fenêtre de progression, comme celle illustrée ici, doit apparaître. Gardez à l’esprit qu’il s’agit d’un exemple de restauration avec récupération d’une configuration SAP HANA Scale-out à 3 nœuds.

![Progression de la restauration](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Si la restauration ne répond plus à l’écran de **fin** et que l’écran de progression n’apparaît pas, vérifiez que toutes les instances SAP HANA sur les nœuds Worker sont en cours d’exécution. Si nécessaire, démarrez manuellement les instances SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Restauration automatique depuis un site de récupération d’urgence vers un site de production

Vous pouvez effectuer une restauration automatique depuis un site de récupération d’urgence vers un site de production. Supposons que le basculement vers le site de récupération d’urgence a été provoqué par des problèmes dans la région Azure de production et non par votre besoin de récupérer des données perdues. 

Vous exécutez votre charge de travail de production SAP depuis un certain temps sur le site de récupération d’urgence. Les problèmes rencontrés sur le site de production étant résolus, vous souhaitez effectuer une restauration automatique sur votre site de production. Étant donné que vous ne pouvez pas perdre de données, le retour au site de production implique plusieurs étapes et une collaboration étroite avec l’équipe des opérations SAP HANA sur Azure. Il vous incombe d’autoriser l’équipe des opérations à lancer la resynchronisation avec le site de production, une fois les problèmes résolus.

Procédez comme suit :

1. L’équipe des opérations SAP HANA sur Azure reçoit l’autorisation de synchroniser les volumes de stockage de production à partir des volumes de stockage de récupération d’urgence, qui représentent désormais l’état de production. Dans cet état, la grande instance HANA sur le site de production est arrêtée.
1. L’équipe des opérations SAP HANA sur Azure surveille la réplication et s’assure qu’un rattrapage est effectué avant de vous informer.
1. Vous arrêtez les applications qui utilisent l’instance HANA de production sur le site de récupération d’urgence. Vous effectuez ensuite une sauvegarde du journal des transactions HANA. Puis vous arrêtez l’instance HANA en cours d’exécution sur les grandes instances HANA dans le site de récupération d’urgence.
1. À présent, l’équipe des opérations synchronise à nouveau manuellement les volumes de disque.
1. L’équipe des opérations SAP HANA sur Azure redémarre la grande instance HANA sur le site de production. Elle vous la repasse. Vous vérifiez que l’instance SAP HANA est arrêtée au moment du démarrage de la grande instance HANA.
1. Vous effectuez les mêmes étapes de restauration de base de données que celles que vous avez réalisées pour basculer vers le site de récupération d’urgence.

## <a name="monitor-disaster-recovery-replication"></a>Surveillance de la réplication de récupération d’urgence

Pour surveiller l’état de la progression de la réplication de stockage, exécutez le script `azure_hana_replication_status`. Pour fonctionner comme prévu, cette commande doit être exécutée depuis une unité en cours d’exécution sur l’emplacement de la récupération d’urgence. La commande fonctionne que la réplication soit active ou non. La commande peut être exécutée pour chaque grande instance HANA de votre locataire à l’emplacement de la récupération d’urgence. Vous ne pouvez pas l’utiliser pour obtenir des détails sur le volume de démarrage. 

Pour plus d’informations sur la commande et sa sortie, consultez la section « Obtention de l’état de réplication de récupération d’urgence - azure_hana_replication_status » relative aux [outils de capture instantanée Microsoft pour SAP HANA sur Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf).


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la surveillance de SAP HANA (Grandes instances) sur Azure.

> [!div class="nextstepaction"]
> [Surveiller SAP HANA (grandes instances) sur Azure](troubleshooting-monitoring.md)
