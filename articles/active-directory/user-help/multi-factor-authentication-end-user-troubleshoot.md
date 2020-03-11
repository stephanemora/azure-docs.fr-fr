---
title: Problèmes courants avec l’authentification à 2 facteurs de compte – Azure AD
description: Solutions pour certains des problèmes de vérification en deux étapes les plus courants et pour votre compte professionnel ou scolaire.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 7dfd15f6270827382a6456231e8b1699986cd5f6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191365"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Problèmes courants avec la vérification en deux étapes et votre compte professionnel ou scolaire

Lorsque votre organisation Azure Active Directory (Azure AD) active la vérification en deux étapes, la connexion de votre compte professionnel ou scolaire nécessite une combinaison de votre nom d’utilisateur, votre mot de passe et un appareil mobile ou téléphone. Elle est plus sécurisée qu’un simple mot de passe, car elle s’appuie sur deux formes d’authentification : quelque chose que vous connaissez et quelque chose que vous avez sur vous. La vérification en deux étapes peut aider à empêcher les pirates de se faire passer pour vous, car même s’ils ont votre mot de passe, il est probable qu’ils n’ont pas votre appareil.

<center>

![Image conceptuelle des méthodes d’authentification](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Certains problèmes courants de vérification à deux facteurs semblent se produire plus fréquemment que nous le souhaiterions. Nous avons rédigé cet article afin d’aborder les problèmes les plus courants et de proposer certains correctifs possibles.

>[!Important]
>Si vous êtes un administrateur, vous trouverez plus d’informations sur la méthode à suivre pour configurer et gérer votre environnement Azure AD dans la [documentation relative à Azure AD](https://docs.microsoft.com/azure/active-directory).
>
>Ce contenu est également destiné à être utilisé uniquement avec votre compte professionnel ou scolaire, lequel vous a été fourni par votre organisation (par exemple, alain@contoso.com). Si vous rencontrez des problèmes avec la vérification en deux étapes et votre compte Microsoft personnel, compte que vous avez configuré pour vous-même (par exemple, danielle@outlook.com), consultez [Activation ou désactivation de la vérification en deux étapes de votre compte Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Je n’ai pas mon appareil mobile avec moi

Cela peut arriver. Vous avez laissé votre appareil mobile chez vous, et maintenant vous ne pouvez pas utiliser votre téléphone pour confirmer votre identité. Si vous avez ajouté une autre méthode pour vous connecter à votre compte, par exemple votre téléphone de bureau, vous devriez pouvoir utiliser cette méthode maintenant. Si vous n’avez jamais ajouté de méthode de vérification supplémentaire, contactez le support technique de votre organisation et demandez-lui de vous aider à vous reconnecter à votre compte.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Pour vous connecter à votre compte professionnel ou scolaire à l’aide d’une autre méthode de vérification

1. Connectez-vous à votre compte, mais sélectionnez le lien **Se connecter autrement** dans la page **Vérification en deux étapes**.

    ![Changer de méthode de vérification](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Si vous ne voyez pas le lien **Sign in another way** (Se connecter autrement), cela signifie que vous n’avez pas configuré d’autres méthodes de vérification. Dans ce cas, vous devez contacter votre administrateur pour qu’il vous aide à vous connecter à votre compte.

2. Choisissez votre autre méthode de vérification et poursuivez le processus de vérification à deux facteurs.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>J’ai perdu mon appareil mobile ou il a été volé

En cas de perte ou de vol de votre appareil mobile, vous pouvez vous connecter à l’aide d’une autre méthode, ou vous pouvez demander au support technique de votre organisation d’effacer vos paramètres. Nous vous recommandons vivement d’informer le support technique de votre organisation en cas de perte ou de vol de votre téléphone, afin que les mises à jour nécessaires puissent être appliquées à votre compte. Une fois vos paramètres effacés, la prochaine fois que vous vous connecterez, vous serez invité à vous [réinscrire à la vérification à deux facteurs](multi-factor-authentication-end-user-first-time.md).

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Je ne reçois pas le code de vérification envoyé à mon appareil mobile

Le fait de ne pas recevoir votre code de vérification est un problème courant, généralement lié à votre appareil mobile et à ses paramètres. Voici quelques solutions possibles :

Procédure | Conseils
--------- | ------------
Redémarrer votre appareil mobile | Parfois, votre appareil a simplement besoin d’une actualisation. Le redémarrage de votre appareil met fin à tous les processus ou services en arrière-plan en cours d’exécution et pourrait provoquer des problèmes, de même que l’actualisation des composants principaux de votre appareil, en les redémarrant au cas où ils se seraient bloqués à un moment donné.
Vérifier que vos informations de sécurité sont correctes | Vérifiez que les informations relatives à votre méthode de vérification de sécurité sont exactes, en particulier vos numéros de téléphone. Si vous avez entré un numéro de téléphone incorrect, toutes vos alertes seront dirigées vers ce numéro incorrect. Heureusement, cet utilisateur ne pourra rien faire avec les alertes, mais cela ne vous aidera pas non plus à vous connecter à votre compte. Pour vérifier que vos informations sont correctes, consultez les instructions fournies dans l’article [Gérer les paramètres de la vérification à deux facteurs](multi-factor-authentication-end-user-manage-settings.md).
Vérifier que les notifications sont activées | Vérifiez que les notifications sont activées sur votre appareil mobile et que vous avez sélectionné une méthode de notification qui autorise les appels téléphoniques, votre application d’authentification et votre application de messagerie (pour les messages texte) à envoyer des notifications d’alerte visibles à votre appareil mobile.
Vérifier que votre appareil capte un signal et dispose d’une connexion Internet | Vérifiez que vos appels téléphoniques et vos SMS parviennent à votre appareil mobile. Demandez à un ami de vous appeler et de vous envoyer un SMS pour vérifier que vous les recevez bien. Si ce n’est pas le cas, commencez par vérifier que votre appareil mobile est allumé. S’il est allumé mais que vous ne recevez toujours pas l’appel ou le SMS, le problème est sans doute lié à votre réseau et vous devrez le signaler à votre fournisseur. Si vous rencontrez souvent des problèmes liés aux signaux, nous vous recommandons d’installer et d’utiliser l’[application Microsoft Authenticator](user-help-auth-app-download-install.md) sur votre appareil mobile. L’application Authenticator peut générer des codes de sécurité aléatoires pour la connexion, sans nécessiter de signal de réseau mobile ou de connexion Internet.
Désactiver la fonctionnalité Ne pas déranger | Vérifiez que vous n’avez pas activé la fonctionnalité **Ne pas déranger** sur votre appareil mobile. Quand elle est activée, les notifications ne sont pas autorisées à vous alerter sur votre appareil mobile. Pour obtenir des instructions sur la façon de désactiver cette fonctionnalité, consultez le manuel de votre appareil mobile.
Débloquer les numéros de téléphone | Aux États-Unis, les appels vocaux de Microsoft proviennent des numéros suivants : +1 (866) 539 4191, +1 (855) 330 8653 et +1 (877) 668 6536.
Vérifier les paramètres liés à la batterie | Cela peut sembler étrange de prime abord, mais si vous avez configuré l’optimisation de la batterie de façon à empêcher les applications les moins utilisées de rester actives en arrière-plan, cela aura probablement affecté votre système de notification. Pour essayer de résoudre ce problème, désactivez l’optimisation de la batterie pour votre application d’authentification et votre application de messagerie, puis réessayez de vous connecter à votre compte.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Je ne suis pas invité à entrer ma deuxième information de vérification

Si vous vous êtes connecté à votre compte professionnel ou scolaire à l’aide de votre nom d’utilisateur et de votre mot de passe, mais que vous n’êtes pas invité à fournir vos informations de vérification de sécurité supplémentaires, vous n’avez peut-être pas encore configuré votre appareil. Votre appareil mobile doit être configuré pour fonctionner avec votre méthode spécifique de vérification de sécurité supplémentaire. Pour vérifier que vous avez activé votre appareil mobile et que vous pouvez l’utiliser avec votre méthode de vérification, consultez l’article [Gérer les paramètres de la vérification à deux facteurs](multi-factor-authentication-end-user-manage-settings.md). Si vous savez que vous n’avez pas configuré votre appareil ou votre compte, vous pouvez le faire maintenant en suivant les étapes décrites dans l’article [Configurer mon compte pour la vérification en deux étapes](multi-factor-authentication-end-user-first-time.md).

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>J’ai un nouveau numéro de téléphone et je souhaite l’ajouter

Si vous avez reçu un nouveau numéro de téléphone, vous devez mettre à jour les détails de votre méthode de vérification de sécurité afin que les invites de vérification soient dirigées vers le bon numéro. Pour mettre à jour votre méthode de vérification, suivez les étapes décrites dans la section **Ajouter ou modifier votre numéro de téléphone** de l’article [Gérer les paramètres de la vérification à deux facteurs](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number).

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>J’ai un nouvel appareil mobile et je souhaite l’ajouter

Si vous avez reçu un nouvel appareil mobile, vous devez le configurer pour qu’il fonctionne avec la vérification à deux facteurs. Cette solution comprend plusieurs étapes :

1. Configurez votre appareil pour qu’il fonctionne avec votre compte professionnel ou scolaire en suivant les étapes décrites dans l’article [Configurer mon compte pour la vérification en deux étapes](multi-factor-authentication-end-user-first-time.md).

1. Mettez à jour les informations de votre compte et de votre appareil dans la page **Vérification de sécurité supplémentaire**, en supprimant votre ancien appareil et en ajoutant le nouveau. Pour plus d’informations, consultez l’article [Gérer les paramètres de la vérification à deux facteurs](multi-factor-authentication-end-user-manage-settings.md).

Étapes facultatives :

- Téléchargez, installez et configurez l’application Microsoft Authenticator sur votre appareil mobile en suivant les étapes de l’article [Télécharger et installer l’application Microsoft Authenticator](user-help-auth-app-download-install.md).

- Activez la vérification à deux facteurs pour vos appareils de confiance en suivant les étapes de la section **Activer les invites de vérification à deux facteurs sur un appareil de confiance** de l’article [Gérer les paramètres de la vérification à deux facteurs](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>J’ai des difficultés à me connecter à mon appareil mobile quand je suis en déplacement

Il peut s’avérer plus difficile d’utiliser une méthode de vérification liée à l’appareil mobile, comme un SMS, quand vous êtes dans un autre pays. Il est également possible que des frais d’itinérance vous soient facturés. Dans ce cas, nous vous recommandons d’utiliser l’application Microsoft Authenticator, avec l’option de connexion à un point d’accès Wi-Fi. Pour plus d’informations sur la façon de télécharger, d’installer et de configurer l’application Microsoft Authenticator sur votre appareil mobile, consultez l’article [Télécharger et installer l’application Microsoft Authenticator](user-help-auth-app-download-install.md).

## <a name="i-cant-get-my-app-passwords-to-work"></a>Mes mots de passe d’application ne fonctionnent pas

Les mots de passe d’application viennent remplacer les mots de passe standard de vos anciennes applications de bureau qui ne prennent pas en charge la vérification à deux facteurs. En premier lieu, vérifiez que vous avez correctement tapé le mot de passe. Si cela ne résout pas le problème, essayez de créer un nouveau mot de passe d’application pour l’application en suivant les étapes décrites dans la section **Créer et supprimer des mots de passe d’application à l’aide du portail MyApps** de l’article [Gérer les mots de passe d’application pour la vérification en deux étapes](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page).

## <a name="i-cant-turn-two-factor-verification-off"></a>Je ne peux pas désactiver la vérification en deux étapes

Si vous utilisez la vérification à deux facteurs avec votre compte professionnel ou scolaire (par exemple, alain@contoso.com), cela signifie très probablement que votre organisation a décidé de vous contraindre à utiliser cette fonctionnalité de sécurité supplémentaire. Comme votre organisation a décidé que cette fonctionnalité était obligatoire, vous n’avez aucun moyen de la désactiver individuellement. En revanche, si vous utilisez la vérification à deux facteurs avec un compte personnel, comme alain@outlook.com, vous avez la possibilité d’activer et de désactiver la fonctionnalité. Pour obtenir des instructions sur la façon de contrôler la vérification en deux étapes pour vos comptes personnels, consultez [Activation ou désactivation de la vérification en deux étapes de votre compte Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Je n’ai pas trouvé de réponse à mon problème

Si, malgré ces solutions, vous rencontrez encore des problèmes, contactez le support technique de votre organisation pour obtenir de l’aide.

## <a name="related-articles"></a>Articles connexes

- [Gérer les paramètres de la vérification à deux facteurs](multi-factor-authentication-end-user-manage-settings.md)

- [Configurer mon compte pour la vérification en deux étapes](multi-factor-authentication-end-user-first-time.md)

- [Forum aux questions sur l’application Microsoft Authenticator](user-help-auth-app-faq.md)
