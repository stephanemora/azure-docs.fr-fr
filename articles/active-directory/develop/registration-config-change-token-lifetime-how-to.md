---
title: Changer les valeurs par défaut de durée de vie des jetons pour les applications Azure AD personnalisées
description: Comment mettre à jour les stratégies de durée de vie des jetons pour l’application que vous développez sur Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 8de6a7aafdd402e4ee75862e69ac60af3af0e041
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88114929"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Modifier les valeurs par défaut de la durée de vie des jetons pour une application personnalisée

Cet article explique comment utiliser Azure AD PowerShell pour définir une stratégie de durée de vie du jeton. Azure AD Premium permet aux développeurs d’applications et aux administrateurs de locataires de configurer la durée de vie des jetons émis pour les clients non confidentiels. Les stratégies de durée de vie des jetons sont définies à l’échelle d’un locataire ou en fonction des ressources accessibles.

1. Pour définir une stratégie de durée de vie de jeton, vous devez télécharger le [module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Exécutez la commande **Connect-AzureAD -Confirm**.

    Voici un exemple de stratégie qui définit le jeton d’actualisation à facteur unique d’âge maximal. Créez la stratégie : ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Étapes suivantes

* Consultez l’article [Configurable token lifetimes in Azure AD](./active-directory-configurable-token-lifetimes.md) (Durées de vie configurables des jetons dans Azure AD) pour découvrir comment configurer les durées de vie des jetons émis par Azure AD, notamment comment définir les durées de vie des jetons pour toutes les applications de votre organisation, pour une application mutualisée ou pour un principal de service spécifique de votre organisation. 
* [Référence sur les jetons Azure AD](./id-tokens.md)