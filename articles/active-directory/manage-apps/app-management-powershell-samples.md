---
title: Exemples PowerShell pour la gestion d’applications Azure Active Directory
description: Ces exemples PowerShell sont utilisés pour les applications que vous gérez dans votre locataire Azure Active Directory. Vous pouvez utiliser ces exemples de scripts pour rechercher des informations relatives à l’expiration des secrets et des certificats.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: davidmu
ms.reviewer: mifarca
ms.openlocfilehash: c0e6969b0d6814e74e99a6ad893284bc407b269d
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568421"
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
