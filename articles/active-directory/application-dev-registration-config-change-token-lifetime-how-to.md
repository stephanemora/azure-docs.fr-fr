---
title: Modifier les valeurs par défaut de la durée de vie des jetons pour une application personnalisée | Microsoft Docs
description: Comment mettre à jour les stratégies de durée de vie des jetons pour l’application que vous développez sur Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: bd963253ee796fb56405e8da855475df2d2f5d08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335337"
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

