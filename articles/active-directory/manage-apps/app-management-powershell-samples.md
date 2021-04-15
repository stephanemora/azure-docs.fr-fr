---
title: Exemples PowerShell pour la gestion d’applications Azure Active Directory
description: Ces exemples PowerShell sont utilisés pour les applications que vous gérez dans votre locataire Azure Active Directory. Vous pouvez utiliser ces exemples de scripts pour rechercher des informations relatives à l’expiration des secrets et des certificats.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 6b6314935bfafc2fe6288c30619e1d01242a991d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378820"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Exemples PowerShell pour la gestion d’applications Azure Active Directory

Le tableau suivant contient des liens vers des exemples de scripts PowerShell pour la gestion d’applications Azure AD. Ces exemples nécessitent :
- Le [module AzureAD V2 PowerShell pour Graph](/powershell/azure/active-directory/install-adv2) ou,
- La [préversion du module AzureAD V2 PowerShell pour Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), sauf indication contraire.

Pour plus d’informations sur les applets de commande utilisées dans ces exemples, consultez [Applications](/powershell/module/azuread/#applications).

| Lien | Description |
|---|---|
|**Scripts de gestion d’applications**||
| [Exporter des secrets et des certificats (inscriptions d’applications)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Exportez les secrets et certificats relatifs aux inscriptions d’applications vers le locataire Azure Active Directory. |
| [Exporter des secrets et des certificats (applications d’entreprise)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Exportez les secrets et certificats relatifs aux applications d’entreprise vers le locataire Azure Active Directory. |
| [Exporter des secrets et des certificats arrivant à expiration](scripts/powershell-export-apps-with-expriring-secrets.md) | Exportez des inscriptions d’applications avec des secrets et des certificats arrivant à expiration et leurs propriétaires vers un locataire Azure Active Directory. |
| [Exporter les secrets et certificats arrivant à expiration au-delà de la date imposée](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Exportez des inscriptions d’applications avec des secrets et des certificats arrivant à expiration au-delà de la date requise vers un locataire Azure Active Directory. Cela utilise le flux Oauth Client_Credentials non interactif. |
