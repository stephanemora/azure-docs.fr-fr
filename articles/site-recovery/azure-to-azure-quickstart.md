---
title: Configurer la récupération d’urgence pour une machine virtuelle IaaS Azure vers une région Azure secondaire
description: Ce démarrage rapide présente les étapes nécessaires à la récupération d’urgence d’une machine virtuelle IaaS Azure entre des régions Azure à l’aide du service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 23aeeb8cd14ec2d0654525af42b48f59a6f7564f
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520320"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Configurer la récupération d’urgence vers une région Azure secondaire pour une machine virtuelle Azure        

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence et de continuité d’activité en garantissant le bon fonctionnement et la disponibilité de vos applications métier pendant les interruptions planifiées et non planifiées. Site Recovery gère et orchestre la récupération d’urgence des machines locales et des machines virtuelles Azure, notamment la réplication, le basculement et la récupération.

Ce démarrage rapide explique comment configurer la récupération d’urgence pour une machine virtuelle Azure en la répliquant vers une autre région.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
> Cet article est une procédure pas à pas rapide pour les nouveaux utilisateurs. Il propose la méthode la plus rapide avec les options par défaut et une personnalisation minimale.  Pour une procédure plus complète, consultez [notre didacticiel](azure-to-azure-tutorial-enable-replication.md).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Activer la réplication des machines virtuelles Azure

1. Dans le portail Azure, cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle à répliquer.
2. Dans **Opérations**, cliquez sur **Récupération d’urgence**.
3. Dans **Configurer la récupération d’urgence** > **Région cible**, sélectionnez la région cible vers laquelle vous allez effectuer la réplication.
4. Pour ce démarrage rapide, acceptez les autres paramètres par défaut.
5. Cliquez sur **Activer la réplication**. Cette opération démarre un travail consistant à activer la réplication pour la machine virtuelle.

    ![activer la réplication](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Vérifier les paramètres

Une fois le travail de réplication terminé, vous pouvez vérifier l’état de la réplication, modifier les paramètres de réplication et tester le déploiement.

1. Dans **Opérations**, cliquez sur **Récupération d’urgence**.
2. Vous pouvez vérifier l’intégrité de la réplication, les points de récupération qui ont été créés ainsi que les régions sources et cibles sur la carte.

   ![État de la réplication](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

La réplication de la machine virtuelle dans la région principale cesse quand vous désactivez la réplication pour cette machine :

- Les paramètres de réplication source sont automatiquement nettoyés. L’extension Site Recovery installée sur la machine virtuelle dans le cadre de la réplication n’est pas supprimée et doit être supprimée manuellement. 
- La facturation Site Recovery pour la machine virtuelle cesse.

Arrêtez la réplication comme suit :

1. Sélectionnez la machine virtuelle.
2. Dans **Récupération d’urgence**, cliquez sur **Désactiver la réplication**.

   ![Désactiver la réplication](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez répliqué une seule machine virtuelle vers une région secondaire. À présent, réessayez la réplication de plusieurs machines virtuelles Azure à l’aide d’un plan de récupération.

> [!div class="nextstepaction"]
> [Configurer la récupération d’urgence pour des machines virtuelles Azure](azure-to-azure-tutorial-enable-replication.md)
