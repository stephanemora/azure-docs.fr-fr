---
title: Intégration dans la préversion d’Azure Sentinel | Microsoft Docs
description: Découvrez comment collecter des données dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: rkarlin
ms.openlocfilehash: b609dc70c45941ec1132c7cdf614cf9bec8119ff
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019017"
---
# <a name="on-board-azure-sentinel-preview"></a>Intégration dans la préversion d’Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dans ce guide de démarrage rapide, vous allez apprendre à intégrer Azure Sentinel. 

Pour intégrer Azure Sentinel, vous devez d’abord activer Azure Sentinel, puis vous connecter à vos sources de données. Azure Sentinel est fourni avec plusieurs connecteurs pour les solutions Microsoft, disponibles par défaut et offrant une intégration en temps réel, y compris les solutions Microsoft Threat Protection, les sources Microsoft 365, dont Office 365, Azure AD, Azure ATP et Microsoft Cloud App Security, etc. En outre, il existe des connecteurs intégrés pour la connexion aux écosystèmes de sécurité élargis pour les solutions non Microsoft. Vous pouvez également utiliser le format d’événement commun, Syslog ou l’API REST pour connecter vos sources de données à Azure Sentinel.  

Après avoir connecté vos sources de données, vous pouvez faire votre choix parmi une galerie de tableaux de bord créés par des experts, qui mettent en avant les insights provenant de vos sources de données. Ces tableaux de bord peuvent être aisément adaptés à vos besoins.


## <a name="global-prerequisites"></a>Conditions préalables globales

- Activez l’abonnement Azure, et si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Espace de travail Log Analytics. Découvrir comment [créer un espace de travail Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)

-  Pour activer Azure Sentinel, vous avez besoin des autorisations des contributeurs concernant l’abonnement dans lequel réside l’espace de travail Azure Sentinel. 
- Pour utiliser Azure Sentinel, vous avez besoin des autorisations des contributeurs ou des lecteurs sur le groupe de ressources auquel appartient l’espace de travail
- Des autorisations supplémentaires peuvent être nécessaires pour la connexion à des sources de données spécifiques
 
## Activer Azure Sentinel <a name="enable"></a>

1. Accédez au Portail Azure.
2. Assurez-vous que l’abonnement dans lequel est créé Azure Sentinel est bien sélectionné. 
3. Recherchez Azure Sentinel. 
   ![search](./media/quickstart-onboard/search-product.png)

1. Cliquez sur **+Ajouter**.
1. Sélectionnez l’espace de travail que vous souhaitez utiliser ou créez-en un. Vous pouvez exécuter Azure Sentinel sur plusieurs espaces de travail, mais les données sont isolées sur un seul espace de travail.

   ![recherche](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Les espaces de travail par défaut créés par Azure Security Center n’apparaissent pas dans la liste. Vous ne pouvez pas installer Azure Sentinel sur ces derniers.
   > - Azure Sentinel peut s’exécuter sur des espaces de travail déployés dans l’une des régions suivantes : Australie Est, Australie Sud-Est, Canada Centre, Inde Centre, USA Est, USA Est 2 - EUAP (Canary), Japon Est, Asie Sud-Est, Royaume-Uni Sud, Europe Ouest, USA Ouest 2, USA Centre-Ouest, France Centre, Corée Centre, Europe Nord, USA Est 2, Asie Est, USA Ouest, USA Centre, USA Centre Sud.


6. Cliquez sur **Ajouter Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Connecter des sources de données

Azure Sentinel crée la connexion aux services et aux applications en se connectant au service et en transférant les événements et les journaux vers Azure Sentinel. Pour les ordinateurs et les machines virtuelles, vous pouvez installer l’agent Azure Sentinel qui collecte les journaux et les transfère à Azure Sentinel. Pour les pare-feux et les proxies, Azure Sentinel utilise un serveur Linux Syslog. L’agent est installé sur ce dernier et l’utilise pour collecter les fichiers journaux et les transférer à Azure Sentinel. 
 
1. Cliquez sur **Collecte de données**.
2. Il existe une vignette pour chaque source de données que vous pouvez connecter.<br>
Par exemple, cliquez sur **Azure Active Directory**. Si vous connectez cette source de données, vous diffusez tous les journaux d’Azure AD vers Azure Sentinel. Vous pouvez sélectionner le type de journaux de votre choix pour accéder et vous connecter aux journaux et/ou journaux d’audit. <br>
Dans la partie inférieure, Azure Sentinel fournit des recommandations sur les tableaux de bord que vous devez installer pour chaque connecteur afin que vous puissiez immédiatement obtenir des insights intéressants sur vos données. <br> Suivez les instructions d’installation ou [consultez le guide de connexion concerné](connect-data-sources.md) pour plus d’informations. Pour plus d’informations sur les connecteurs de données, consultez [Connecter des services Microsoft](connect-data-sources.md).

Une fois que vos données sources sont connectées, elles commencent à être diffusées en continu dans Azure Sentinel et peuvent être utilisées. Vous pouvez afficher les journaux dans les [tableaux de bord intégrés](quickstart-get-visibility.md) et commencer à créer des requêtes dans Log Analytics pour [examiner les données](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter des sources de données à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
- Diffusez des données à partir d’[appliances de format d’erreur courants](connect-common-event-format.md) vers Azure Sentinel.
