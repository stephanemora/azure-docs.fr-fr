---
title: Collecte des données de Microsoft web application firewall Sentinel version préliminaire d’Azure | Microsoft Docs
description: Découvrez comment collecter des données de Microsoft web application firewall dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 2238060acb60b1be0d06b81f62fb45a7f1c7a9b6
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580597"
---
# <a name="collect-data-from-microsoft-web-application-firewall"></a>Collecter des données à partir de pare-feu d’applications web Microsoft

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser des journaux de pare-feu d’applications de la passerelle d’Application Azure Microsoft web (WAF). Ce pare-feu d’applications Web protège vos applications contre les vulnérabilités web courantes telles que l’injection SQL et les scripts intersites et vous permet de personnaliser les règles pour réduire les faux positifs. Suivez ces instructions pour diffuser vos journaux de pare-feu d’applications Web de Microsoft dans Azure Sentinel.


## <a name="prerequisites"></a>Conditions préalables

- Une ressource de passerelle d’application existante

## <a name="connect-to-microsoft-web-application-firewall"></a>Se connecter au pare-feu d’applications web Microsoft

Si vous avez déjà le pare-feu d’applications web de Microsoft, assurez-vous que vous disposez d’une ressource de passerelle existante.
Une fois que votre pare-feu d’applications web Microsoft est déployé et l’obtention de données, les données d’alerte peuvent facilement être diffusé en continu dans Azure Sentinel.
    
5. Dans le portail Azure Sentinel, sélectionnez **connecteurs de données**.
5. Dans la page de connecteurs de données, sélectionnez le **WAF** vignette.
1. Accédez à [ressource Application Gateway](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) et choisissez votre WAF.
    1. Sélectionnez **les paramètres de Diagnostic**.
    1. Sélectionnez **+ ajouter un paramètre de diagnostic** sous la table.
    1. Dans le **les paramètres de Diagnostic** page, tapez un **nom** et sélectionnez **envoyer à Log Analytique**.
    1. Sous **espace de travail Log Analytique** sélectionnez l’espace de travail Azure Sentinel.
    1. Sélectionnez les types de journaux que vous souhaitez analyser. Nous vous recommandons : ApplicationGatewayAccessLog et ApplicationGatewayFirewallLog.
6. Pour utiliser le schéma pertinent dans Analytique de journal pour les alertes de Microsoft web application firewall, recherchez **AzureDiagnostics**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter des pare-feu d’applications web Microsoft à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).
