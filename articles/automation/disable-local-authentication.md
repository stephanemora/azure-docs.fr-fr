---
title: Désactiver l’authentification locale dans Azure Automation
description: Cet article décrit la désactivation de l’authentification locale dans Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: 54da805b7f4c2332d0d475d2c5eb31b24fca597f
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165655"
---
# <a name="disable-local-authentication-in-automation"></a>Désactiver l’authentification locale dans Automation

Azure Automation fournit la prise en charge de l’authentification Microsoft Azure Active Directory (Azure AD) pour tous les points de terminaison publics du service Automation. Cette amélioration critique de la sécurité supprime les dépendances de certificat et offre aux organisations le contrôle de la désactivation des méthodes d’authentification locales. Cette fonctionnalité vous offre une intégration transparente lorsque vous avez besoin de contrôler et de gérer de manière centralisée les identités et les informations d’identification des ressources via Azure AD.

Azure Automation fournit une fonctionnalité facultative pour « désactiver l’authentification locale » au niveau du compte Automation à l’aide de la stratégie Azure [Configurer le compte Azure Automation pour désactiver l’authentification locale](../automation/policy-reference.md#azure-automation). Par défaut, cet indicateur est défini sur false au niveau du compte. Vous pouvez donc utiliser l’authentification locale et l’authentification Azure AD. Si vous choisissez de désactiver l’authentification locale, le service Automation n’accepte que l’authentification Azure AD.

Dans le portail Azure, vous pouvez recevoir un message d’avertissement sur la page d’accueil du compte Automation sélectionné si l’authentification est désactivée.Pour confirmer l’activation de la stratégie d’authentification locale, utilisez la cmdlet PowerShell [Get-AzAutomationAccount](/powershell/module/az.automation/get-azautomationaccount) et vérifiez la propriété `DisableLocalAuth`. La valeur `true` indique que l’authentification locale est désactivée.

La désactivation de l’authentification locale ne prend pas effet immédiatement. Patientez quelques minutes avant que le service bloque les demandes d’authentification ultérieures.
 
## <a name="re-enable-local-authentication"></a>Réactiver l’authentification locale

Pour réactiver l’authentification locale, exécutez la cmdlet PowerShell `Set-AzAutomationAccount` avec le paramètre `-DisableLocalAuth false`.Patientez quelques minutes avant que le service accepte la modification afin d’autoriser les demandes d’authentification locales. 

## <a name="compatibility"></a>Compatibilité

Le tableau suivant décrit les comportements ou les fonctionnalités qui ne peuvent pas fonctionner si l’authentification locale est désactivée.

|Scénario | Alternative |
|---|---|
|Démarrage d’un runbook à l’aide d’un webhook. | Démarrez un travail de runbook à l’aide d’un modèle Azure Resource Manager, qui utilise l’authentification Azure AD. |
|Utilisation d’Automation Desired State Configuration.| Utilisez la [Configuration Invité d’Azure Policy](../governance/policy/concepts/guest-configuration.md).  |
|Utilisation de Runbook Workers hybrides basés sur des agents.| Utilisez des [Runbook Workers hybrides basés sur des extensions (préversion)](./extension-based-hybrid-runbook-worker-install.md).|

## <a name="limitations"></a>Limites

La mise à jour corrective Update Management ne fonctionne pas lorsque l’authentification locale est désactivée.


## <a name="next-steps"></a>Étapes suivantes
- [Vue d’ensemble de l’authentification de compte Azure Automation](./automation-security-overview.md)
