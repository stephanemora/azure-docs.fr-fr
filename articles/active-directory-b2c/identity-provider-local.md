---
title: Configurer le fournisseur d’identité d’un compte local Azure AD B2C
titleSuffix: Azure AD B2C
description: Définissez les types d’identité que les utilisateurs peuvent utiliser pour s’inscrire ou se connecter (adresse e-mail, nom d’utilisateur, numéro de téléphone) dans votre locataire Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/22/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1de783a3e6696e9ca8a8f618ce3744a91e774692
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112198851"
---
# <a name="set-up-the-local-account-identity-provider"></a>Configurer le fournisseur d’identité d’un compte local

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Cet article explique comment déterminer les méthodes de connexion pour vos comptes locaux Azure AD B2C. Un compte local fait référence à un compte créé dans votre annuaire Azure AD B2C lorsqu’un utilisateur s’inscrit à votre application ou qu’un administrateur crée le compte. Les noms d’utilisateur et les mots de passe sont stockés localement et Azure AD B2C sert de fournisseur d’identité pour les comptes locaux.

Plusieurs méthodes de connexion sont disponibles pour les comptes locaux :

- **E-mail** : Les utilisateurs peuvent s’inscrire et se connecter à votre application avec leur adresse e-mail et leur mot de passe. L’inscription par e-mail est activée par défaut dans les paramètres du fournisseur d’identité de votre compte local.
- **Nom d’utilisateur** : Les utilisateurs peuvent s’inscrire et se connecter avec un nom d’utilisateur et un mot de passe.
- **Téléphone (ou authentification sans mot de passe)** : Les utilisateurs peuvent s’inscrire et se connecter à votre application en utilisant un numéro de téléphone comme identifiant principal de connexion. Ils n’ont pas besoin de créer de mots de passe. Les mots de passe uniques sont envoyés à vos utilisateurs par SMS.
- **Téléphone ou e-mail** : Les utilisateurs peuvent s’inscrire ou se connecter en saisissant un numéro de téléphone ou une adresse e-mail. En fonction de l’entrée utilisateur, Azure AD B2C dirige l’utilisateur vers le flux correspondant sur la page d’inscription ou de connexion.
- **Récupération par téléphone** : Si vous avez activé l’inscription ou la connexion par téléphone, la récupération par téléphone permet aux utilisateurs de fournir une adresse e-mail qui peut être utilisée pour récupérer leur compte lorsqu’ils n’ont pas leur téléphone.

Pour en savoir plus sur ces méthodes, consultez [Options de connexion](sign-in-options.md). 

Pour configurer les paramètres pour les identités de réseau social ou d’entreprise, où l’identité d’un utilisateur est gérée par un fournisseur d’identité fédéré comme Facebook ou Google, consultez [Ajouter un fournisseur d’identité](add-identity-provider.md).

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Configurer les paramètres du fournisseur d’identité du compte local


Vous pouvez choisir les méthodes de connexion au compte local (e-mail, nom d’utilisateur ou numéro de téléphone) que vous souhaitez rendre disponibles dans votre locataire en configurant le fournisseur **Compte local** dans votre liste de **fournisseurs d’identité** Azure AD B2C. Quand vous configurez un workflow utilisateur, vous pouvez choisir l’une des méthodes de connexion de compte local que vous avez activées à l’ensemble du locataire. Vous ne pouvez sélectionner qu’une seule méthode de connexion de compte local pour un workflow utilisateur, mais vous pouvez sélectionner une autre option pour chaque workflow utilisateur.

