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
ms.openlocfilehash: 4a730c340ea4d1e1137a7449c6d1005ea59917bf
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814006"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Modifier les valeurs par défaut de la durée de vie des jetons pour une application personnalisée

Azure AD Premium permet aux développeurs d’applications et aux administrateurs de locataires de configurer la durée de vie des jetons émis pour les clients non confidentiels. Les stratégies de durée de vie des jetons sont définies à l’échelle d’un locataire ou en fonction des ressources accessibles.

1. Pour définir une stratégie de durée de vie de jeton, vous devez télécharger le [module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Exécutez la commande **Connect-AzureAD -Confirm**.

    Voici un exemple de stratégie qui définit le jeton d’actualisation à facteur unique d’âge maximal. Créez la stratégie : ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Étapes suivantes

* Consultez l’article [Configurable token lifetimes in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) (Durées de vie configurables des jetons dans Azure AD) pour découvrir comment configurer les durées de vie des jetons émis par Azure AD, notamment comment définir les durées de vie des jetons pour toutes les applications de votre organisation, pour une application mutualisée ou pour un principal de service spécifique de votre organisation. 
* [Référence sur les jetons Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

