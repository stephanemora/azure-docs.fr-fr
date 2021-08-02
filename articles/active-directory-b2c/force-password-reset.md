---
title: Configurer un flux de réinitialisation forcée du mot de passe dans Azure AD B2C
titleSuffix: Azure AD B2C
description: Découvrez comment configurer un flux de réinitialisation forcée du mot de passe dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/10/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0944dfd177ba82680b315913046ce8a4103f1156
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111960608"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Configurer un flux de réinitialisation forcée du mot de passe dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="overview"></a>Vue d’ensemble

En tant qu’administrateur, vous pouvez [réinitialiser le mot de passe d’un utilisateur](manage-users-portal.md#reset-a-users-password) qui viendrait à l’oublier. Ou si vous souhaitez les forcer à réinitialiser le mot de passe. Cet article explique comment forcer une réinitialisation de mot de passe dans ces scénarios.

Quand un administrateur réinitialise le mot de passe d’un utilisateur via le portail Azure, la valeur de l’attribut [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) est définie sur `true`. Le [parcours de connexion et d’inscription](add-sign-up-and-sign-in-policy.md) vérifie la valeur de cet attribut. Une fois que l’utilisateur s’est connecté, si l’attribut a la valeur `true`, l’utilisateur doit réinitialiser son mot de passe. L’attribut repasse ensuite à la valeur `false`.

![Flux de réinitialisation forcée du mot de passe](./media/force-password-reset/force-password-reset-flow.png)

Le flux de réinitialisation du mot de passe s’applique aux comptes locaux dans Azure AD B2C qui utilisent une [adresse e-mail](identity-provider-local.md#email-sign-in) ou un [nom d’utilisateur](identity-provider-local.md#username-sign-in) avec un mot de passe pour la connexion.

::: zone pivot="b2c-user-flow"

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-user-flow"></a>Configuration du flux d’utilisateur

Pour activer le paramètre de **Réinitialisation forcée du mot de passe** dans un flux d’utilisateur d’inscription ou de connexion :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**.
1. Sélectionnez le flux d’utilisateur d’inscription et de connexion ou le flux d’utilisateur de connexion (de type **Recommandé**) que vous souhaitez personnaliser.
1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Propriétés**.
1. Sous **Configuration du mot de passe**, sélectionnez **Réinitialisation forcée du mot de passe**.
1. Sélectionnez **Enregistrer**.

## <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur d’utilisateurs ou administrateur de mot de passe. Pour plus d’informations sur les rôles disponibles, consultez [Assigner des rôles d’administrateur dans Azure Active Directory](../active-directory/roles/permissions-reference.md#all-roles).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Utilisateurs**. Recherchez et sélectionnez l’utilisateur à utiliser pour tester la réinitialisation du mot de passe, puis sélectionnez **Réinitialiser le mot de passe**.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Flux d’utilisateurs**.
1. Sélectionnez un flux d’utilisateur d’inscription ou de connexion (de type **Recommandé**) que vous souhaitez tester.
1. Sélectionnez **Exécuter le flux utilisateur**.
1. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
1. Sélectionnez **Exécuter le flux utilisateur**.
1. Connectez-vous avec le compte d’utilisateur pour lequel vous réinitialisez le mot de passe.
1. Vous devez maintenant modifier le mot de passe pour l’utilisateur. Changez le mot de passe et sélectionnez **Continuer**. Le jeton est envoyé à `https://jwt.ms` et vous devez le voir.

## <a name="force-password-reset-on-next-login"></a>Réinitialisation forcée du mot de passe à la connexion suivante

Pour forcer la réinitialisation du mot de passe à la connexion suivante, mettez à jour le profil de mot de passe du compte avec l’opération [Mettre à jour l’utilisateur](/graph/api/user-update) de Microsoft Graph. Dans l’exemple suivant, l’attribut [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) du profil de mot de passe est mis à jour avec la valeur `true`, ce qui force l’utilisateur à réinitialiser le mot de passe lors de la prochaine connexion.

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
"passwordProfile": {
  "forceChangePasswordNextSignIn": true
}
```

Une fois le profil de mot de passe du compte défini, vous devez également configurer un flux de réinitialisation forcée du mot de passe comme le décrit cet article.

## <a name="force-a-password-reset-after-90-days"></a>Forcer la réinitialisation du mot de passe au bout de 90 jours

En tant qu’administrateur, vous pouvez fixer l’expiration du mot de passe d’un utilisateur à 90 jours à l’aide de [MS Graph](microsoft-graph-operations.md). Au bout de 90 jours, la valeur de l’attribut [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) est définie automatiquement sur `true`. Pour forcer la réinitialisation de mot de passe au bout de 90 jours, supprimez la valeur `DisablePasswordExpiration` de l’attribut [Stratégie de mot de passe](user-profile-attributes.md#password-policy-attribute) du profil de l’utilisateur.

Dans l’exemple suivant, la stratégie de mot de passe est mise à jour avec la valeur `None`, ce qui force la réinitialisation de mot de passe après 90 jours :

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
  "passwordPolicies": "None"
}
```

Si vous avez désactivé la [complexité du mot de passe](password-complexity.md) forte, mettez à jour la stratégie de mot de passe avec la valeur [DisableStrongPassword](user-profile-attributes.md#password-policy-attribute) :

```http
PATCH https://graph.microsoft.com/v1.0/users/<user-object-ID>
Content-type: application/json

{
  "passwordPolicies": "DisableStrongPassword"
}
```

Une fois qu’une stratégie d’expiration du mot de passe a été définie, vous devez également configurer un flux de réinitialisation forcée du mot de passe, comme décrit dans cet article.

### <a name="password-expiry-duration"></a>Délai d'expiration du mot de passe

La valeur par défaut de la durée d’expiration du mot de passe est de **90** jours. Elle est configurable avec la cmdlet [Set-MsolPasswordPolicy](/powershell/module/msonline/set-msolpasswordpolicy) dans le Module Azure Active Directory pour Windows PowerShell. Cette commande met à jour le locataire de sorte que tous les mots de passe des utilisateurs expirent au bout du nombre de jours configuré.

::: zone-end

::: zone pivot="b2c-custom-policy"

Cette fonctionnalité est actuellement disponible uniquement pour les flux d’utilisateurs. Pour les étapes de configuration, choisissez le **Flux utilisateur** ci-dessus. Pour les stratégies personnalisées, utilisez [l’exemple GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset-first-logon) de réinitialisation de mot de passe à la première ouverture de session.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Configurez une [Réinitialisation de mot de passe en libre-service](add-password-reset-policy.md).
