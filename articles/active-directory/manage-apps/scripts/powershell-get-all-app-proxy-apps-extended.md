---
title: Exemple PowerShell - Répertorier des informations étendues pour les applications de proxy d’application
description: Exemple PowerShell qui répertorie toutes les applications de proxy d’application Azure Active Directory (Azure AD), ainsi que l’ID d’application (AppId), le nom (DisplayName), l’URL externe (ExternalUrl), l’URL interne (InternalUrl) et le type d’authentification (ExternalAuthenticationType).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: ccd0c7be7fd0dd533028faa0dc2bbdad30d74c79
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258691"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Obtenir toutes les applications de proxy d’application et répertorier les informations étendues

Cet exemple de script PowerShell liste des informations sur toutes les applications de proxy d’application Azure Active Directory (Azure AD), notamment l’ID d’application (AppId), le nom (DisplayName), l’URL externe (ExternalUrl), l’URL interne (InternalUrl), le type d’authentification (ExternalAuthenticationType), le mode SSO et d’autres paramètres.

Tout changement de la valeur de la variable $ssoMode active une sortie filtrée par le mode SSO. De plus amples détails sont documentés dans le script.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Cet exemple nécessite le [module AzureAD V2 PowerShell pour Graph](/powershell/azure/active-directory/install-adv2) (AzureAD).

## <a name="sample-script"></a>Exemple de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Explication du script

| Commande | Notes |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Permet d’obtenir un principal de service. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Permet d’obtenir une application Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Permet de récupérer une application configurée pour le proxy d’application dans Azure AD. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le Module Azure AD PowerShell, consultez [Présentation du Module Azure AD PowerShell](/powershell/azure/active-directory/overview).

Pour d’autres exemples PowerShell pour le proxy d’application, consultez [Exemples Azure AD PowerShell pour le Proxy d’application Azure Active Directory](../application-proxy-powershell-samples.md).
