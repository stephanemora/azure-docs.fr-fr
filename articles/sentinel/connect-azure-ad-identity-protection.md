---
title: Connexion des données de Azure AD Identity Protection à Azure en version préliminaire Sentinel | Microsoft Docs
description: Découvrez comment connecter des données d’Azure AD Identity Protection à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d8306056655809c69af2ed39b5bbf8efebe05d3f
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496161"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Connectez des données à partir d’Azure AD Identity Protection

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser des journaux à partir de [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) dans Azure Sentinel aux alertes de flux de données dans Azure Sentinel pour afficher des tableaux de bord, créer des alertes personnalisées et améliore l’examen. Azure Active Directory Identity Protection fournit une vue consolidée sur les utilisateurs à risque, les événements à risque et les vulnérabilités, avec la possibilité de résoudre ces risques immédiatement et définir des stratégies de mise à jour automatique des événements futurs. Le service repose sur la protection des identités des consommateurs de l’expérience de Microsoft et développe une précision de signal avec plus de 13 milliards ouvertures de session jour. 


## <a name="prerequisites"></a>Conditions préalables

- Vous devez avoir un [licence Azure Active Directory Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Utilisateur avec l’administrateur général ou d’autorisations d’administrateur de sécurité


## <a name="connect-to-azure-ad-identity-protection"></a>Se connecter à Azure AD Identity Protection

Si vous avez déjà Azure AD Identity Protection, assurez-vous qu’il est [activé sur votre réseau](../active-directory/identity-protection/enable.md).
Si Azure AD Identity Protection est déployée et obtention de données, les données d’alerte peuvent facilement être diffusé en continu dans Azure Sentinel.


1. Dans Azure Sentinel, sélectionnez **connecteurs de données** puis cliquez sur le **Azure AD Identity Protection** vignette.

2. Cliquez sur **Connect** pour démarrer la diffusion en continu d’événements Azure AD Identity Protection dans Azure Sentinel.


6. Pour utiliser le schéma pertinent dans Analytique de journal pour les alertes d’Azure AD Identity Protection, recherchez **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure AD Identity Protection pour Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).
