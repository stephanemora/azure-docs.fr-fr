---
title: Inscrire des informations d’authentification pour réinitialiser son propre mot de passe – Azure AD
description: Inscrivez vos informations de méthode de vérification destinées à la réinitialisation de mot de passe en libre-service Azure AD pour pouvoir réinitialiser votre propre mot de passe sans l’aide d’un administrateur.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9748c0d04f958648f7919b7ac29dc58d5a137ac
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279049"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Inscrire vos informations de méthode de vérification pour réinitialiser votre mot de passe

> [!IMPORTANT]
> Vous ne pouvez pas vous connecter et vous êtes retrouvé ici ? Dans ce cas, consultez [Réinitialiser votre mot de passe professionnel ou scolaire](active-directory-passwords-update-your-own-password.md).

En tant qu’utilisateur final, vous pouvez réinitialiser votre mot de passe ou déverrouiller votre compte vous-même au moyen de la réinitialisation de mot de passe en libre-service (SSPR) Azure Active Directory (Azure AD). Pour pouvoir utiliser cette fonctionnalité, il vous faut inscrire vos méthodes de vérification ou confirmer les méthodes de vérification prédéfinies et remplies par votre administrateur.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Inscrire ou confirmer les données d’authentification avec SSPR

1. Ouvrez le navigateur web de votre appareil et accédez à la [page d’inscription à la réinitialisation de mot de passe](https://aka.ms/ssprsetup).
2. Entrez le nom d’utilisateur et le mot de passe que vous a fournis votre administrateur.
3. Selon la façon dont votre service informatique a effectué la configuration, une ou plusieurs des options suivantes peuvent être configurées et vérifiées par vous. Si vous avez autorisé votre administrateur à utiliser vos informations, il peut remplir certaines informations pour vous.
    * **Téléphone de bureau** : seul votre administrateur peut définir cette option.
    * **Téléphone d’authentification** : définissez cette option sur un autre numéro de téléphone auquel vous avez accès. Par exemple, un téléphone cellulaire qui peut recevoir un texte ou un appel.
    * **E-mail d’authentification** : définissez cette option sur une autre adresse e-mail à laquelle vous avez accès sans utiliser le mot de passe que vous souhaitez réinitialiser.
    * **Question de sécurité** : votre administrateur a approuvé cette liste de questions auxquelles vous devez répondre. Vous ne pouvez pas utiliser la même question ou réponse plusieurs fois.
4. Fournissez et vérifiez les informations que demande votre administrateur. Si plusieurs options sont disponibles, nous vous suggérons d’inscrire plusieurs méthodes. Cela vous donne une grande souplesse si une des méthodes n’est pas disponible. C’est par exemple le cas quand vous êtes en déplacement et que vous ne pouvez pas accéder à votre téléphone de bureau.

    ![Inscrire des méthodes de vérification et sélectionner Terminer][Register]

5. Sélectionnez **Terminer**. Vous pouvez désormais utiliser la fonctionnalité SSPR.

Si vous entrez des données pour **Téléphone d’authentification** ou **E-mail d’authentification**, elles ne sont pas visibles dans l’annuaire global. Les seules personnes qui peuvent voir ces données sont vos administrateurs et vous-même. Vous êtes la seule personne à pouvoir voir les réponses à vos questions de sécurité.

Vos administrateurs peuvent vous demander de confirmer vos méthodes de vérification après quelque temps pour vérifier que les méthodes inscrites sont toujours les bonnes.

## <a name="common-problems-and-their-solutions"></a>Problèmes courants et leurs solutions

 Voici quelques cas d'erreur courants et leurs solutions :

| Cas d’erreur| Quelle erreur voyez-vous ?| Solution |
| --- | --- | --- |
| J’obtiens une page « Veuillez contacter votre administrateur » après avoir entré mon identifiant utilisateur. | Contactez votre administrateur. <br> <br> Nous avons détecté que votre mot de passe de compte d'utilisateur n'est pas géré par Microsoft. Par conséquent, nous ne pouvons pas réinitialiser automatiquement votre mot de passe. <br> <br> Contactez votre service informatique pour une aide supplémentaire. | Ce message s’affiche parce que votre service informatique gère votre mot de passe dans votre environnement local et ne vous autorise pas à réinitialiser votre mot de passe à partir du lien **Votre compte n’est pas accessible**. <br> <br> Pour réinitialiser votre mot de passe, contactez votre service informatique directement. Indiquez-lui que vous voulez réinitialiser votre mot de passe pour qu’il active cette fonctionnalité pour vous.|
| J'obtiens une erreur « Votre compte n'est pas activé pour la réinitialisation de mot de passe » après avoir entré mon identifiant utilisateur. | Votre compte n’est pas activé pour la réinitialisation du mot de passe. <br> <br> Nous sommes désolés, mais votre service informatique n’a pas configuré votre compte pour utiliser ce service. <br> <br> Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place. | Ce message s’affiche parce que votre service informatique n’a pas activé la réinitialisation de mot de passe pour votre entreprise à partir du lien **Vous ne pouvez pas accéder à votre compte** ou ne vous a pas octroyé de licence vous permettant d’utiliser la fonctionnalité. <br> <br> Pour réinitialiser votre mot de passe, sélectionnez le lien **Contacter un administrateur**. Un e-mail est envoyé au service informatique de votre entreprise. Il lui indique que vous voulez réinitialiser votre mot de passe afin qu’elle active cette fonctionnalité pour vous. |
| J'obtiens une erreur « Impossible de vérifier votre compte » après avoir entré mon identifiant utilisateur. | Nous n'avons pas pu vérifier votre compte. <br> <br> Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place. | Ce message s’affiche parce que vous êtes autorisé à réinitialiser le mot de passe, mais que vous ne vous êtes pas inscrit pour utiliser le service. Pour demander une réinitialisation du mot de passe, accédez à la [page d’inscription à la réinitialisation du mot de passe](https://aka.ms/ssprsetup) une fois que vous avez à nouveau accès à votre compte. <br> <br> Pour réinitialiser votre mot de passe, sélectionnez le lien **Contacter un administrateur** afin d’envoyer un e-mail au service informatique de votre entreprise. |

## <a name="next-steps"></a>Étapes suivantes

* [Changer votre mot de passe avec la réinitialisation de mot de passe en libre-service](active-directory-passwords-update-your-own-password.md)
* [Page relative à l’inscription à la réinitialisation du mot de passe](https://aka.ms/ssprsetup)
* [Portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com/)
* [Quand vous ne pouvez pas vous connecter à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Page Inscription à la réinitialisation de mot de passe, montrant les méthodes inscrites et le bouton Terminer"

