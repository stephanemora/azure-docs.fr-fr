---
title: Connexion des données Azure Information Protection à Azure Sentinel (préversion) | Microsoft Docs
description: Découvrez comment connecter les données Azure Information Protection dans Azure Sentinel.
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
ms.openlocfilehash: 3edfdca16141726ea591c8473528fc23ee076c53
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620605"
---
# <a name="connect-data-from-azure-information-protection"></a>Connecter des données depuis Azure Information Protection

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser des journaux dans Azure Sentinel avec un seul clic à partir d’[Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip). Azure Information Protection vous aide à protéger vos données, qu’elles soient stockées dans le cloud ou dans des infrastructures locales, ainsi qu’à contrôler et à sécuriser la messagerie électronique, les documents et les données sensibles que vous partagez en dehors de votre entreprise. De la classification aisée aux étiquettes et autorisations intégrées, vos données profiteront d’une protection améliorée en permanence grâce à Azure Information Protection. Lorsque vous vous connectez Azure Information Protection à Azure Sentinel, vous diffusez en continu toutes les alertes d’Azure Information Protection dans Azure Sentinel.


## <a name="prerequisites"></a>Prérequis

- Utilisateur, doté d’autorisations d’administrateur général, d’administrateur de la sécurité ou de protection des informations


## <a name="connect-to-azure-information-protection"></a>Connexion à Azure Information Protection

Si vous avez déjà Azure Information Protection, assurez-vous qu’il est [activé sur votre réseau](https://docs.microsoft.com/azure/information-protection/activate-service).
Si Azure Information Protection est déployé et obtient des données, les données d’alerte peuvent facilement être diffusées en continu dans Azure Sentinel.


1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette **Azure Information Protection**.

2. Accédez au [portail Azure Information Protection](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade). 

3. Sous **Connexion**, configurez la diffusion en continu des journaux à partir d’Azure Information Protection vers Azure Sentinel en cliquant sur [Configurer l’analytique](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade).

4. Sélectionnez l’espace de travail dans lequel vous avez déployé Azure Sentinel. 

5. Cliquez sur **OK**.

6. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes d’Azure Information Protection, recherchez **InformationProtectionLogs_CL**.




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure Information Protection à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
