---
title: Connecter des données de veille contre les menaces à Azure Sentinel (préversion) | Microsoft Docs
description: Découvrez comment connecter des données de veille contre les menaces à Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 56412543-5664-44c1-b026-2dbaf78a9a50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 266e487a7c345f75e966afbde567c5bc4683b5c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233747"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Connecter des données issues de fournisseurs de veille contre les menaces 

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Après avoir diffusé vos données dans Azure Sentinel, vous pouvez les enrichir avec le flux de veille contre les menaces que vous utilisez au sein de votre organisation. 

Afin de pouvoir vérifier vos alertes et vos règles en les comparant avec une véritable veille contre les menaces, Azure Sentinel permet l’intégration à des [fournisseurs de veille contre les menaces](https://aka.ms/graphsecuritytips). Par exemple, si vous recevez une alerte provenant d’une adresse IP spécifique, votre intégration à un fournisseur de veille contre les menaces vous avertira si cette dernière a été récemment détectée comme étant malveillante. 

Vous pouvez diffuser des journaux issus de fournisseurs de veille contre les menaces dans Azure Sentinel avec un seul clic. Cette connexion vous permet d’incorporer des indicateurs contenant différents types d’éléments observables comme l’adresse IP, le domaine, l’URL et le hachage de fichier afin de rechercher et créer des règles d’alerte personnalisées dans Azure Sentinel.  
> [!NOTE]
> Dans Azure Sentinel, vous pouvez entrer des indicateurs de menace personnalisés à utiliser dans des règles d’alerte, des tableaux de bord et des scénarios de recherche en intégrant l’entité [tiIndicator de Microsoft Graph Security](https://aka.ms/graphsecuritytiindicators) ou à l’aide d’une [plateforme de veille contre les menaces intégrée Microsoft Graph Security](https://aka.ms/graphsecuritytips).

## <a name="prerequisites"></a>Prérequis  

- Utilisateur, doté d’autorisations d’administrateur général ou d’administrateur de la sécurité 

- Application de veille contre les menaces intégrée à Microsoft Intelligent Security Graph 

## <a name="connect-to-threat-intelligence"></a>Connecter à la veille contre les menaces 

1. Si vous utilisez déjà un fournisseur de veille contre les menaces, pensez à accéder à votre application TIP pour accorder l’autorisation d’envoyer des indicateurs à Microsoft, puis définissez Azure Sentinel en tant que service.  

2. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette de **Threat Intelligence**.

3. Cliquez sur **Connecter**. 

4. Pour utiliser le schéma approprié pour les flux de veille contre les menaces dans Log Analytics, recherchez **ThreatIntelligenceIndicator**. 

 
## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter votre fournisseur de veille contre les menaces à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, consultez les articles suivants.

- Pour utiliser Azure Sentinel, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
- Découvrez comment [intégrer vos données à Azure Sentinel](quickstart-onboard.md) et [obtenir une visibilité de vos données et des menaces potentielles](quickstart-get-visibility.md).
