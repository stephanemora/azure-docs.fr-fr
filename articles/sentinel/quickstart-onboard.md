---
title: 'Démarrage rapide : Intégration dans Azure Sentinel'
description: Découvrez comment collecter des données dans Azure Sentinel grâce à ce guide de démarrage rapide.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: 11fecd875385d8ba044cbe44e2270eed11d61ce1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77581547"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Démarrage rapide : Intégrer Azure Sentinel

Dans ce guide de démarrage rapide, apprenez à intégrer Azure Sentinel. 

Pour intégrer Azure Sentinel, vous devez d’abord activer Azure Sentinel, puis vous connecter à vos sources de données. Azure Sentinel est fourni avec plusieurs connecteurs pour les solutions Microsoft, disponibles par défaut et offrant une intégration en temps réel, y compris les solutions Microsoft Threat Protection, les sources Microsoft 365, dont Office 365, Azure AD, Azure ATP et Microsoft Cloud App Security, etc. En outre, il existe des connecteurs intégrés pour la connexion aux écosystèmes de sécurité élargis pour les solutions non Microsoft. Vous pouvez également utiliser le format d’événement commun, Syslog ou l’API REST pour connecter vos sources de données à Azure Sentinel.  

Après avoir connecté vos sources de données, opérez votre choix dans une galerie de classeurs créés par des experts, qui mettent en avant des insights sur la base de vos données. Vous pouvez aisément adapter ces classeurs à vos besoins.

>[!IMPORTANT] 
> Pour plus d’informations sur les frais liés à l’utilisation d’Azure Sentinel, consultez les [tarifs Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Conditions préalables globales

- Activez l’abonnement Azure, et si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Espace de travail Log Analytics. Découvrez comment [créer un espace de travail Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Pour plus d’informations sur les espaces de travail Log Analytics, consultez [Conception de votre déploiement de journaux Azure Monitor](../azure-monitor/platform/design-logs-deployment.md).

- Pour activer Azure Sentinel, vous avez besoin des autorisations des contributeurs concernant l’abonnement dans lequel réside l’espace de travail Azure Sentinel. 
- Pour utiliser Azure Sentinel, vous avez besoin des autorisations des contributeurs ou des lecteurs sur le groupe de ressources auquel appartient l’espace de travail.
- Des autorisations supplémentaires peuvent être nécessaires pour la connexion à des sources de données spécifiques.
- Azure Sentinel est un service payant. Pour obtenir des informations de tarification, voir [À propos d’Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## <a name="enable-azure-sentinel"></a>Activer Azure Sentinel <a name="enable"></a>

1. Connectez-vous au portail Azure. Assurez-vous que l’abonnement dans lequel est créé Azure Sentinel est bien sélectionné.

1. Recherchez et sélectionnez **Azure Sentinel**.

   ![recherche](./media/quickstart-onboard/search-product.png)

1. Sélectionnez **Ajouter**.

1. Sélectionnez l’espace de travail que vous souhaitez utiliser ou créez-en un. Vous pouvez exécuter Azure Sentinel sur plusieurs espaces de travail, mais les données sont isolées sur un seul espace de travail.

   ![recherche](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Les espaces de travail par défaut créés par Azure Security Center n’apparaissent pas dans la liste. Vous ne pouvez pas installer Azure Sentinel sur ces derniers.
   > - Azure Sentinel peut s’exécuter sur des espaces de travail dans tout [région généralement disponible de Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor), à l’exception des régions Chine, Allemagne et Azure Government. Les données générées par Azure Sentinel (par exemple, incidents, signets et règles d’alerte, qui peuvent contenir des données client provenant de ces espaces de travail) sont enregistrées dans les régions Europe Ouest (pour les espaces de travail situés en Europe) ou USA Est (pour tous les espaces de travail basés aux États-Unis, ainsi que dans toute région autre que l’Europe).

1. Sélectionnez **Ajouter Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Connecter des sources de données

Azure Sentinel crée la connexion aux services et aux applications en se connectant au service et en transférant les événements et les journaux vers Azure Sentinel. Pour les ordinateurs et les machines virtuelles, vous pouvez installer l’agent Azure Sentinel qui collecte les journaux et les transfère à Azure Sentinel. Pour les pare-feux et les proxies, Azure Sentinel utilise un serveur Linux Syslog. L’agent est installé sur ce dernier et l’utilise pour collecter les fichiers journaux et les transférer à Azure Sentinel. 
 
1. Cliquez sur **Collecte de données**.
2. Il existe une vignette pour chaque source de données que vous pouvez connecter.<br>
Par exemple, cliquez sur **Azure Active Directory**. Si vous connectez cette source de données, vous diffusez tous les journaux d’Azure AD vers Azure Sentinel. Vous pouvez sélectionner le type de journaux de votre choix pour accéder et vous connecter aux journaux et/ou journaux d’audit. <br>
Dans la partie inférieure, Azure Sentinel fournit des suggestions sur les classeurs que vous devez installer pour chaque connecteur afin que vous puissiez immédiatement obtenir des insights intéressants sur vos données. <br> Suivez les instructions d’installation ou [consultez le guide de connexion concerné](connect-data-sources.md) pour plus d’informations. Pour plus d’informations sur les connecteurs de données, consultez [Connecter des services Microsoft](connect-data-sources.md).

Une fois que vos données sources sont connectées, elles commencent à être diffusées en continu dans Azure Sentinel et peuvent être utilisées. Vous pouvez afficher les journaux dans les [tableaux de bord intégrés](quickstart-get-visibility.md) et commencer à créer des requêtes dans Log Analytics pour [examiner les données](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter des sources de données à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- Diffusez des données en streaming depuis des [appliances Common Event Format](connect-common-event-format.md) dans Azure Sentinel.
