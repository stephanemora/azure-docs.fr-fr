---
title: Options de connexion prises en charge par Azure AD B2C
titleSuffix: Azure AD B2C
description: En savoir plus sur les options d’inscription et de connexion que vous pouvez utiliser avec Azure Active Directory B2C, y compris le nom d’utilisateur et le mot de passe, l’e-mail, le téléphone ou la fédération avec des fournisseurs d’identité sociale ou externes.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: efb526f9a7418c0d7d2316ec91f355a7c4431404
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112204744"
---
# <a name="sign-in-options-in-azure-ad-b2c"></a>Options de connexion dans Azure AD B2C

Azure AD B2C propose plusieurs méthodes d’inscription et de connexion pour les utilisateurs de vos applications. Lorsque les utilisateurs s’inscrivent à votre application, vous déterminez s’ils utilisent un nom d’utilisateur, une adresse e-mail ou un numéro de téléphone pour créer des comptes locaux dans votre locataire Azure AD B2C. Vous pouvez également fédérer avec des fournisseurs d’identité sociale (comme Facebook, LinkedIn et Twitter) et des protocoles d’identité standard (comme OAuth 2.0, OpenID Connect, etc.).

Cet article donne une vue d’ensemble des options de connexion Azure AD B2C.

## <a name="email-sign-in"></a>Connexion par adresse e-mail

L’inscription par e-mail est activée par défaut dans les paramètres du fournisseur d’identité de votre compte local. Avec l’option de l’adresse e-mail, les utilisateurs peuvent s’inscrire et se connecter avec leur adresse e-mail et leur mot de passe.

- **Connexion** : Les utilisateurs sont invités à fournir leur adresse e-mail et leur mot de passe.
- **Inscription** : Les utilisateurs sont invités à entrer une adresse e-mail, qui est vérifiée lors de l’inscription (facultatif) et devient leur ID de connexion. L’utilisateur entre ensuite toute autre information demandée sur la page d’inscription, par exemple, le nom d’affichage, le prénom et le nom de famille. Ensuite, ils sélectionnent **Continuer** pour créer un compte.
- **Réinitialisation du mot de passe** : Les utilisateurs entrent et vérifient leur adresse e-mail, après quoi, l’utilisateur peut réinitialiser son mot de passe.

![Expérience d’inscription ou de connexion par adresse e-mail](./media/sign-in-options/local-account-email-experience.png)

Découvrez comment configurer la connexion par e-mail dans votre fournisseur d’identité de compte local.
## <a name="username-sign-in"></a>Connexion avec un nom d’utilisateur

Votre fournisseur d’identité de compte local comprend une option de nom d’utilisateur qui permet aux utilisateurs de s’inscrire et de se connecter à votre application avec un nom d’utilisateur et un mot de passe.

- **Connexion** : Les utilisateurs sont invités à fournir leur nom d’utilisateur et leur mot de passe.
- **Inscription** : Les utilisateurs seront invités à entrer un nom d’utilisateur, qui deviendra leur ID de connexion. Ils sont également invités à entrer une adresse e-mail, qui est vérifiée lors de l’inscription. L’adresse e-mail sera utilisée lors d’un processus de réinitialisation du mot de passe. L’utilisateur entre toute autre information demandée sur la page d’inscription, par exemple, le nom d’affichage, le prénom et le nom de famille. L’utilisateur sélectionne ensuite Continuer pour créer le compte.
- **Réinitialisation de mot de passe** : Les utilisateurs doivent entrer leur nom d’utilisateur et l’adresse e-mail associée. L’adresse e-mail doit être vérifiée, après quoi l’utilisateur peut réinitialiser le mot de passe.

![Expérience d’inscription ou de connexion avec un nom d’utilisateur](./media/sign-in-options/local-account-username-experience.png)

## <a name="phone-sign-in"></a>Connexion par téléphone

La connexion par téléphone est une option avec mot de passe dans les paramètres de votre fournisseur d’identité de compte local. Cette méthode permet aux utilisateurs de s’inscrire à votre application à l’aide d’un numéro de téléphone comme identificateur principal. Les mots de passe uniques sont envoyés à vos utilisateurs par SMS. Les utilisateurs feront l’expérience suivante lors de l’inscription et de la connexion :

