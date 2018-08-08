---
title: Configurer les informations de sécurité pour utiliser une application d’authentification - Azure Active Directory | Microsoft Docs
description: Configurez vos informations de sécurité pour vérifier votre identité avec l’application Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: bd62ff03c3158743337ce958eb1bda7c7bd2a7f6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347699"
---
# <a name="set-up-security-info-to-use-an-authenticator-app-preview"></a>Configurer les informations de sécurité pour utiliser une application d’authentification (préversion)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Pour configurer vos informations de sécurité, vous devez vous connecter à votre compte professionnel ou scolaire, puis suivre le processus d’inscription. Si vous n’avez jamais configuré vos informations de sécurité, vous êtes amené à le faire maintenant.

## <a name="set-up-the-microsoft-authenticator-app"></a>Configurer l'application Microsoft Authenticator

En fonction des paramètres de votre organisation, vous pouvez être invité à paramétrer l’application Microsoft Authenticator lors de la connexion. Sinon, pour commencer à configurer l’application Microsoft Authenticator dans les informations de sécurité, suivez les étapes indiquées dans l’article [Manage your security info](security-info-manage-settings.md) (Gérer vos informations de sécurité).

Pour télécharger et découvrir l’application Microsoft Authenticator, consultez l’article [Bien démarrer avec l’application Microsoft Authenticator](microsoft-authenticator-app-how-to.md).

>[!Note]
>Si vous ne souhaitez pas utiliser l’application Microsoft Authenticator, vous pouvez choisir une autre application au cours de la configuration. Cet article utilise l’application Microsoft Authenticator. Si vous ne voyez pas l’option d’application d’authentification, il est possible que votre organisation ne vous autorise pas à utiliser l’application d’authentification à des fins de vérification. Dans ce cas, vous devez choisir une autre méthode ou contacter votre administrateur pour obtenir de l’aide.

### <a name="to-use-the-microsoft-authenticator-app"></a>Pour utiliser l’application Microsoft Authenticator

1. Sélectionnez l’option **application Authenticator**.

    L’Assistant **Obtenir l’application** s’affiche.

    ![Assistant Obtenir l’application, écran initial](media/security-info/security-info-auth-app-wizard.png)

    Si vous ne souhaitez pas utiliser l’application Microsoft Authenticator, vous pouvez cliquer sur le lien **Je souhaite utiliser une autre application d’authentification** sur l’écran **Obtenir l’application**.

2. Après avoir installé l’application Microsoft Authenticator, sélectionnez **Suivant**.

    Si vous y êtes invité, autorisez les notifications, ajoutez un nouveau compte puis sélectionnez **Compte professionnel ou scolaire**.

3. Sélectionnez **Suivant**.

    L’écran **Scanner le code QR** s’affiche.

    ![Scanner le code QR à l’aide de l’application Authenticator](media/security-info/security-info-scan-qr.png)

4. Ouvrez l’application Microsoft Authenticator, sélectionnez **Ajouter un compte** grâce à l’icône **Personnaliser et contrôler** dans l’angle supérieur droit, puis sélectionnez **Compte professionnel ou scolaire**. 

5. Si vous disposez d’une application de lecteur de code QR, scannez le code fourni. Si vous ne disposez pas d’une application de lecteur de code, vous pouvez sélectionner le **lien Can’t scan the QR code** (Impossible de scanner le code QR) et entrer manuellement le code et l’URL dans l’application Microsoft Authenticator.

6. Utilisez l’application Microsoft Authenticator pour approuver la notification afin d’activer l’application.

    Vos informations de sécurité sont alors mises à jour pour utiliser l’application Microsoft Authenticator, afin de vérifier votre identité lorsque vous utilisez la vérification en deux étapes ou une réinitialisation du mot de passe en libre-service.

## <a name="additional-security-info-options"></a>Options d’informations de sécurité supplémentaires

Vous avez la possibilité de choisir la façon d’être contacté par votre organisation pour la vérification de votre identité, en fonction ce que vous essayez de faire. Ces options sont les suivantes :

- **SMS sur appareil mobile.** Entrez votre numéro d’appareil mobile et recevez un code par SMS, à utiliser pour la vérification en deux étapes ou la réinitialisation de mot de passe. Pour des instructions pas à pas sur la façon de vérifier votre identité au moyen d’un SMS, consultez [Set up security info to use text messaging (SMS)](security-info-setup-text-msg.md) (Configurer les informations de sécurité pour utiliser la messagerie texte [SMS]).

- **Appel sur appareil mobile ou téléphone professionnel.** Entrez votre numéro d’appareil mobile et recevez un appel téléphonique pour la réinitialisation de mot de passe ou la vérification en deux étapes. Pour des instructions pas à pas sur la façon de vérifier votre identité au moyen d’un numéro de téléphone, consultez [Set up security info to use phone calls](security-info-setup-phone-number.md) (Configurer les informations de sécurité pour utiliser les appels téléphoniques).

- **Adresse e-mail.** Entrez votre adresse e-mail professionnelle ou scolaire pour recevoir un e-mail de réinitialisation de mot de passe. Cette option n’est pas disponible pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer votre e-mail, consultez [Set up security info to use email](security-info-setup-email.md) (Configurer les informations de sécurité pour utiliser l’e-mail).

- **Questions de sécurité.** Répondez à certaines questions de sécurité créées par votre administrateur pour votre organisation. Cette option est uniquement disponible pour la réinitialisation du mot de passe et non pour la vérification en deux étapes. Pour obtenir des instructions détaillées sur la façon de configurer vos questions de sécurité, consultez l’article [Set up security info to use security questions](security-info-setup-questions.md) (Configurer les informations de sécurité pour utiliser des questions de sécurité).
    
    >[!Note]
    >Si certaines de ces options ne sont pas disponibles, il est très probable que votre organisation n’autorise pas ces méthodes. Si tel est le cas, vous devez choisir une méthode disponible ou contacter votre administrateur pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes

- Si vous devez mettre à jour vos informations de sécurité, suivez les instructions dans l’article [Manage your security info](security-info-manage-settings.md) (Gérer vos informations de sécurité).

- Réinitialisez votre mot de passe à partir du [portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com/) si vous l’avez perdu ou oublié, ou suivez les étapes de l’article [Reset your work or school password](user-help-reset-password.md) (Réinitialiser votre mot de passe professionnel ou scolaire).

- Obtenez des conseils de dépannage et de l’aide pour résoudre des problèmes de connexion dans l’article [Quand vous ne pouvez pas vous connecter à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).