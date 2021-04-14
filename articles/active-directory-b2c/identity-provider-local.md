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
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d9eb28ad19d53df542769a89b839668bbb205e30
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256104"
---
# <a name="set-up-the-local-account-identity-provider"></a>Configurer le fournisseur d’identité d’un compte local

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C propose différents moyens d’authentifier des utilisateurs. Les utilisateurs peuvent se connecter à un compte local en utilisant un nom d’utilisateur et un mot de passe, la vérification par téléphone (également appelée authentification sans mot de passe) ou des fournisseurs d’identité de réseau social. L’inscription par e-mail est activée par défaut dans les paramètres du fournisseur d’identité de votre compte local. 

Cet article décrit la manière dont les utilisateurs créent leurs comptes locaux sur ce locataire Azure AD B2C. Pour les identités de réseau social ou d’entreprise, où l’identité de l’utilisateur est gérée par un fournisseur d’identité fédéré comme Facebook et Google, consultez [Ajouter un fournisseur d’identité](add-identity-provider.md).

## <a name="email-sign-in"></a>Connexion par adresse e-mail

Avec l’option de l’adresse e-mail, les utilisateurs peuvent s’inscrire ou se connecter avec leur adresse e-mail et leur mot de passe :

- **Connexion** : Les utilisateurs sont invités à fournir leur adresse e-mail et leur mot de passe.
- **Inscription** : Les utilisateurs sont invités à entrer une adresse e-mail, qui est vérifiée lors de l’inscription (facultatif) et devient leur ID de connexion. L’utilisateur entre ensuite toute autre information demandée sur la page d’inscription, par exemple, le nom d’affichage, le prénom et le nom de famille. Sélectionnez ensuite Continuer pour créer le compte.
- **Réinitialisation du mot de passe** : Les utilisateurs doivent entrer et vérifier leur adresse e-mail, après quoi, l’utilisateur peut réinitialiser son mot de passe.

