---
title: Basculer une appliance de réplication vers Azure Site Recovery - Préversion
description: Cet article explique comment basculer entre différentes appliances de réplication lors de la réplication de machines virtuelles VMware vers Azure Site Recovery - Préversion
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: f95ed991833a9f6ed1b0635a8678e393f152cb56
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122535327"
---
# <a name="switch-azure-site-recovery-replication-appliance"></a>Basculer une appliance de réplication Azure Site Recovery

>[!NOTE]
> Les informations contenues dans cet article s’appliquent à Azure Site Recovery - Préversion.

Vous devez [créer et déployer une appliance de réplication locale Azure Site Recovery](deploy-vmware-azure-replication-appliance-preview.md) quand vous utilisez [Azure Site Recovery](site-recovery-overview.md)pour la récupération d’urgence de machines virtuelles VMware et de serveurs physiques sur Azure. Pour plus d’informations sur l’appliance de réplication, consultez [l’architecture](vmware-azure-architecture-preview.md). Vous pouvez créer et utiliser plusieurs appliances de réplication en fonction des besoins en capacité de votre organisation.

Cet article fournit des informations sur la façon dont vous pouvez basculer entre les appliances de réplication.

## <a name="application-resilience"></a>Résilience des applications

En général, dans l’architecture classique, si vous avez besoin de maintenir la résilience de votre serveur de configuration, l’action recommandée consiste à effectuer des sauvegardes manuelles régulières de la machine. Il s’agit d’un processus très fastidieux, également sujet aux erreurs et aux échecs.  

Cette version préliminaire offre une meilleure façon de renforcer la résistance de vos appliances. Si votre appliance de réplication est en baisse ou si vous devez équilibrer les machines en cours d’exécution sur un appareil, il vous suffit de lancer une autre appliance de réplication et de basculer tous vos ordinateurs vers le nouvel appareil.


## <a name="consideration-for-switching-replication-appliance"></a>Considération pour le changement d’appliance de réplication

Vous pouvez basculer l’appliance de réplication dans les scénarios suivants :

- Vous devez effectuer une opération de commutation au cas où votre appliance de réplication actuelle Azure Site Recovery a brûlé (burnt down), c’est-à-dire que tous ses composants sont en arrêt.
  - Un appareil est considéré comme brûlé uniquement si tous ses composants sont en arrêt. Si l’un des composants répond, l’opération de commutation sera bloquée.
  - Si votre appliance actuelle a brûlé, vous devrez à nouveau fournir des informations d’identification pour accéder aux machines que vous essayez de basculer. Si vous équilibrez la charge et que votre appliance actuelle est toujours dans un état non critique, les informations d’identification sont automatiquement sélectionnées et vous n’êtes pas obligé de les entrer à nouveau lors du basculement vers une autre appliance.
- Vous devrez peut-être effectuer l’opération de commutation si vous devez équilibrer la charge de votre appliance de réplication.
- Si vous essayez d’effectuer un basculement avec un objectif d’équilibrage de charge sur une appliance, tous les composants de votre appliance actuelle doivent être dans l’état intègre ou d’avertissement. Le manque de réponse d’un composant bloquera l’opération de commutation.
-  Assurez-vous que l’appareil que vous êtes en train de basculer est dans un état intègre ou d’avertissement pour que l’opération aboutisse.
-  Seules les machines qui sont répliquées de l’environnement local vers Azure peuvent être sélectionnées lors de l’opération de commutation vers une autre appliance.  


## <a name="switch-a-replication-appliance"></a>Basculer une appliance de réplication

Par exemple, voici le scénario dans lequel l’appliance de réplication 1 (RA1) est devenue critique et vous souhaitez déplacer les charges de travail protégées vers l’appliance de réplication 2 (RA2), qui est dans un état intègre. Vous pouvez également basculer les charges de travail sous RA1 à RA2 pour toute modification de l’équilibrage de charge ou de l’organisation.

**Procédez comme suit pour basculer une appliance** :

1. Rendez-vous sur le panneau de l’**infrastructure Site Recovery**  et sélectionnez **appliance de réplication ASR**.

   La liste des appliances disponibles et leur état d’intégrité s’affichent. Par exemple, RA2 est intègre ici.

   ![Liste des appliances de réplication intègres](./media/switch-replication-appliance-preview/appliance-health.png)

2. Sélectionnez l’appliance de réplication (RA1) et sélectionnez **Basculer l’appliance**.

   ![Sélectionner l’appliance de réplication à basculer](./media/switch-replication-appliance-preview/select-switch-appliance.png)


3. Sous **Sélectionner les machines**, sélectionnez les machines que vous souhaitez basculer vers une autre appliance de réplication (RA2). Sélectionnez **Suivant**.

   >[!NOTE]
   > Seuls les machines qui ont été protégées par l’appliance actuelle sont visibles dans la liste. Les machines basculées ne seront pas présentes ici  

    ![Sélectionner les machines à basculer](./media/switch-replication-appliance-preview/select-machines.png)

4. Sur la page  **Paramètres de la source**  , choisissez une appliance de réplication différente pour chacune des machines sélectionnées.

   ![Paramètres de la source pour l’appliance de réplication](./media/switch-replication-appliance-preview/source-settings.png)

   >[!NOTE]
   > Si votre appliance actuelle a brûlé, vous devrez sélectionner les informations d’identification pour accéder aux machines. Dans le cas contraire, le champ sera désactivé.

5. Vérifiez la sélection, puis cliquez sur **Basculer l’appliance**.

   ![Evaluation de l’appliance de réplication](./media/switch-replication-appliance-preview/review-switch-appliance.png)

   Une fois la resynchronisation terminée, l’état de la réplication devient intègre pour les machines virtuelles qui sont déplacées vers une nouvelle appliance.

## <a name="next-steps"></a>Étapes suivantes
Configurez la récupération d’urgence des [machines virtuelles VMware](vmware-azure-tutorial.md) vers Azure.
