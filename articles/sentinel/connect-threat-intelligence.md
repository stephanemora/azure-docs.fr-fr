---
title: Collecter des données d’intelligence contre les menaces dans Azure en version préliminaire Sentinel | Microsoft Docs
description: Découvrez comment connecter des données d’analyse décisionnelle contre les menaces à Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 56412543-5664-44c1-b026-2dbaf78a9a50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2c5a7dc08886e21ef8e287540d9139ec555b11a2
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242290"
---
# <a name="collect-data-from-threat-intelligence-providers"></a>Collecter des données à partir de fournisseurs d’informations 

> [!IMPORTANT]
> Sentinel Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Une fois que vous diffusez vos données dans Azure Sentinel, vous pouvez enrichir il avec le flux de threat intelligence que vous utilisez dans votre organisation. 

Pour activer cross vérifier vos alertes et des règles avec la valeur true sur les menaces, par exemple, si vous recevez une alerte à partir d’une adresse IP spécifique, votre intégration du fournisseur de threat intelligence pourront vous permettent de savoir si cette adresse IP a été récemment trouvée malveillante , Sentinel azure permet l’intégration avec [fournisseurs d’intelligence des menaces](https://aka.ms/graphsecuritytips). 

Vous pouvez diffuser des journaux à partir de fournisseurs d’informations dans Azure Sentinel avec un seul clic. Cette connexion vous permet d’incorporer des indicateurs contenant différents types d’observables comme l’adresse IP, domaine, URL et le hachage de fichier pour rechercher et créer des règles dans Azure Sentinel d’alerte.  

## <a name="prerequisites"></a>Conditions préalables  

- Utilisateur avec l’administrateur général ou d’autorisations d’administrateur de sécurité 

- Application de Threat intelligence intégrée à Microsoft Intelligent Security Graph 

## <a name="connect-to-threat-intelligence"></a>Se connecter à des menaces 

1. Si vous utilisez déjà un fournisseur d’intelligence des menaces, veillez à accéder à votre application de l’info-bulle et accorder l’autorisation d’envoyer des indicateurs à Microsoft et de spécifier le service comme Azure Sentinel.  

2. Dans Azure Sentinel, sélectionnez **collecte des données** puis cliquez sur le **Threat Intelligence** vignette.

3. Cliquez sur **Connecter**. 

4. Pour utiliser le schéma pertinent dans Analytique de journal pour les flux d’intelligence sur les menaces, recherchez **ThreatIntelligenceIndicator**. 

 
## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter votre fournisseur de menaces à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, consultez les articles suivants.

- Pour la prise en main Azure Sentinel, vous avez besoin d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
- Découvrez comment [intégrer vos données vers Azure Sentinel](quickstart-onboard.md), et [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
