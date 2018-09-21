---
title: Modifier les valeurs par défaut de la durée de vie des jetons pour une application personnalisée | Microsoft Docs
description: Comment mettre à jour les stratégies de durée de vie des jetons pour l’application que vous développez sur Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 059920c710b202e22a22f8431c536c5dfa19f2b9
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723809"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Modifier les valeurs par défaut de la durée de vie des jetons pour une application personnalisée

Azure AD Premium permet aux développeurs d’applications et aux administrateurs de locataires de configurer la durée de vie des jetons émis pour les clients non confidentiels. Les stratégies de durée de vie des jetons sont définies à l’échelle d’un locataire ou en fonction des ressources accessibles.

 * Pour définir une stratégie de durée de vie de jeton, vous devez télécharger le [module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

 * Exécutez la commande **Connect-AzureAD -Confirm**.

 * Voici un exemple de stratégie qui définit le jeton d’actualisation à facteur unique d’âge maximal. Créez la stratégie : ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Consultez l’article [Durées de vie des jetons configurables dans Azure Active Directory (version préliminaire publique)](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) pour apprendre à créer d’autres stratégies personnalisées.

## <a name="next-steps"></a>Étapes suivantes
[Durées de vie des jetons configurables dans Azure Active Directory (version préliminaire publique)](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Référence sur les jetons Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

