---
title: Gérer vos informations de sécurité - Azure Active Directory | Microsoft Docs
description: Découvrez comment gérer vos informations de sécurité, notamment comment vous servir de vos paramètres de vérification en deux étapes.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 34023e74fb30f202760b35120da78a18ca653d69
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815359"
---
# <a name="manage-your-security-info-preview"></a>Gérer vos informations de sécurité (préversion)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Vous pouvez utiliser vos informations de sécurité pour vous connecter à votre compte professionnel ou scolaire, ou pour réinitialiser votre mot de passe.

Lorsque vous vous connectez, en fonction des paramètres de votre organisation, vous pouvez voir une option à cocher indiquant **Ne plus me demander pendant X jours**. Cette case à cocher vous permet de rester connecté à votre appareil le nombre de jours qu’autorise votre administrateur, sans nécessiter de revérification.

## <a name="change-your-info"></a>Changer vos informations
Vous pouvez mettre à jour ou ajouter des informations de sécurité, ou changer vos paramètres par défaut, selon ce qui est autorisé par votre administrateur et votre organisation.

### <a name="to-change-your-info"></a>Pour changer vos informations

1. Connectez-vous à votre compte professionnel ou scolaire.

2. Accédez à myapps.microsoft.com, sélectionnez votre nom en haut à droite de la page, puis sélectionnez **Profil**.

3. Dans la zone **Gérer le compte**, sélectionnez **Modifier les informations de sécurité**.

    ![Écran du profil, avec le lien Modifier les informations de sécurité mis en évidence](media/security-info/security-info-profile.png)

4. Utilisez votre méthode par défaut pour approuver l’accès et afficher les détails de vos informations de sécurité actuelles, si l’administrateur a configuré cette expérience pour votre organisation.

5. Dans la page **Protéger votre compte**, vous pouvez :

    - Sélectionner **Ajouter des informations de sécurité** pour ajouter des méthodes supplémentaires.

    - Sélectionner **Modifier le paramètre par défaut** pour changer votre méthode par défaut.

    - Sélectionnez l’icône du **crayon** en regard d’une méthode existante pour mettre à jour vos informations.

    ![Écran des informations de sécurité, avec les informations existantes modifiables](media/security-info/security-info-edit.png)

6. Après avoir apporté des modifications, celles-ci sont enregistrées lorsque vous quittez la page.

Si vous ne voyez pas ces options, ou si vous ne pouvez pas accéder à la page myapps.microsoft.com, il est possible que votre organisation utilise des options personnalisées ou une page personnalisée. Vous devez contacter votre administrateur pour obtenir de l’aide.

## <a name="manage-your-security-info-for-a-lost-or-potentially-compromised-device"></a>Gérer vos informations de sécurité en cas d’appareil perdu ou d’appareil dont la sécurité est potentiellement compromise

Si vous perdez votre appareil ou si la sécurité de votre appareil est compromise, vous devez recommencer le processus de vérification pour tous vos appareils précédemment approuvés.

### <a name="to-manage-your-security-info-for-lost-or-potentially-compromised-devices"></a>Pour gérer vos informations de sécurité en cas d’appareils perdus ou d’appareils dont la sécurité est potentiellement compromise

1. Connectez-vous à votre compte professionnel ou scolaire.

2. Accédez à myapps.microsoft.com, sélectionnez votre nom en haut à droite de la page, puis sélectionnez **Profil**.

3. Dans la zone **Gérer le compte**, sélectionnez **Oublier MFA sur les appareils mémorisés**.
    
    Choisir cette option signifie que vous allez devoir recommencer le processus MFA (Multi-Factor Authentication) après vous être connecté.

    ![Écran du profil avec le lien d’oubli mis en évidence](media/security-info/security-info-forget.png)

## <a name="common-problems-and-solutions-with-your-security-info"></a>Problèmes courants relatifs à vos informations de sécurité, et leurs solutions

Cet article vous aide à résoudre les problèmes liés à vos informations de sécurité, notamment les problèmes concernant la vérification en deux étapes.

