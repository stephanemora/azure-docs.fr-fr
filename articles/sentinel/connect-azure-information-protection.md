---
title: Connexion de données de Azure Information Protection vers Azure Sentinel Preview | Microsoft Docs
description: Découvrez comment connecter des données d’Azure Information Protection dans Azure Sentinel.
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
ms.openlocfilehash: 2f970910e19b3c1ed9d262d356c49848f4248b09
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790839"
---
# <a name="connect-data-from-azure-information-protection"></a>Connectez des données à partir d’Azure Information Protection

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser des journaux à partir de [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) dans Azure Sentinel avec un seul clic. Azure Information Protection vous aide à protéger vos données, qu’elles soient stockées dans le cloud ou dans les infrastructures locales et les contrôle et aident à sécuriser la messagerie électronique, les documents et les données sensibles que vous partagez en dehors de votre entreprise. De la classification aisée aux étiquettes incorporées et les autorisations, améliorer la protection des données à tout moment avec Azure Information Protection. Lorsque vous vous connectez Azure Information Protection pour Azure Sentinel, flux de données vous toutes les alertes d’Azure Information Protection dans Azure Sentinel.


## <a name="prerequisites"></a>Conditions préalables

- Utilisateur avec l’administrateur général, administrateur de sécurité ou des autorisations de protection des informations


## <a name="connect-to-azure-information-protection"></a>Se connecter à Azure Information Protection

Si vous disposez déjà d’Azure Information Protection, assurez-vous qu’il est [activé sur votre réseau](https://docs.microsoft.com/azure/information-protection/activate-service).
Si Azure Information Protection est déployée et obtention de données, les données d’alerte peuvent facilement être diffusé en continu dans Azure Sentinel.


1. Dans Azure Sentinel, sélectionnez **connecteurs de données** puis cliquez sur le **Azure Information Protection** vignette.

2. Accédez à la [portail d’Azure Information Protection](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade) 

3. Sous **connexion**, configurer la diffusion en continu des journaux à partir d’Azure Information Protection vers Azure Sentinel en cliquant sur [configurer analytique](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade)

4. Sélectionnez l’espace de travail dans lequel vous avez déployé Azure Sentinel. 

5. Cliquez sur **OK**.

6. Pour utiliser le schéma pertinent dans Analytique de journal pour les alertes d’Azure Information Protection, recherchez **InformationProtectionLogs_CL**.




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure Information Protection pour Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).
