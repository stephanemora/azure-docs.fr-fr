---
title: Exemple PowerShell – Applications Proxy d’application avec des certificats identiques
description: Exemple PowerShell listant toutes les applications Proxy d’application Azure Active Directory (Azure AD) qui ont été publiées avec le même certificat.
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
ms.openlocfilehash: 39a116cdb8900c5adb1689c6b81649b1963e4fe6
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511131"
---
# <a name="get-all-azure-ad-proxy-application-apps-that-are-published-with-the-identical-certificate"></a>Récupérer toutes les applications Proxy d’application Azure AD publiées avec le même certificat

Cet exemple de script PowerShell liste toutes les applications Proxy d’application Azure Active Directory (Azure AD) qui ont été publiées avec le même certificat.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Cet exemple requiert le [module AzureAD v2 PowerShell pour Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou la [préversion du module AzureAD v2 PowerShell pour Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exemple de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>Explication du script

| Commande | Notes |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Permet d’obtenir un principal de service. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Permet d’obtenir une application Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Permet de récupérer une application configurée pour le proxy d’application dans Azure AD. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le Module Azure AD PowerShell, consultez [Présentation du Module Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Pour d’autres exemples PowerShell pour le proxy d’application, consultez [Exemples Azure AD PowerShell pour le Proxy d’application Azure Active Directory](../application-proxy-powershell-samples.md).