---
title: Résoudre les problèmes de la vérification en deux étapes - Azure Active Directory | Microsoft Docs
description: Fournit la marche à suivre en cas d’erreur liée à Azure Multi-Factor Authentication et à la vérification en deux étapes.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: lizross
ms.reviewer: kexia
ms.openlocfilehash: 2ede7a172eda1122b0668984cc8e11937b3ae852
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768181"
---
# <a name="get-help-with-two-step-verification"></a>Obtenir de l’aide pour la vérification en deux étapes

La vérification en deux étapes est une fonctionnalité de sécurité qui protège les comptes de votre organisation. Elle est plus sécurisée qu’un simple mot de passe, car elle s’appuie sur deux formes d’authentification : quelque chose que vous connaissez et quelque chose que vous avez avec vous. La chose que vous connaissez est votre mot de passe, et la chose que vous avez avec vous est votre téléphone ou un autre appareil. Avec la vérification en deux étapes, vous pouvez empêcher les pirates de se connecter à votre compte, même s’ils connaissent votre mot de passe.

Microsoft propose la vérification en deux étapes, mais c’est votre organisation qui décide si vous devez utiliser cette fonctionnalité. Vous ne pouvez pas la désactiver si votre organisation vous demande de l’utiliser, tout comme vous ne pouvez pas refuser l’utilisation d’un mot de passe pour protéger votre compte.

>[!Note]
>Pour plus d’informations sur l’utilisation de la vérification en deux étapes avec votre compte Microsoft personnel, consultez l’article [Vérification en deux étapes](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="why-do-i-need-to-use-another-verification-method"></a>Pourquoi dois-je utiliser une autre méthode de vérification ?

Plusieurs raisons peuvent expliquer la nécessité de changer de méthode de vérification. Par exemple : 

- **Vous avez oublié votre téléphone ou votre appareil.** Vous laissez parfois votre téléphone à votre domicile, mais vous avez tout de même besoin de vous connecter au bureau. D’abord, vous devez essayer de vous connecter à l’aide d’une méthode qui ne nécessite pas l’usage de votre téléphone.

- **Vous avez perdu votre téléphone ou vous avez changé de numéro de téléphone.** Si vous avez perdu votre téléphone ou avez changé de numéro, vous pouvez vous connecter à l’aide d’une autre méthode ou demander à votre administrateur d’effacer vos paramètres. Nous vous recommandons vivement d’informer votre administrateur en cas de perte ou de vol de votre téléphone, afin que les mises à jour nécessaires puissent être appliquées à votre compte. Une fois vos paramètres effacés, la prochaine fois que vous vous connecterez, vous serez invité à vous [réinscrire à la vérification en deux étapes](multi-factor-authentication-end-user-first-time.md).

- **Vous n’avez pas reçu le SMS ou l’appel d’authentification.** Plusieurs raisons peuvent expliquer le fait que vous ne receviez pas de SMS ou d’appel. Si vous receviez des SMS ou des appels et que ce n’est plus le cas, il s’agit probablement d’un problème avec le fournisseur de téléphonie, et non d’un problème lié à votre compte. Si vous recevez les messages en retard en raison d’un mauvais signal, nous vous recommandons d’utiliser [l’application Microsoft Authenticator](user-help-auth-app-download-install.md) sur votre appareil mobile. Cette application peut générer des codes de sécurité aléatoires pour la connexion, sans nécessiter de signal de mobile ou de connexion Internet.<br><br>Pour savoir si vous pouvez recevoir des SMS, demandez à un ami de vous en envoyer un. Si vous avez reçu plusieurs messages, utilisez le code du message le plus récent.

- **Les mots de passe d’application ne fonctionnent pas.** Les mots de passe d’application viennent remplacer les mots de passe standard de vos anciennes applications de bureau qui ne prennent pas en charge la vérification en deux étapes. En premier lieu, vérifiez que vous avez correctement tapé le mot de passe. Si le problème n’est pas résolu, essayez de vous connecter pour [créer un nouveau mot de passe](multi-factor-authentication-end-user-app-passwords.md), ou contactez votre administrateur pour [supprimer vos mots de passe d’application existants](../authentication/howto-mfa-userdevicesettings.md) afin d’en créer un nouveau.

## <a name="sign-in-using-another-verification-method"></a>Se connecter à l’aide d’une autre méthode de vérification

1. Connectez-vous à votre compte comme vous le faites d’habitude, puis cliquez sur le lien **Sign in another way** (Se connecter autrement) situé dans la page **Vérification en deux étapes**.

    ![Changer de méthode de vérification](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Si vous ne voyez pas le lien **Sign in another way** (Se connecter autrement), cela signifie que vous n’avez pas configuré d’autres méthodes de vérification. Dans ce cas, vous devez contacter votre administrateur pour qu’il vous aide à vous connecter à votre compte. Une fois connecté, veillez à ajouter d’autres méthodes de vérification. Pour plus d’informations sur l’ajout de méthodes de vérification, consultez l’article [Gérer les paramètres de la vérification en deux étapes](multi-factor-authentication-end-user-manage-settings.md).<br><br>Si vous voyez le lien, mais ne voyez toujours pas les autres méthodes de vérification, contactez votre administrateur pour qu’il vous aide à vous connecter à votre compte.

2. Choisissez votre méthode de vérification et poursuivez le processus de vérification en deux étapes.

3. Une fois de retour dans votre compte, vous pouvez mettre à jour vos méthodes de vérification (si nécessaire). Pour plus d’informations sur l’ajout ou le changement de méthodes, consultez l’article [Gérer les paramètres de la vérification en deux étapes](multi-factor-authentication-end-user-manage-settings.md).

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Je n’ai pas trouvé de réponse à mon problème

Si malgré ces solutions, vous rencontrez encore des problèmes, contactez votre administrateur.

## <a name="related-topics"></a>Rubriques connexes

* [Gérer les paramètres de la vérification en deux étapes](multi-factor-authentication-end-user-manage-settings.md)

* [FAQ sur l’application Microsoft Authenticator](user-help-auth-app-faq.md)
