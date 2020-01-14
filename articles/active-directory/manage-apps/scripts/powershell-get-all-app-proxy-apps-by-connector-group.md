---
title: Exemple PowerShell - Répertorier tous les groupes de connecteurs de proxy d'application
description: Exemple PowerShell qui répertorie tous les groupes de connecteurs de proxy d'application Azure Active Directory (Azure AD) auxquels les applications ont été attribuées.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dcf2eb6a1904e4a7223a19e598f0c06ddc36f71
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475655"
---
# <a name="get-all-application-proxy-apps-and-list-by-connector-group"></a>Obtenir toutes les applications de proxy d'application et répertorier par groupe de connecteurs

Ce script PowerShell répertorie les informations relatives à tous les groupes de connecteurs de proxy d'application Azure Active Directory (Azure AD) auxquels les applications ont été attribuées.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Cet exemple requiert le [module AzureAD v2 PowerShell pour Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) ou la [préversion du module AzureAD v2 PowerShell pour Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exemple de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-by-connectorgroup.ps1 "Get all Application Proxy Connector groups with the assigned applications")]

## <a name="script-explanation"></a>Explication du script

| Commande | Notes |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Permet d'obtenir un principal de service. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Permet d'obtenir une application Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Permet de récupérer une application configurée pour le proxy d'application dans Azure AD. |
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Dresse la liste de tous les groupes de connecteurs, ou si cela est spécifié, fournit les détails du groupe de connecteurs spécifié. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur le module Azure AD PowerShell, consultez [Présentation du module Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Pour accéder à d'autres exemples PowerShell sur le proxy d'application, consultez [Exemples Azure AD PowerShell pour le proxy d'application Azure AD](../application-proxy-powershell-samples.md).