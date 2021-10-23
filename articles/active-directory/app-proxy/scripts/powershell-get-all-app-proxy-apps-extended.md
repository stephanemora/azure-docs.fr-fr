---
title: Exemple PowerShell - Lister les informations étendues des applications Proxy d’application Azure Active Directory
description: Exemple PowerShell qui répertorie toutes les applications de proxy d’application Azure Active Directory (Azure AD), ainsi que l’ID d’application (AppId), le nom (DisplayName), l’URL externe (ExternalUrl), l’URL interne (InternalUrl) et le type d’authentification (ExternalAuthenticationType).
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: sample
ms.date: 04/29/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 16fd5ac821f09363249f904b14ac836b063e1b25
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988123"
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
