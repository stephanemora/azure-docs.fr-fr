---
title: Connecter des données intelligence des menaces à la version préliminaire d’Azure Sentinel | Microsoft Docs
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
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: a33b00e4e553824f1f719be2c8518487241f0a11
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786182"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Connectez des données à partir de fournisseurs d’informations 

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Une fois que vous diffusez vos données dans Azure Sentinel, vous pouvez enrichir il avec le flux de threat intelligence que vous utilisez dans votre organisation. 

Pour activer cross vérifier vos alertes et des règles avec la valeur true sur les menaces, par exemple, si vous recevez une alerte à partir d’une adresse IP spécifique, votre intégration du fournisseur de threat intelligence pourront vous permettent de savoir si cette adresse IP a été récemment trouvée malveillante , Sentinel azure permet l’intégration avec [fournisseurs d’intelligence des menaces](https://aka.ms/graphsecuritytips). 

Vous pouvez diffuser des journaux à partir de fournisseurs d’informations dans Azure Sentinel avec un seul clic. Cette connexion vous permet d’incorporer des indicateurs contenant différents types d’observables comme l’adresse IP, domaine, URL et le hachage de fichier pour rechercher et créer des règles dans Azure Sentinel d’alerte.  
> [!NOTE]
> Vous pouvez entrer des indicateurs de menace personnalisé dans Azure Sentinel pour une utilisation dans les règles d’alerte, les tableaux de bord et les scénarios de chasse en intégrant le [tiIndicator de sécurité de Microsoft Graph](https://aka.ms/graphsecuritytiindicators) entité ou en utilisant un [Microsoft Sécurité de graphique intégrée Threat Intelligence Platform](https://aka.ms/graphsecuritytips).

## <a name="prerequisites"></a>Conditions préalables  

- Utilisateur avec l’administrateur général ou d’autorisations d’administrateur de sécurité 

- Application de Threat intelligence intégrée à Microsoft Intelligent Security Graph 

## <a name="connect-to-threat-intelligence"></a>Se connecter à des menaces 

1. Si vous utilisez déjà un fournisseur d’intelligence des menaces, veillez à accéder à votre application de l’info-bulle et accorder l’autorisation d’envoyer des indicateurs à Microsoft et de spécifier le service comme Azure Sentinel.  

2. Dans Azure Sentinel, sélectionnez **connecteurs de données** puis cliquez sur le **Threat Intelligence** vignette.

3. Cliquez sur **Connecter**. 

4. Pour utiliser le schéma pertinent dans Analytique de journal pour les flux d’intelligence sur les menaces, recherchez **ThreatIntelligenceIndicator**. 

 
## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter votre fournisseur de menaces à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, consultez les articles suivants.

- Pour commencer à utiliser Azure Sentinel, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
- Découvrez comment [intégrer vos données à Azure Sentinel](quickstart-onboard.md) et [obtenir une visibilité de vos données et des menaces potentielles](quickstart-get-visibility.md).
