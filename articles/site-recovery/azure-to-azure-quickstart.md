---
title: Répliquer une machine virtuelle Azure vers une autre région Azure
description: Ce démarrage rapide fournit les étapes nécessaires pour répliquer une machine virtuelle Azure dans une région Azure vers une autre région.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 623c45cbc82139090d893b8501c7d363c4443d67
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456162"
---
# <a name="replicate-an-azure-vm-to-another-azure-region"></a>Répliquer une machine virtuelle Azure vers une autre région Azure

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence et de continuité d’activité en garantissant le bon fonctionnement et la disponibilité de vos applications métier pendant les interruptions planifiées et non planifiées. Site Recovery gère et orchestre la récupération d’urgence des machines locales et des machines virtuelles Azure, notamment la réplication, le basculement et la récupération.

Ce démarrage rapide explique comment répliquer une machine virtuelle Azure vers une autre région.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
> Cet article a pour but de guider les utilisateurs novices tout au long de leur expérience avec Azure Site Recovery en ce qui concerne les options par défaut et la personnalisation minimum. Si vous voulez en savoir plus sur les différents paramètres qui peuvent être personnalisés, reportez-vous au [didacticiel d’activation de la réplication des machines virtuelles Azure](azure-to-azure-tutorial-enable-replication.md)

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur http://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Activer la réplication des machines virtuelles Azure

1. Dans le portail Azure, cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle à répliquer.
2. Dans **Opérations**, cliquez sur **Récupération d’urgence**.
3. Dans **Configurer la récupération d’urgence** > **Région cible**, sélectionnez la région cible vers laquelle vous allez effectuer la réplication.
4. Pour ce démarrage rapide, acceptez les autres paramètres par défaut.
5. Cliquez sur **Activer la réplication**. Cette opération démarre un travail consistant à activer la réplication pour la machine virtuelle.

    ![activer la réplication](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Vérifier les paramètres

Une fois le travail de réplication terminé, vous pouvez vérifier l’état de la réplication, modifier les paramètres de réplication et tester le déploiement.

1. Dans le menu Machine virtuelle, cliquez sur **Récupération d’urgence**.
2. Vous pouvez vérifier l’intégrité de la réplication, les points de récupération qui ont été créés ainsi que les régions sources et cibles sur la carte.

   ![État de la réplication](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

La réplication de la machine virtuelle dans la région principale cesse quand vous désactivez la réplication pour cette machine :

- Les paramètres de réplication source sont automatiquement nettoyés.
- La facturation Site Recovery pour la machine virtuelle cesse également.

Arrêtez la réplication comme suit :

1. Sélectionnez la machine virtuelle.
2. Dans **Récupération d’urgence**, cliquez sur **Désactiver la réplication**.

   ![Désactiver la réplication](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez répliqué une seule machine virtuelle vers une région secondaire. Vous pouvez désormais explorer d’autres options et réessayer la réplication d’un ensemble de machines virtuelles Azure à l’aide d’un plan de récupération.

> [!div class="nextstepaction"]
> [Configurer la récupération d’urgence pour des machines virtuelles Azure](azure-to-azure-tutorial-enable-replication.md)
