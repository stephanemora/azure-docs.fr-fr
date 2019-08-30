---
title: 'Stratégie de référence : exiger l’authentification MFA pour le management des services (préversion) - Azure Active Directory'
description: Stratégie d’accès conditionnel pour exiger l’authentification MFA pour Azure Resource Manager
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
ms.openlocfilehash: e8095b4fa6e52b7c34cedaea35b129ab68dddc65
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532958"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Stratégie de référence : exiger l’authentification MFA pour le management des services (préversion)

Vous utilisez probablement différents services Azure dans votre organisation. Ces services peuvent être gérés via l’API Azure Resource Manager :

* Portail Azure
* Azure PowerShell
* D’Azure CLI

Utiliser Azure Resource Manager pour gérer vos services est une action très privilégiée. Azure Resource Manager peut modifier des configurations à l’échelle du locataire, telles que les paramètres de service et la facturation de l’abonnement. L’authentification à facteur unique est vulnérable à diverses attaques comme le hameçonnage et la pulvérisation de mots de passe. Par conséquent, il est important de vérifier l’identité des utilisateurs qui veulent accéder à Azure Resource Manager et mettre à jour des configurations. Pour cela, exigez une authentification multifacteur avant d’accorder l’accès.

**Exiger l’authentification MFA pour le management des services** est une [stratégie de référence](concept-baseline-protection.md) qui demandera à chaque utilisateur de s’authentifier par MFA avant d’accéder au portail Azure, Azure PowerShell ou Azure CLI. Cette stratégie s’applique à tous les utilisateurs accédant Azure Resource Manager, y compris s’ils ont un rôle Administrateur.

Une fois que cette stratégie est activée dans un locataire, tous les utilisateurs qui se connectent à des ressources de gestion Azure seront confrontés à une authentification multifacteur. Si l’utilisateur n’est pas inscrit pour une authentification MFA, celui-ci devra s’inscrire à l’aide de l’application d’authentification Microsoft Authenticator pour continuer.

Pour effectuer une connexion interactive à l’aide d’[Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps), utilisez l’applet de commande [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

```PowerShell
Connect-AzAccount
```

Lorsque vous l’exécutez, cette cmdlet présente une chaîne de jeton. Pour vous connecter, copiez cette chaîne et collez-la dans [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) dans un navigateur. Votre session PowerShell est ainsi authentifiée pour vous connecter à Azure.

Pour effectuer une connexion interactive à l’aide d’[Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest), exécutez la commande[az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login).

```azurecli
az login
```

Si l’interface CLI peut ouvrir votre navigateur par défaut, elle le fera et chargera une page de connexion par la même occasion. Sinon, vous devez ouvrir une page de navigateur et suivre les instructions sur la ligne de commande pour entrer un code d’autorisation après avoir accédé à [https://aka.ms/devicelogin](https://aka.ms/devicelogin) dans votre navigateur. Ensuite, connectez-vous avec les informations d’identification de votre compte dans le navigateur.

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

Comme la stratégie **Exiger l’authentification MFA pour le management des services** s’applique à tous les utilisateurs Azure Resource Manager, vous devez tenir compte de différentes considérations pour assurer un déploiement harmonieux. Ces considérations incluent l’identification, dans Azure AD, des utilisateurs et des principes de service qui ne peuvent pas ou ne doivent pas utiliser l’authentification multifacteur, ainsi que des applications et clients utilisés par votre organisation qui ne prennent pas en charge l’authentification moderne.

## <a name="enable-the-baseline-policy"></a>Activer la stratégie de référence

La stratégie **Stratégie de référence : exiger l’authentification MFA pour le management des services (préversion)** est préconfigurée et s’affiche sur la partie supérieure de la fenêtre lorsque vous accédez au panneau Accès conditionnel dans le portail Azure.

Pour activer cette stratégie et protéger vos administrateurs, procédez comme suit :

1. Connectez-vous au **portail Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Accès conditionnel**.
1. Dans la liste des stratégies, sélectionnez **Stratégie de référence : exiger l’authentification MFA pour le management des services (préversion)** .
1. Définissez **Activer la stratégie** sur **Utiliser la stratégie immédiatement**.
1. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

* [Stratégies de protection de base de référence pour l’accès conditionnel](concept-baseline-protection.md)
* [Cinq étapes pour sécuriser votre infrastructure d’identité](../../security/fundamentals/steps-secure-identity.md)
* [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](overview.md)