---
title: Connexion des données Azure AD Identity Protection à Azure Sentinel (préversion) | Microsoft Docs
description: Découvrez comment connecter les données Azure AD Identity Protection à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 7478e5a5ec2260760bb6ddb1a90a66e3acdf2201
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129255"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Connecter des données depuis Azure AD Identity Protection

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser des journaux en continu depuis [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) vers Azure Sentinel pour transmettre en continu des alertes à Azure Sentinel et afficher des tableaux de bord, créer des alertes personnalisées et améliorer les examens. Azure Active Directory Identity Protection fournit une vue consolidée des utilisateurs à risque, des détections de risques et des vulnérabilités, et permet d’éliminer ces risques immédiatement et de définir des stratégies de remédiation automatique pour les événements futurs. Le service repose sur l’expérience de Microsoft en matière de protection des identités des consommateurs et bénéficie d’une précision spectaculaire grâce à plus de 13 milliards d’ouvertures de session par jour. 


## <a name="prerequisites"></a>Prérequis

- Possession d’une [licence Azure Active Directory Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Utilisateur, doté d’autorisations d’administrateur général ou d’administrateur de la sécurité


## <a name="connect-to-azure-ad-identity-protection"></a>Connexion à Azure AD Identity Protection

Si vous avez déjà Azure AD Identity Protection, assurez-vous qu’il est [activé sur votre réseau](../active-directory/identity-protection/enable.md).
Si Azure AD Identity Protection est déployé et obtient des données, les données d’alerte peuvent facilement être diffusées en continu dans Azure Sentinel.


1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette **Azure AD Identity Protection**.

2. Cliquez sur **Connecter** pour démarrer la diffusion en continu d’événements Azure AD Identity Protection dans Azure Sentinel.


6. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes d’Azure AD Identity Protection, recherchez **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure AD Identity Protection à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