Pour définir les options de connexion de votre compte local au niveau du locataire : 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser le répertoire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Répertoire + abonnement** dans le menu supérieur et en choisissant le répertoire qui contient votre locataire Azure AD.
1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Fournisseurs d’identité**.
1. Dans la liste des fournisseurs d’identité, sélectionnez **Compte local**.
1. Dans la page **Configurer le fournisseur d’identité local**, sélectionnez un ou plusieurs types d’identité que vous souhaitez activer pour les flux d’utilisateurs dans votre locataire Azure AD B2C. La sélection d’une option ici le rend simplement disponible pour une utilisation à l’ensemble du locataire. Lorsque vous créez ou modifiez un workflow d’utilisateur, vous pouvez choisir parmi les options que vous activez ici.

   - **Téléphone** : les utilisateurs sont invités à entrer un numéro de téléphone, qui est vérifié au moment de l’inscription et devient son identifiant utilisateur.
   - **Nom d’utilisateur** : Les utilisateurs peuvent créer leur propre identifiant unique. Une adresse e-mail est collectée auprès de l’utilisateur et vérifiée.
   - **E-mail** : Les utilisateurs seront invités à fournir une adresse e-mail qui sera vérifiée lors de l’inscription, avant de devenir leur ID utilisateur.
1. Sélectionnez **Enregistrer**.

## <a name="configure-your-user-flow"></a>Configuration du flux d’utilisateur

1. Dans le menu de gauche du portail Azure, sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Flux utilisateur (stratégies)** .
1. Sélectionnez le flux d’utilisateur pour lequel vous souhaitez configurer l’expérience d’inscription et de connexion.
1. Sélectionnez **Fournisseurs d’identité**.
1. Sous **Comptes locaux**, sélectionnez l’une des options suivantes : **Inscription par adresse e-mail**, **Inscription avec l’ID d’utilisateur**, **Inscription par téléphone**, **Inscription par téléphone/adresse e-mail** ou **Aucun**.

### <a name="enable-the-recovery-email-prompt"></a>Activer l’invite de récupération par e-mail

Si vous choisissez l’option **Inscription par téléphone** ou **Inscription par téléphone/adresse e-mail**, activez l’invite de récupération par e-mail.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans la Portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Dans Azure AD B2C, sélectionnez **Flux d’utilisateurs** sous **Stratégies**.
1. Sélectionnez le flux d’utilisateur dans la liste.
1. Sous **Paramètres**, sélectionnez **Propriétés**.
1. À côté de l’option **Activer l’invite de récupération par e-mail pour l’inscription et la connexion par numéro de téléphone (préversion)** , sélectionnez :
   - **Activé** pour afficher l’invite de récupération par e-mail lors de l’inscription et de la connexion.
   - **Désactivé** pour masquer l’invite de récupération par e-mail.
1. Sélectionnez **Enregistrer**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Obtenez le pack de démarrage

Les stratégies personnalisées sont un ensemble de fichiers XML que vous téléchargez vers votre locataire Azure AD B2C pour définir des parcours utilisateur. Nous fournissons des packs de démarrage avec plusieurs stratégies prédéfinies. Téléchargez le pack de démarrage approprié : 

- [Connexion par adresse e-mail](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Connexion avec un nom d’utilisateur](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Connexion par téléphone](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless) Sélectionnez la stratégie de partie de confiance [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml). 
- [Connexion par adresse e-mail ou par téléphone](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless) Sélectionnez la stratégie de partie de confiance [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml).

Après avoir téléchargé le pack de démarrage

1. Dans chaque fichier, remplacez la chaîne `yourtenant` par le nom de votre locataire Azure AD B2C. Par exemple, si le nom de votre locataire B2C est *contosob2c*, toutes les instances de `yourtenant.onmicrosoft.com` deviennent `contosob2c.onmicrosoft.com`.

1. Effectuez les étapes décrites dans la section [Ajouter des ID d’applications à la stratégie personnalisée](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-application-ids-to-the-custom-policy) de la rubrique [Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy). Par exemple, mettez à jour `/phone-number-passwordless/`**`Phone_Email_Base.xml`** avec les **ID d’application (client)** des deux applications que vous avez inscrites en remplissant les conditions préalables, *IdentityExperienceFramework* et *ProxyIdentityExperienceFramework*.
1. Télécharger les fichiers de stratégie

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des fournisseurs d’identité externes](add-identity-provider.md)
- [Créer un flux utilisateur](tutorial-create-user-flows.md)
