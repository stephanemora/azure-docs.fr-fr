---
title: Exemple PowerShell - Exportez les applications avec des secrets et des certificats arrivant à expiration au-delà de la date imposée vers le locataire Azure Active Directory.
description: Exemple PowerShell qui exporte tous les secrets et certificats arrivant à expiration au-delà de la date imposée pour les applications spécifiées vers votre locataire Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 692ab2cfd480fd15760c13c63922fe76d23058ea
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375387"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Exporter les applications avec des secrets et des certificats arrivant à expiration au-delà de la date imposée

Cet exemple de script PowerShell exporte tous les secrets et certificats d’inscriptions d’applications arrivant à expiration au-delà d’une période requise pour les applications spécifiées, de votre annuaire vers un fichier CSV, de manière non interactive.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Explication du script

Ce script fonctionne de manière non interactive. L’administrateur qui l’utilise doit modifier les valeurs de la section « #PARAMETERS TO CHANGE » avec ses propres ID d’application, secret d’application, nom de locataire, période d’expiration des informations d’identification des applications et chemin d’exportation du fichier CSV.
Ce script utilise le [flux OAuth Client_Credential](../../develop/v2-oauth2-client-creds-grant-flow.md) La fonction « RefreshToken » génère le jeton d’accès en fonction des valeurs des paramètres modifiés par l’administrateur.

La commande « Add-Member » est chargée de créer les colonnes dans le fichier CSV.

| Commande | Notes |
|---|---|
| [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | Envoie des requêtes HTTP et HTTPS à une page web ou à un service web. Elle analyse la réponse et retourne des collections de liens, images et autres éléments HTML significatifs. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le Module Azure AD PowerShell, consultez [Présentation du Module Azure AD PowerShell](/powershell/azure/active-directory/overview).

Pour d’autres exemples PowerShell de gestion d’applications, consultez [Exemples PowerShell pour la gestion d’applications Azure AD](../app-management-powershell-samples.md).
