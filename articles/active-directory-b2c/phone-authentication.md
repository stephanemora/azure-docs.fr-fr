---
title: Inscription et connexion par téléphone avec des stratégies personnalisées
titleSuffix: Azure AD B2C
description: Découvrez comment envoyer des mots de passe à usage unique dans des messages texte vers les téléphones des utilisateurs de votre application à l’aide de stratégies personnalisées dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 96bf088e6db29817d1ae4276c254a84723e5d03d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475047"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Configurer l’inscription et la connexion par téléphone avec des stratégies personnalisées dans Azure AD B2C

L’inscription et la connexion par téléphone dans Azure Active Directory B2C (Azure AD B2C) permet à vos utilisateurs de s’inscrire et de se connecter à vos applications en utilisant un mot de passe à usage unique (OTP) envoyé par SMS sur leur téléphone. Les mots de passe à usage unique permettent de réduire le risque que vos utilisateurs oublient leur mot de passe ou que ce mot de passe soit piraté.

Suivez les étapes de cet article pour utiliser des stratégies personnalisées permettant à vos clients de s’inscrire et de se connecter à vos applications en utilisant un mot de passe à usage unique envoyé sur leur téléphone.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Conditions préalables requises

* [Locataire Azure AD B2C](tutorial-create-tenant.md)
* [Application web inscrite](tutorial-register-applications.md) dans votre locataire
* [Stratégies personnalisées](active-directory-b2c-get-started-custom.md) téléchargées sur votre locataire

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Obtenir le pack de démarrage pour l’inscription et la connexion par téléphone

Commencez par mettre à jour les fichiers de stratégies personnalisées d’inscription et de connexion par téléphone afin de les utiliser avec votre locataire Azure AD B2C.

Les étapes suivantes supposent que vous avez défini les [préférences](#prerequisites) et que vous avez déjà cloné le référentiel du [pack de démarrage de stratégies personnalisées][starter-pack] sur votre machine locale.

1. Vous trouverez les [fichiers de stratégies personnalisés d’inscription et de connexion par téléphone][starter-pack-phone] dans le clone local du référentiel de votre pack de démarrage, ou vous pouvez directement les télécharger. Les fichiers de stratégie XML se trouvent dans le répertoire suivant :

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Dans chaque fichier, remplacez la chaîne `yourtenant` par le nom de votre locataire Azure AD B2C. Par exemple, si le nom de votre locataire B2C est *contosob2c*, toutes les instances de `yourtenant.onmicrosoft.com` deviennent `contosob2c.onmicrosoft.com`.

1. Effectuez les étapes décrites dans la section [Ajouter des ID d’applications à la stratégie personnalisée](active-directory-b2c-get-started-custom.md#add-application-ids-to-the-custom-policy) de la rubrique [Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-get-started-custom.md). Dans ce cas, mettez à jour `/phone-number-passwordless/` **`Phone_Email_Base.xml`** avec les **ID d’application (client)** des deux applications que vous avez enregistrées en remplissant les conditions préalables, *IdentityExperienceFramework* et *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Télécharger les fichiers de stratégie

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et accédez à votre locataire Azure AD B2C.
1. Sous **Stratégies**, sélectionnez **Identity Experience Framework**.
1. Sélectionnez **Charger une stratégie personnalisée**.
1. Téléchargez les fichiers de stratégie dans l'ordre suivant :
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

À mesure que vous téléchargez chaque fichier, Azure ajoute le préfixe `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Tester la stratégie personnalisée

1. Sous **Stratégies personnalisées**, sélectionnez **B2C_1A_SignUpOrSignInWithPhone**.
1. Sous **Sélectionner l’application**, sélectionnez l’application *webapp1* que vous avez enregistrée lorsque vous avez rempli les conditions préalables.
1. Pour **Sélectionner l’URL de réponse**, choisissez `https://jwt.ms`.
1. Sélectionnez **Exécuter maintenant** et inscrivez-vous en utilisant une adresse e-mail ou un numéro de téléphone.
1. Sélectionnez de nouveau **Exécuter maintenant** et connectez-vous avec le même compte pour vérifier que votre configuration est correcte.

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez des informations sur l’inscription par téléphone et le pack de démarrage de stratégie personnalisée (et d'autres packs de démarrage) sur GitHub :

[Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless][starter-pack-phone]

Les fichiers de stratégies du pack de démarrage utilisent des profils techniques d'authentification multifacteur et des transformations de revendications de numéros de téléphone :

* [Définir un profil technique Azure Multi-Factor Authentication](multi-factor-auth-technical-profile.md)
* [Définir des transformations de revendications de numéros de téléphone](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