![Expérience d’inscription ou de connexion par adresse e-mail](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>Connexion avec un nom d’utilisateur

Avec l’option utilisateur, les utilisateurs peuvent s’inscrire ou se connecter à l’aide d’un nom d’utilisateur et d’un mot de passe :

- **Connexion** : Les utilisateurs sont invités à fournir leur nom d’utilisateur et leur mot de passe.
- **Inscription** : Les utilisateurs seront invités à entrer un nom d’utilisateur, qui deviendra leur ID de connexion. Ils sont également invités à entrer une adresse e-mail, qui est vérifiée lors de l’inscription. L’adresse e-mail sera utilisée lors d’un processus de réinitialisation du mot de passe. L’utilisateur entre toute autre information demandée sur la page d’inscription, par exemple, le nom d’affichage, le prénom et le nom de famille. L’utilisateur sélectionne ensuite Continuer pour créer le compte.
- **Réinitialisation du mot de passe** : Les utilisateurs doivent entrer leur nom d’utilisateur et l’adresse e-mail associée. L’adresse e-mail doit être vérifiée, après quoi l’utilisateur peut réinitialiser le mot de passe.

![Expérience d’inscription ou de connexion avec un nom d’utilisateur](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>Connexion par téléphone (préversion)

L’authentification sans mot de passe est un type d’authentification où un utilisateur n’a pas besoin de se connecter avec son mot de passe. Avec l’inscription et la connexion par téléphone, l’utilisateur peut s’inscrire à l’application en utilisant un numéro de téléphone comme principal identifiant de connexion. L’utilisateur fera l’expérience suivante lors de l’inscription et de la connexion :

- **Connexion** : Si l’utilisateur possède déjà un compte avec un numéro de téléphone comme identifiant, il entre son numéro de téléphone et sélectionne *Se connecter*. Il confirme le pays et le numéro de téléphone en sélectionnant *Continuer*, et un code de vérification unique est envoyé sur son téléphone. L’utilisateur entre le code de vérification et sélectionne *Continuer* pour se connecter.
- **Inscription** : Si l’utilisateur n’a pas encore de compte pour votre application, il peut en créer un en cliquant sur le lien *S’inscrire*. 
    1. Une page d’inscription s’affiche, dans laquelle l’utilisateur sélectionne son *pays*, entre son numéro de téléphone et sélectionne *Envoyer un code*. 
    1. Un code de vérification unique est envoyé au numéro de téléphone de l’utilisateur. L’utilisateur entre le *code de vérification* sur la page d’inscription, puis il sélectionne *Vérifier le code*. (Si l’utilisateur n’a pas pu récupérer le code, il peut sélectionner *Envoyer un nouveau code*.) 
    1. L’utilisateur entre toute autre information demandée sur la page d’inscription, par exemple, le nom d’affichage, le prénom et le nom de famille. Sélectionnez Continuer.
    1. Ensuite, l’utilisateur est invité à fournir une **adresse e-mail de récupération**. L’utilisateur entre son adresse e-mail, puis sélectionne *Envoyer le code de vérification*. Un code est envoyé à l’adresse e-mail de l’utilisateur, qu’il peut récupérer et entrer dans la zone Code de vérification. L’utilisateur sélectionne ensuite Vérifier le code.
    1. Une fois le code vérifié, l’utilisateur sélectionne *Créer* pour créer son compte. 

![Expérience d’inscription ou de connexion par téléphone](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>Tarifs

Les mots de passe uniques sont envoyés à vos utilisateurs par SMS. Selon votre opérateur de réseau mobile, vous pouvez être facturé pour chaque message envoyé. Pour obtenir des informations sur la tarification, voir la section **Frais distincts** dans [Tarification Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

> [!NOTE]
> L’authentification multifacteur (MFA) est désactivée par défaut lorsque vous configurez un flux d’utilisateur avec l’inscription par téléphone. Vous pouvez activer la MFA dans les flux d’utilisateurs avec l’inscription par téléphone, mais, comme un numéro de téléphone est utilisé comme identificateur principal, l’envoi par e-mail d’un code secret à usage unique est la seule option disponible pour le deuxième facteur d’authentification.

### <a name="phone-recovery"></a>Récupération par téléphone

Lorsque vous activez l’inscription et la connexion par téléphone pour vos flux d’utilisateurs, il est également judicieux d’activer la fonctionnalité de récupération par e-mail. Grâce à cette fonctionnalité, un utilisateur peut fournir une adresse e-mail qui peut être utilisée pour récupérer son compte lorsqu’il n’a pas son téléphone. Cette adresse e-mail est utilisée uniquement pour la récupération du compte. Elle ne peut pas être utilisée pour se connecter.

- Lorsque l’invite de récupération par e-mail est **activée**, un utilisateur qui s’inscrit pour la première fois est invité à confirmer une adresse e-mail de secours. Un utilisateur qui n’a pas fourni d’e-mail de récupération auparavant est invité à confirmer une adresse e-mail de secours lors de sa prochaine connexion.

- Lorsque la récupération par e-mail est **désactivée**, un utilisateur qui s’inscrit ou se connecte ne reçoit pas d’invite de récupération par e-mail.
 
Les captures d’écran suivantes illustrent le processus de récupération par téléphone :

![Flux d’utilisateur de la récupération par téléphone](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>Connexion par adresse e-mail ou par téléphone (préversion)

Vous pouvez choisir de combiner la [connexion par téléphone](#phone-sign-in-preview) et la [connexion par adresse e-mail](#email-sign-in). Dans la page d’inscription ou de connexion, l’utilisateur peut saisir un numéro de téléphone ou une adresse e-mail. En fonction de l’entrée utilisateur, Azure AD B2C dirige l’utilisateur vers le flux correspondant. 

![Expérience d’inscription ou de connexion par téléphone ou adresse e-mail](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Configurer les paramètres du fournisseur d’identité du compte local

Vous pouvez configurer les fournisseurs d’identité locaux pouvant être utilisés dans un flux d’utilisateur en activant ou en désactivant les fournisseurs (adresse e-mail, nom d’utilisateur ou numéro de téléphone).  Plusieurs fournisseurs d’identité locaux peuvent être activés au niveau du locataire.

Un flux d’utilisateur ne peut être configuré que pour utiliser l’un des fournisseurs d’identité de compte local à un moment donné. Chaque flux d’utilisateur peut avoir un fournisseur d’identité de compte local différent, si plusieurs ont été activés au niveau du locataire.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser le répertoire qui contient votre locataire Azure AD B2C en sélectionnant le filtre **Répertoire + abonnement** dans le menu supérieur et en choisissant le répertoire qui contient votre locataire Azure AD.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Fournisseurs d’identité**.
1. Dans la liste des fournisseurs d’identité, sélectionnez **Compte local**.
1. Dans la page **Configurer un IdP local**, sélectionnez au moins l’un des types d’identité autorisés que les consommateurs peuvent utiliser pour créer leurs comptes locaux dans votre locataire Azure AD B2C.
1. Sélectionnez **Enregistrer**.

## <a name="configure-your-user-flow"></a>Configurer votre flux d’utilisateur

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
