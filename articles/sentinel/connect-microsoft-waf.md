---
title: Connecter Microsoft web application firewall data vers Azure Sentinel Preview | Microsoft Docs
description: Découvrez comment connecter les données de Microsoft web application firewall à Azure Sentinel.
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
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5316fa7e3aa4465349b762b99bec9171f821062f
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491028"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Connectez des données à partir de pare-feu d’applications web Microsoft

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser des journaux de pare-feu d’applications de la passerelle d’Application Azure Microsoft web (WAF). Ce pare-feu d’applications Web protège vos applications contre les vulnérabilités web courantes telles que l’injection SQL et les scripts intersites et vous permet de personnaliser les règles pour réduire les faux positifs. Suivez ces instructions pour diffuser vos journaux de pare-feu d’applications Web de Microsoft dans Azure Sentinel.


## <a name="prerequisites"></a>Conditions préalables

- Une ressource de passerelle d’application existante

## <a name="connect-to-microsoft-web-application-firewall"></a>Se connecter au pare-feu d’applications web Microsoft

Si vous avez déjà le pare-feu d’applications web de Microsoft, assurez-vous que vous disposez d’une ressource de passerelle existante.
Une fois que votre pare-feu d’applications web Microsoft est déployé et l’obtention de données, les données d’alerte peuvent facilement être diffusé en continu dans Azure Sentinel.
    
1. Dans le portail Azure Sentinel, sélectionnez **connecteurs de données**.
1. Dans la page de connecteurs de données, sélectionnez le **WAF** vignette.
1. Accédez à [ressource Application Gateway](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) et choisissez votre WAF.
    1. Sélectionnez **les paramètres de Diagnostic**.
    1. Sélectionnez **+ ajouter un paramètre de diagnostic** sous la table.
    1. Dans le **les paramètres de Diagnostic** page, tapez un **nom** et sélectionnez **envoyer à Log Analytique**.
    1. Sous **espace de travail Log Analytique** sélectionnez l’espace de travail Azure Sentinel.
    1. Sélectionnez les types de journaux que vous souhaitez analyser. Nous vous recommandons : ApplicationGatewayAccessLog et ApplicationGatewayFirewallLog.
1. Pour utiliser le schéma pertinent dans Analytique de journal pour les alertes de Microsoft web application firewall, recherchez **AzureDiagnostics**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter des pare-feu d’applications web Microsoft à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).