|Problème|Solution|
|-------|--------|
|Je n’ai pas mon téléphone sur moi|Il peut arriver que vous n’ayez pas votre téléphone en permanence avec vous et que vous souhaitiez néanmoins vous connecter à votre compte professionnel ou scolaire. Pour résoudre ce problème, vous pouvez vous connecter avec une autre méthode d’authentification ne nécessitant pas l’utilisation de votre téléphone, par exemple votre numéro de téléphone au bureau. Pour ajouter des méthodes supplémentaires à vos informations de sécurité, suivez les étapes décrites dans la section [Changer vos informations](#change-your-info).|
|Mon téléphone a été perdu ou volé|Malheureusement, la perte ou le vol de votre téléphone peut se produire. Dans ce cas, il est fortement recommandé d’en informer votre organisation, afin que le service informatique puisse réinitialiser vos mots de passe d’application et effacer les appareils mémorisés dans votre liste d’appareils de confiance. Vous pouvez également procéder vous-même à l’oubli de vos appareils de confiance en suivant les étapes décrites dans la section [Gérer vos informations de sécurité en cas d’appareil perdu ou d’appareil dont la sécurité est potentiellement compromise](#manage-your-security-info-for-a-lost-or-potentially-compromised-device).|
|J’ai un nouveau numéro de téléphone|Vous disposez de deux moyens pour résoudre ce problème. Vous pouvez vous connecter à l’aide d’une autre méthode d’authentification qui ne demande pas de numéro de téléphone, comme un e-mail ou, si cette solution n’est pas envisageable, vous pouvez contacter le service informatique de votre organisation pour que celui-ci se charge de l’effacement de vos paramètres. Pour ajouter des méthodes supplémentaires à vos informations de sécurité, suivez les étapes décrites dans la section [Changer vos informations](#change-your-info).|
|Ma méthode par défaut est incorrecte|Vous pouvez mettre à jour votre méthode par défaut dans vos options de sécurité. Pour de plus amples renseignements, vous pouvez consulter la section [Changer vos informations](#change-your-info).|
|Je ne reçois pas de SMS ou d’appel sur mon appareil mobile|Si vous avez déjà reçu des SMS ou des appels téléphoniques sur votre appareil mobile, ce problème vient très probablement de l’opérateur de téléphonie, et non de votre compte. Vérifiez que vous captez un signal de bonne qualité et que vous êtes en mesure de recevoir des SMS et des appels téléphoniques. Vous pouvez demander à un ami de vous appeler ou de vous envoyer un SMS pour faire un essai.<br><br>Si vous recevez bien SMS et messages téléphoniques, mais que vous n’avez toujours pas reçu la notification, essayez d’utiliser une autre méthode. Vous pouvez ajouter des méthodes supplémentaires à vos informations de sécurité en suivant les étapes décrites dans la section [Changer vos informations](#change-your-info). Si vous n’avez pas d’autre méthode à ajouter, vous pouvez contacter le support technique de votre entreprise et lui demander d’effacer vos paramètres, pour que vous puissiez configurer vos méthodes la prochaine fois que vous vous connecterez.<br><br>Si vous remarquez souvent des problèmes dus à une mauvaise réception de signal, nous vous recommandons d’utiliser l’application Microsoft Authenticator sur votre appareil mobile. L’application peut générer des codes de sécurité aléatoires qui vous permettent de vous connecter et qui ne nécessitent pas de signal téléphonique ou de connexion Internet. Pour plus d’informations sur l’application Microsoft Authenticator, consultez l’article [Bien démarrer avec l’application Microsoft Authenticator](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to).|
|Aucune des options de ce tableau n’a résolu mon problème|Si malgré ces étapes de dépannage, vous rencontrez toujours des difficultés, contactez le support de votre entreprise, il devrait être en mesure de vous aider.|

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les informations de sécurité avec [Vue d’ensemble des informations de sécurité (préversion)](user-help-security-info-overview.md).

- Pour plus d’informations sur la vérification en deux étapes, consultez l’article [Vue d’ensemble de la vérification en deux étapes](user-help-two-step-verification-overview.md). 

- Suivez l’un de ces guides pratiques pour en savoir plus sur la configuration de vos appareils dans le domaine des informations de sécurité :

    - [Configurer les informations de sécurité pour utiliser une application d’authentification](security-info-setup-auth-app.md)

    - [Configurer les informations de sécurité pour utiliser un appel téléphonique](security-info-setup-phone-number.md)

    - [Configurer les informations de sécurité pour utiliser les SMS](security-info-setup-text-msg.md)

    - [Configurer les informations de sécurité pour utiliser l’e-mail](security-info-setup-email.md)

    - [Configurer les informations de sécurité pour utiliser les questions de sécurité](security-info-setup-questions.md)

- Si vous avez perdu ou oublié votre mot de passe, réinitialisez-le à partir du [portail de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/), ou suivez les étapes de l’article [Réinitialiser votre mot de passe professionnel ou scolaire](user-help-reset-password.md).

- Obtenez des conseils de dépannage et de l’aide pour les problèmes de connexion en consultant l’article [Quand vous ne pouvez pas vous connecter à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).