- **Connexion** : Si l’utilisateur possède déjà un compte avec un numéro de téléphone comme identifiant, il entre son numéro de téléphone et sélectionne *Se connecter*. Il confirme le pays et le numéro de téléphone en sélectionnant *Continuer*, et un code de vérification unique est envoyé sur son téléphone. L’utilisateur entre le code de vérification et sélectionne *Continuer* pour se connecter.
- **Inscription** : Si l’utilisateur n’a pas encore de compte pour votre application, il peut en créer un en cliquant sur le lien *S’inscrire*.
    1. Une page d’inscription s’affiche, dans laquelle l’utilisateur sélectionne son *pays*, entre son numéro de téléphone et sélectionne *Envoyer un code*. 
    1. Un code de vérification unique est envoyé au numéro de téléphone de l’utilisateur. L’utilisateur entre le *code de vérification* sur la page d’inscription, puis il sélectionne *Vérifier le code*. (Si l’utilisateur n’a pas pu récupérer le code, il peut sélectionner *Envoyer un nouveau code*.)
    1. L’utilisateur entre toute autre information demandée sur la page d’inscription, par exemple, le nom d’affichage, le prénom et le nom de famille. Sélectionnez Continuer.
    1. Ensuite, l’utilisateur est invité à fournir une **adresse e-mail de récupération**. L’utilisateur entre son adresse e-mail, puis sélectionne *Envoyer le code de vérification*. Un code est envoyé à l’adresse e-mail de l’utilisateur, qu’il peut récupérer et entrer dans la zone Code de vérification. L’utilisateur sélectionne ensuite Vérifier le code.
    1. Une fois le code vérifié, l’utilisateur sélectionne *Créer* pour créer son compte.

![Expérience d’inscription ou de connexion par téléphone](./media/sign-in-options/local-account-phone-experience.png)

### <a name="pricing-for-phone-sign-in"></a>Tarification pour la connexion par téléphone

Les mots de passe uniques sont envoyés à vos utilisateurs par SMS. Selon votre opérateur de réseau mobile, vous pouvez être facturé pour chaque message envoyé. Pour obtenir des informations sur la tarification, voir la section **Frais distincts** dans [Tarification Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

> [!NOTE]
> L’authentification multifacteur (MFA) est désactivée par défaut lorsque vous configurez un flux d’utilisateur avec l’inscription par téléphone. Vous pouvez activer la MFA dans les flux d’utilisateurs avec l’inscription par téléphone, mais, comme un numéro de téléphone est utilisé comme identificateur principal, l’envoi par e-mail d’un code secret à usage unique est la seule option disponible pour le deuxième facteur d’authentification.

### <a name="phone-recovery"></a>Récupération par téléphone

Lorsque vous activez l’inscription et la connexion par téléphone pour vos flux d’utilisateurs, il est également judicieux d’activer la fonctionnalité de récupération par e-mail. Grâce à cette fonctionnalité, un utilisateur peut fournir une adresse e-mail qui peut être utilisée pour récupérer son compte lorsqu’il n’a pas son téléphone. Cette adresse e-mail est utilisée uniquement pour la récupération du compte. Elle ne peut pas être utilisée pour se connecter.

- Lorsque l’invite de récupération par e-mail est **activée**, un utilisateur qui s’inscrit pour la première fois est invité à confirmer une adresse e-mail de secours. Un utilisateur qui n’a pas fourni d’e-mail de récupération auparavant est invité à confirmer une adresse e-mail de secours lors de sa prochaine connexion.

- Lorsque la récupération par e-mail est **désactivée**, un utilisateur qui s’inscrit ou se connecte ne reçoit pas d’invite de récupération par e-mail.

Les captures d’écran suivantes illustrent le processus de récupération par téléphone :

![Flux d’utilisateur de la récupération par téléphone](./media/sign-in-options/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in"></a>Connexion par adresse e-mail ou par téléphone

Vous pouvez choisir de combiner l’[identification par téléphone](#phone-sign-in) et l’[identification par e-mail](#email-sign-in) dans les paramètres du fournisseur d’identité de votre compte local. Dans la page d’inscription ou de connexion, l’utilisateur peut saisir un numéro de téléphone ou une adresse e-mail. En fonction de l’entrée utilisateur, Azure AD B2C dirige l’utilisateur vers le flux correspondant.

![Expérience d’inscription ou de connexion par téléphone ou adresse e-mail](./media/sign-in-options/local-account-phone-and-email-experience.png)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les stratégies intégrées fournies par [Flux d’utilisateur dans Azure Active Directory B2C](user-flow-overview.md).
- [Configurer votre fournisseur d’identité du compte local](identity-provider-local.md).