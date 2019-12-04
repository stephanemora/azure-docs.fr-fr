---
title: Connexion des données Azure AD Identity Protection à Azure Sentinel | Microsoft Docs
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
ms.date: 11/17/2019
ms.author: rkarlin
ms.openlocfilehash: 5d73ce12d509b2ccc534e19a27124980f3b60044
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158886"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Connecter des données depuis Azure AD Identity Protection



Vous pouvez diffuser des journaux en continu depuis [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) vers Azure Sentinel pour transmettre en continu des alertes à Azure Sentinel et afficher des tableaux de bord, créer des alertes personnalisées et améliorer les examens. Azure Active Directory Identity Protection fournit une vue consolidée des utilisateurs à risque, des détections de risques et des vulnérabilités, et permet d’éliminer ces risques immédiatement et de définir des stratégies de remédiation automatique pour les événements futurs. Le service repose sur l’expérience de Microsoft en matière de protection des identités des consommateurs et bénéficie d’une précision spectaculaire grâce à plus de 13 milliards d’ouvertures de session par jour. 


## <a name="prerequisites"></a>Prérequis

- Possession d’une [licence Azure Active Directory Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Utilisateur, doté d’autorisations d’administrateur général ou d’administrateur de la sécurité


## <a name="connect-to-azure-ad-identity-protection"></a>Connexion à Azure AD Identity Protection

Si vous avez déjà Azure AD Identity Protection, assurez-vous qu’il est [activé sur votre réseau](../active-directory/identity-protection/overview-identity-protection.md).
Si Azure AD Identity Protection est déployé et obtient des données, les données d’alerte peuvent facilement être diffusées en continu dans Azure Sentinel.


1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** puis cliquez sur la vignette **Azure AD Identity Protection**.

2. Cliquez sur **Connecter** pour démarrer la diffusion en continu d’événements Azure AD Identity Protection dans Azure Sentinel.


6. Pour utiliser le schéma pertinent dans Log Analytics pour les alertes d’Azure AD Identity Protection, recherchez **SecurityAlert**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure AD Identity Protection à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
