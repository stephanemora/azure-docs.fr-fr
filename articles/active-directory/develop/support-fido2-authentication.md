---
title: Prendre en charge l’authentification sans mot de passe avec des clés FIDO2 dans les applications que vous développez | Azure
titleSuffix: Microsoft identity platform
description: Ce guide de déploiement explique comment prendre en charge l’authentification sans mot de passe avec des clés de sécurité FIDO2 dans les applications que vous développez.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: f63d7aed75b14f5f008a639d667d8806b233b9fa
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174596"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>Prendre en charge l’authentification sans mot de passe avec des clés FIDO2 dans les applications que vous développez

Ces configurations et meilleures pratiques vous aideront à éviter des scénarios courants qui empêchent la mise à la disposition de l’[authentification sans mot de passe FIDO2](../../active-directory/authentication/concept-authentication-passwordless.md) aux utilisateurs de vos applications.

## <a name="general-best-practices"></a>Bonnes pratiques générales

### <a name="domain-hints"></a>Indications de domaine

N’utilisez pas d’indication de domaine pour contourner la [découverte de home-real](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md). Cette fonctionnalité est destinée à rationaliser les connexions, mais le fournisseur d’identité fédéré peut ne pas prendre en charge l’authentification sans mot de passe.

### <a name="requiring-specific-credentials"></a>Exiger des informations d’identification spécifiques

Si vous utilisez SAML, ne spécifiez pas qu’un mot de passe est requis [avec l’élément RequestedAuthnContext](single-sign-on-saml-protocol.md#requestauthncontext).

L’élément RequestedAuthnContext est facultatif. Par conséquent, pour le résoudre, vous pouvez le supprimer de vos demandes d’authentification SAML. Il s’agit d’une meilleure pratique générale, car l’utilisation de cet élément peut également empêcher le bon fonctionnement d’autres options d’authentification, comme l’authentification multifacteur.

### <a name="using-the-most-recently-used-authentication-method"></a>Utilisation de la méthode d’authentification la plus récemment utilisée

La méthode de connexion la plus récemment utilisée par un utilisateur lui sera présentée en premier. Cela peut être source de confusion lorsque les utilisateurs pensent qu’ils doivent utiliser la première option présentée. Toutefois, ils peuvent choisir une autre option en sélectionnant « Autres méthodes pour se connecter », comme indiqué ci-dessous.

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="Image de l’expérience d’authentification de l’utilisateur mettant en évidence le bouton qui permet à l’utilisateur de changer la méthode d’authentification.":::

## <a name="platform-specific-best-practices"></a>Meilleures pratiques spécifiques à la plateforme

### <a name="desktop"></a>Bureau

Les options recommandées pour l’implémentation de l’authentification sont, dans l’ordre :

- Les applications de bureau .NET qui utilisent la bibliothèque d’authentification Microsoft (MSAL) doivent utiliser le gestionnaire d’authentification Windows (WAM). Cette intégration et ses avantages sont [documentés sur GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam).
- Utilisez [WebView2](/microsoft-edge/webview2/) pour prendre en charge FIDO2 dans un navigateur incorporé.
- Utilisez le navigateur du système. Les bibliothèques MSAL pour les plateformes de bureau utilisent cette méthode par défaut. Vous pouvez consulter notre page sur la compatibilité des navigateurs avec FIDO2 pour vous assurer que le navigateur que vous utilisez prend en charge l’authentification FIDO2.

### <a name="mobile"></a>Mobile

Depuis février 2021, FIDO2 n’est plus pris en charge pour les applications iOS ou Android natives, mais cela est en cours de développement.

Pour préparer les applications à sa disponibilité, et comme meilleure pratique générale, les applications iOS et Android doivent utiliser MSAL avec la configuration par défaut qui consiste à utiliser le navigateur web du système.

Si vous n’utilisez pas MSAL, vous devez quand même utiliser le navigateur web du système pour l’authentification. Les fonctionnalités telles que l’authentification unique et l’accès conditionnel s’appuient sur une surface web partagée fournie par le navigateur web du système. Cela signifie que vous devez utiliser les [onglets personnalisés de Chrome](https://developer.chrome.com/docs/multidevice/android/customtabs/) (Android) ou [authentifier un utilisateur à l’aide d’un service web [Documentation pour développeurs sur Apple]](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) (iOS).

### <a name="web-and-single-page-apps"></a>Applications web et monopages

La disponibilité de l’authentification sans mot de passe FIDO2 pour les applications qui s’exécutent dans un navigateur web dépend de la combinaison du navigateur et de la plateforme. Vous pouvez consulter notre [matrice de compatibilité FIDO2](../authentication/fido2-compatibility.md) pour vérifier si la combinaison que vos utilisateurs rencontreront est prise en charge.

## <a name="next-steps"></a>Étapes suivantes

[Options d’authentification sans mot de passe pour Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)