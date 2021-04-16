---
title: Exemple PowerShell - Répertorier tous les groupes de connecteurs de proxy d’application
description: Exemple PowerShell qui répertorie tous les connecteurs et groupes de connecteurs de proxy d’application Azure Active Directory (Azure AD) dans votre répertoire.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 032d4930e29126ec9636e710f016d9612dc2cda0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377562"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Récupérer tous les connecteurs et groupes de connecteurs dans le répertoire

Cet exemple de script PowerShell répertorie tous les connecteurs et groupes de connecteurs de proxy d’application Azure Active Directory (Azure AD) dans votre répertoire.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Cet exemple requiert le [module AzureAD v2 PowerShell pour Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou la [préversion du module AzureAD v2 PowerShell pour Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Exemple de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Explication du script

| Commande | Notes |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Dresse la liste de tous les groupes de connecteurs, ou si cela est spécifié, fournit les détails du groupe de connecteurs spécifié. |
| [Get-AzureADApplicationProxyConnectorGroupMembers](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers) | Obtient tous les connecteurs de proxy d’application associés à chaque groupe de connecteurs.|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le Module Azure AD PowerShell, consultez [Présentation du Module Azure AD PowerShell](/powershell/azure/active-directory/overview).

Pour d’autres exemples PowerShell pour le proxy d’application, consultez [Exemples Azure AD PowerShell pour le Proxy d’application Azure Active Directory](../application-proxy-powershell-samples.md).
