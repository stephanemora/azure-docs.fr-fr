---
title: Connecter des données de pare-feu d’applications web Microsoft à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données de pare-feu d’applications web Microsoft à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 0a308394c3cfa77f80db1361b5a49a485ee5ca0e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611346"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Connecter des données de pare-feu d’application web Microsoft

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser en continu des journaux de pare-feu d’applications web (WAF) Microsoft d’Azure Application Gateway. Ce pare-feu d’applications web (WAF) protège vos applications contre les vulnérabilités web courantes telles que l’injection SQL et les scripts intersites, et vous permet de personnaliser les règles afin de réduire le nombre de faux positifs. Suivez ces instructions pour diffuser en continu vos journaux de pare-feu d’applications web Microsoft dans Azure Sentinel.


## <a name="prerequisites"></a>Prérequis

- Une ressource Application Gateway existante

## <a name="connect-to-microsoft-web-application-firewall"></a>Se connecter à un pare-feu d’applications web Microsoft

Si vous disposez déjà d’un pare-feu d’applications web Microsoft, assurez-vous que vous disposez d’une ressource de passerelle existante.
Lorsque votre pare-feu d’applications web Microsoft est déployé et obtient des données, les données d’alerte peuvent facilement être diffusées en continu dans Azure Sentinel.
    
1. Dans le portail Azure Sentinel, sélectionnez **Connecteurs de données**.
1. Dans la page des connecteurs de données, sélectionnez la vignette **WAF**.
1. Accédez à la [ressource Application Gateway](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways)  et choisissez votre WAF.
    1. Sélectionnez  **Paramètres de diagnostic**.
    1. Sélectionnez  **+ Ajouter un paramètre de diagnostic** sous la table.
    1. Dans la page  **Paramètres de diagnostic**, saisissez un  **nom** et sélectionnez  **Envoyer à Log Analytics**.
    1. Sous **Espace de travail Log Analytics**, sélectionnez l’espace de travail Azure Sentinel.
    1. Sélectionnez les types de journaux que vous souhaitez analyser. Recommandé : ApplicationGatewayAccessLog and ApplicationGatewayFirewallLog.
1. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes de pare-feu d’applications web Microsoft, recherchez **AzureDiagnostics**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter le pare-feu d’applications web Microsoft à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
