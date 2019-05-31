---
title: Intégrer en version préliminaire de sentinelle Azure | Microsoft Docs
description: Découvrez comment collecter des données dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2019
ms.author: rkarlin
ms.openlocfilehash: 891f9fbd26b53b392ac84ed9d420b58558cd20c2
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258434"
---
# <a name="on-board-azure-sentinel-preview"></a>Aperçu de la sentinelle Azure intégrée

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dans ce démarrage rapide, vous allez apprendre comment intégrer Sentinel d’Azure. 

Pour intégrer Azure Sentinel, vous devez d’abord activer Azure Sentinel et connectez vos sources de données. Sentinel Azure est fourni avec plusieurs connecteurs pour les solutions de Microsoft, disponibles en dehors de la zone et fournissant l’intégration en temps réel, notamment des solutions de Protection contre les menaces de Microsoft, Microsoft 365 sources, y compris Office 365, Azure AD, Azure ATP, et Microsoft Cloud App Security et bien plus encore. En outre, il existe des connecteurs intégrés pour la connexion aux écosystèmes de sécurité élargis pour les solutions non Microsoft. Vous pouvez également utiliser le format d’événement commun, Syslog ou l’API REST pour vous connecter vos sources de données avec Azure Sentinel.  

Une fois que vous vous connectez vos sources de données, choisissez parmi une galerie de tableaux de bord créé de manière experte surface insights basés sur vos données. Ces tableaux de bord permettre être facilement adapté à vos besoins.


## <a name="global-prerequisites"></a>Conditions préalables globales

- Abonnement Azure actif, si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Ouvrez une session espace de travail Analytique. Découvrez comment [créer un espace de travail Analytique de journal](../log-analytics/log-analytics-quick-create-workspace.md)

-  Pour activer Azure Sentinel, vous avez besoin d’autorisations de collaborateur pour l’abonnement dans lequel réside l’espace de travail Azure Sentinel. 
- Pour utiliser Azure Sentinel, vous avez besoin des autorisations de collaborateur ou lecteur sur le groupe de ressources auquel appartient l’espace de travail
- Des autorisations supplémentaires peuvent être nécessaires pour connecter les sources de données spécifiques
 
## Activer Azure Sentinel <a name="enable"></a>

1. Accédez au portail Azure.
2. Assurez-vous que l’abonnement dans lequel Azure Sentinel est créé, est sélectionné. 
3. Recherchez Azure Sentinel. 
   ![search](./media/quickstart-onboard/search-product.png)

1. Cliquez sur **+Ajouter**.
1. Sélectionnez l’espace de travail que vous souhaitez utiliser ou créez-en un. Vous pouvez exécuter Azure Sentinel sur plus d’un espace de travail, mais les données sont isolées sur un seul espace de travail.

   ![recherche](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - **Emplacement de l’espace de travail** il est important de comprendre que toutes les données que vous diffusez sur Azure Sentinel est stockée dans l’emplacement géographique de l’espace de travail que vous avez sélectionné.  
   > - Espaces de travail par défaut créés par Azure Security Center n’apparaîtra pas dans la liste ; Vous ne pouvez pas installer Azure Sentinel dessus.
   > - Sentinel Azure peut s’exécuter sur les espaces de travail qui sont déployés dans une des régions suivantes :  Sud-est de l’Australie, centre du Canada, centre de l’Inde, est des États-Unis, est des États-Unis 2-EUAP (Canaries), est du Japon, Asie du Sud-est, Royaume-Uni Sud, Europe de l’ouest, ouest des États-Unis 2.

6. Cliquez sur **ajouter Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Connecter des sources de données

Sentinel Azure crée la connexion aux services et applications en vous connectant au service et de transfert des événements et les journaux à Azure Sentinel. Pour les ordinateurs et les machines virtuelles, vous pouvez installer l’agent Sentinel Azure qui collecte les journaux et les transfère à Azure Sentinel. Pour les pare-feux et proxys, Sentinel Azure utilise un serveur Linux Syslog. L’agent est installé sur celui-ci et à partir de laquelle l’agent collecte les fichiers journaux et les transfère à Azure Sentinel. 
 
1. Cliquez sur **collecte des données**.
2. Il existe une vignette pour chaque source de données que vous pouvez vous connecter.<br>
Par exemple, cliquez sur **Azure Active Directory**. Si vous vous connectez à cette source de données, vous diffusez tous les journaux à partir d’Azure AD dans Azure Sentinel. Vous pouvez sélectionner le type de journaux que vous souhaitez pour obtenir - journaux de connexion et/ou les journaux d’audit. <br>
En bas, Sentinel Azure fournit des recommandations pour les tableaux de bord, vous devez installer pour chaque connecteur afin de vous pouvez immédiatement obtenir des intéressants insights sur vos données. <br> Suivez les instructions d’installation ou [consultez le guide de connexion pertinentes](connect-data-sources.md) pour plus d’informations. Pour plus d’informations sur les connecteurs de données, consultez [services Microsoft de se connecter](connect-data-sources.md).

Une fois que vos données sources sont connectés, vos données démarre la diffusion en continu dans Azure Sentinel et sont prêtes à commencer à utiliser. Vous pouvez afficher les journaux dans le [tableaux de bord intégrés](quickstart-get-visibility.md) et commencez à créer des requêtes dans Analytique de journal à [examiner les données](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris sur la connexion des sources de données à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).
- Données à partir de Stream [appliances de Format d’erreur commun](connect-common-event-format.md) dans Azure Sentinel.
