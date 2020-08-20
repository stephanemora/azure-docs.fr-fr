---
title: Exemple PowerShell - Répertorier toutes les applications de proxy d’application avec une stratégie
description: Exemple PowerShell qui répertorie toutes les applications de proxy d’application Azure Active Directory (Azure AD) dans votre répertoire qui ont une stratégie de jeton de durée de vie.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: aa66b842007d9471828171c44c2dcb7505e8b4d7
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506855"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>Obtenir toutes les applications de proxy d’application avec une stratégie de durée de vie de jeton

Cet exemple de script PowerShell répertorie toutes les applications de proxy d’application Azure Active Directory (Azure AD) dans votre répertoire qui ont une stratégie de jeton de durée de vie de jeton et répertorie des détails sur la stratégie.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Cet exemple nécessite la [version en préversion du module AzureAD v2 PowerShell pour Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exemple de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>Explication du script

| Commande | Notes |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Obtient un principal de service. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Permet d’obtenir une application Azure AD. |
|[Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) | Obtient une stratégie dans Azure AD. |
|[Get-AzureADServicePrincipalPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview) | Obtient la stratégie d’un principal de service dans Azure AD. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le Module Azure AD PowerShell, consultez [Présentation du Module Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Pour d’autres exemples PowerShell pour le proxy d’application, consultez [Exemples Azure AD PowerShell pour le Proxy d’application Azure Active Directory](../application-proxy-powershell-samples.md).