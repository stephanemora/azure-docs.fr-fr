---
title: Stratégie de référence nécessitent une authentification Multifacteur pour la gestion de service (version préliminaire) - Azure Active Directory
description: Stratégie d’accès conditionnel pour exiger l’authentification Multifacteur pour Azure Resource Manager
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00ed40bef3f3cbe59825f546ffa39c3ebfb2e41f
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003175"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Stratégie de référence : Exiger l’authentification Multifacteur pour la gestion de service (version préliminaire)

Vous utilisez peut-être une variété de services Azure dans votre organisation. Ces services peuvent être gérés via l’API Azure Resource Manager :

* Portail Azure
* Azure PowerShell
* Azure CLI

À l’aide d’Azure Resource Manager pour gérer vos services est une action disposant de privilèges élevés. Azure Resource Manager peut modifier les configurations au niveau du locataire, telles que les paramètres de service et de facturation d’abonnement. Authentification unique est vulnérable à une variété d’attaques de type pulvérisation anti-hameçonnage et de mot de passe. Par conséquent, il est important de vérifier l’identité des utilisateurs qui veulent accéder à Azure Resource Manager et de mettre à jour des configurations, en exigeant l’authentification multifacteur avant d’autoriser l’accès.

**Exiger l’authentification Multifacteur pour la gestion de service** est un [stratégie de référence](concept-baseline-protection.md) qui nécessiteront l’authentification Multifacteur pour tout utilisateur accédant au portail Azure, Azure PowerShell ou Azure CLI. Cette stratégie s’applique à tous les utilisateurs l’accès à Azure Resource Manager, indépendamment de si elles sont un administrateur.

Une fois que cette stratégie est activée dans un locataire, tous les utilisateurs qui se connectent à des ressources de gestion Azure seront mis au défi avec l’authentification multifacteur. Si l’utilisateur n’est pas inscrit pour l’authentification Multifacteur, l’utilisateur devront s’inscrire à l’aide de l’application Microsoft Authenticator pour pouvoir continuer.

![Exiger l’authentification Multifacteur pour Azure Resource Manager](./media/howto-baseline-protect-azure/baseline-policy-require-mfa-for-service-management.png)

Pour effectuer interactive connectez-vous à l’aide de [Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps), utilisez le [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) applet de commande.

```PowerShell
Connect-AzAccount
```

Lorsque vous l’exécutez, cette cmdlet présente une chaîne de jeton. Pour vous connecter, copiez cette chaîne et collez-la dans [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) dans un navigateur. Votre session PowerShell est ainsi authentifiée pour vous connecter à Azure.

Pour effectuer interactive connectez-vous à l’aide de [Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest), exécutez le [connexion de az](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) commande.

```azurecli
az login
```

Si l’interface CLI peut ouvrir votre navigateur par défaut, elle le fera et chargera une page de connexion par la même occasion. Sinon, vous devez ouvrir une page de navigateur et suivez les instructions sur la ligne de commande à entrer un code d’autorisation après avoir navigué à [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) dans votre navigateur. Ensuite, connectez-vous à vos informations d’identification de compte dans le navigateur.

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

Étant donné que le **requièrent une authentification Multifacteur pour la gestion de service** stratégie s’applique à tous les utilisateurs d’Azure Resource Manager, plusieurs considérations doivent être apportées pour garantir un déploiement sans heurts. Ces considérations incluent l’identification des utilisateurs et les principes de service dans Azure AD qui ne peuvent pas ou ne devez pas effectuer d’authentification Multifacteur, ainsi que les applications et les clients utilisés par votre organisation qui ne prennent pas en charge l’authentification moderne.

### <a name="user-exclusions"></a>Exclusions de l’utilisateur

Cette stratégie de référence vous offre la possibilité d’exclure les utilisateurs. Avant d’activer la stratégie pour votre client, nous recommandons à l’exclusion des comptes suivants :

* **Accès d’urgence** ou **secours** comptes afin d’éviter le verrouillage de compte de l’échelle du client. Dans le scénario improbable où tous les administrateurs sont verrouillés en dehors de votre locataire, votre compte d’administration d’accès d’urgence peut être utilisé pour vous connecter à la procédure client de récupérer l’accès.
   * Vous trouverez plus d’informations dans l’article [gérer les comptes d’accès d’urgence dans Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Comptes de service** et **principes de service**, telles que le compte de synchronisation de connexion AD Azure. Comptes de service sont des comptes non interactifs qui ne sont pas liés à un utilisateur spécifique. Ils sont normalement utilisés par les services back-end et autorisent l’accès par programmation aux applications. Comptes de service doivent être exclus, car l’authentification Multifacteur ne peut pas être effectuée par programme.
   * Si votre organisation dispose de ces comptes en cours d’utilisation dans des scripts ou du code, envisagez d’en les remplaçant par [gérés identités](../managed-identities-azure-resources/overview.md). En tant que solution de contournement temporaire, vous pouvez exclure ces comptes spécifiques à partir de la stratégie de référence.
* Utilisateurs qui n’ont pas ou ne sera pas en mesure d’utiliser un Smartphone.
   * Cette stratégie nécessite que les utilisateurs de s’inscrire à MFA à l’aide de l’application Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Activer la stratégie de référence

La stratégie **stratégie de référence : Exiger l’authentification Multifacteur pour la gestion de service (version préliminaire)** est préconfigurée et s’affichera en haut lorsque vous accédez au panneau d’accès conditionnel dans le portail Azure.

Pour activer cette stratégie et protéger vos administrateurs :

1. Se connecter à la **Azure portal** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **accès conditionnel**.
1. Dans la liste des stratégies, sélectionnez **stratégie de référence : Exiger l’authentification Multifacteur pour la gestion de service (version préliminaire)**.
1. Définissez **activer la stratégie** à **utiliser immédiatement la stratégie**.
1. Ajoutez les exclusions d’utilisateur en cliquant sur **utilisateurs** > **sélectionner les utilisateurs exclus** et en choisissant les utilisateurs qui doivent être exclues. Cliquez sur **sélectionnez** puis **fait**.
1. Cliquez sur **enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

* [Stratégies de protection d’accès conditionnel de base](concept-baseline-protection.md)
* [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/azure-ad-secure-steps.md)
* [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](overview.md)