---
title: Présentation - Configurer des méthodes de vérification à deux facteurs - Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer vos méthodes de vérification à deux facteurs pour la vérification à deux facteurs.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83bba343ac73fd0df575ae1e8a83c589a0bc15ac
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616165"
---
# <a name="set-up-your-two-factor-verification-methods-overview"></a>Présentation - Configurer vos méthodes de vérification à deux facteurs

Votre organisation a activé la vérification à deux facteurs, ce qui signifie que la connexion de votre compte professionnel ou scolaire nécessite à présent une combinaison de votre nom d’utilisateur, de votre mot de passe et d’un appareil mobile ou d’un téléphone. Votre organisation a activé cette vérification supplémentaire car elle est plus sécurisée qu’un mot de passe, puisqu’elle s’appuie sur deux formes d’authentification : quelque chose que vous connaissez et quelque chose que vous avez avec vous. La vérification à deux facteurs peut aider à empêcher les pirates de se faire passer pour vous, car même s’ils ont votre mot de passe, il est probable qu’ils n’ont pas votre appareil.

>[!Important]
>Ce contenu est destiné aux utilisateurs. Si vous êtes un administrateur, vous trouverez plus d’informations sur la méthode à suivre pour configurer et gérer votre environnement Azure Active Directory (Azure AD) dans le [documentation relative à Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

## <a name="who-decides-if-you-use-this-feature"></a>Qui décide si vous utilisez cette fonctionnalité ?

Selon votre type de compte, votre organisation peut décider que vous devez utiliser la vérification à deux facteurs ou vous pouvez le décider par vous-même.

- **Un compte professionnel ou scolaire.** Si vous utilisez un compte professionnel ou scolaire (par exemple alain@contoso.com), c’est à votre organisation de décider si vous devez utiliser la vérification à deux facteurs, ainsi que les méthodes de vérification spécifiques. Étant donné que votre organisation a décidé que vous devez utiliser cette fonctionnalité, vous n’avez aucun moyen de la désactiver individuellement.

- **Compte Microsoft personnel.** Vous pouvez choisir de configurer la vérification à deux facteurs pour vos comptes Microsoft personnels (par exemple alain@outlook.com). Si vous rencontrez des problèmes avec la vérification à deux facteurs et votre compte Microsoft personnel, consultez [Activation ou désactivation de la vérification à deux facteurs de votre compte Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Étant donné que vous choisissez d’utiliser cette fonctionnalité, vous pouvez l’activer et la désactiver chaque fois que vous le souhaitez.

## <a name="access-the-additional-security-verification-page"></a>Accès à la page Vérification de sécurité supplémentaire

Une fois que votre organisation a activé et configuré la vérification à deux facteurs, vous recevez un message vous invitant à fournir des informations supplémentaires pour vous aider à protéger votre compte.

![Invite Informations supplémentaires requises](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Pour accéder à la page Vérification de sécurité supplémentaire

1. Sélectionnez **Suivant** dans l’invite**Plus d’informations requises**.

    La page **Vérification de sécurité supplémentaire** s’affiche.

2. À partir de la page **Vérification de sécurité supplémentaire**, vous devez décider de la méthode de vérification à deux facteurs à utiliser pour vérifier que vous êtes bien qui vous prétendez être une fois que vous vous êtes connecté à votre compte professionnel ou scolaire. Vous pouvez sélectionner :

    | Méthode de contact | Description |
    | --- | --- |
    | Application mobile | <ul><li>**Recevoir des notifications pour la vérification.** Cette option envoie une notification à l’application d’authentification sur votre smartphone ou tablette. Examinez la notification et, si elle est légitime, sélectionnez **Authentifier** dans l’application. Votre entreprise ou établissement scolaire peut vous demander d’entrer un code confidentiel avant de vous authentifier.</li><li>**Utiliser le code de vérification.** Dans ce mode, l’application d’authentification génère un code de vérification mis à jour toutes les 30 secondes. Entrez le code de vérification le plus récent dans l’écran de connexion.<br>L’application Microsoft Authenticator est disponible pour [Android](https://go.microsoft.com/fwlink/?linkid=866594) et [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Téléphone d’authentification | <ul><li>L’option **Appel téléphonique** passe un appel vocal automatisé au numéro de téléphone indiqué. Répondez à l’appel et appuyez sur la touche # du clavier du téléphone pour vous authentifier.</li><li>L’option **SMS** envoie un SMS contenant un code de vérification. Selon l’invite contenue dans le SMS, répondez au SMS ou entrez le code de vérification fourni dans l’interface de connexion.</li></ul> |
    | Téléphone de bureau | Passe un appel vocal automatisé au numéro de téléphone indiqué. Répondez à l’appel et appuyez sur la touche # du clavier du téléphone pour vous authentifier. |

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez accédé à la page **Vérification de sécurité supplémentaire**, vous devez sélectionner et configurer votre méthode de vérification à deux facteurs :

- [Configurer votre appareil mobile comme méthode de vérification](multi-factor-authentication-setup-phone-number.md)

- [Configurer votre téléphone de bureau comme méthode de vérification](multi-factor-authentication-setup-office-phone.md)

- [Configurer l’application Microsoft Authenticator comme méthode de vérification](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Ressources associées

- [Gérer les paramètres de la vérification à deux facteurs](multi-factor-authentication-end-user-manage-settings.md)

- [Manage app passwords](multi-factor-authentication-end-user-app-passwords.md) (Gérer les mots de passe d’application)

- [Se connecter à l’aide de la vérification en deux étapes](multi-factor-authentication-end-user-signin.md)

- [Obtenir de l’aide pour la vérification à deux facteurs](multi-factor-authentication-end-user-troubleshoot.md) 
