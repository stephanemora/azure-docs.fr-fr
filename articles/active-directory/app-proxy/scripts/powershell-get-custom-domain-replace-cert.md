---
title: Exemple PowerShell - Remplacer un certificat dans des applications Proxy d’application Azure Active Directory
description: Exemple PowerShell qui remplace en bloc le certificat de toutes les applications Proxy d’application Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: sample
ms.date: 04/29/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 7897cc324095c38e6b626c2057bd2bbca921b397
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293652"
---
# <a name="get-all-azure-active-directory-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>Récupérer toutes les applications Proxy d’application Azure Active Directory ayant été publiées avec le même certificat et remplacer ce certificat

Cet exemple de script PowerShell vous permet de remplacer en bloc le certificat de toutes les applications Proxy d’application Azure Active Directory (Azure AD) qui ont été publiées avec le même certificat.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Cet exemple requiert le [module AzureAD v2 PowerShell pour Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou la [préversion du module AzureAD v2 PowerShell pour Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Exemple de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-replace-cert.ps1 "Get all Application Proxy applications published with the identical certificate and replace it")]

## <a name="script-explanation"></a>Explication du script

| Commande | Notes |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Permet d’obtenir un principal de service. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Permet d’obtenir une application Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Permet de récupérer une application configurée pour le proxy d'application dans Azure AD. |
|[Set-AzureADApplicationProxyApplicationCustomDomainCertificate](/powershell/module/azuread/set-azureadapplicationproxyapplicationcustomdomaincertificate) | Attribue un certificat à une application configurée pour le proxy d’application dans Azure AD. Cette commande charge le certificat et permet à l’application d’utiliser des domaines personnalisés. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le Module Azure AD PowerShell, consultez [Présentation du Module Azure AD PowerShell](/powershell/azure/active-directory/overview).

Pour d’autres exemples PowerShell pour le proxy d’application, consultez [Exemples Azure AD PowerShell pour le Proxy d’application Azure Active Directory](../application-proxy-powershell-samples.md).
