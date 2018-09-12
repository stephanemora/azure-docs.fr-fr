---
title: Configurer les informations de sécurité pour utiliser un appel téléphonique - Azure Active Directory | Microsoft Docs
description: Configurez vos informations de sécurité pour vérifier votre identité au moyen d’un numéro d’appareil mobile ou de téléphone professionnel.
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
ms.openlocfilehash: 78b085bfad4dd3bab9d8b83f540b6ea315f911b9
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162102"
---
# <a name="set-up-security-info-to-use-phone-calls-preview"></a>Configurer les informations de sécurité pour utiliser un appel téléphonique (préversion)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Pour configurer vos informations de sécurité, vous devez vous connecter à votre compte professionnel ou scolaire et suivre la procédure d’inscription. Si vous n’avez jamais configuré vos informations de sécurité, vous êtes amené à le faire maintenant.

## <a name="set-up-phone-calls"></a>Configurer les appels téléphoniques

En fonction des paramètres de votre organisation, vous pouvez être invité à ajouter un numéro de téléphone à vos informations de sécurité lorsque vous vous connectez. La configuration d’appels téléphoniques dans les informations de sécurité peut sinon s’effectuer en suivant les étapes dans [Gérer vos informations de sécurité](security-info-manage-settings.md).

>[!Note]
>Les informations de sécurité ne prennent pas en charge les numéros de poste. Même si vous ajoutez le bon format, +33 4 25 55 51 23-12345, les numéros de poste sont supprimés avant l’appel.<br>Si vous ne voyez pas l’option de téléphone, il est possible que votre organisation ne vous autorise pas à utiliser les appels téléphoniques à des fins de vérification. Si tel est le cas, vous devez choisir une autre méthode, ou contacter votre administrateur pour obtenir de l’aide.

### <a name="to-use-your-phone-number"></a>Pour utiliser votre numéro de téléphone

1. Sélectionnez l’option **Téléphone**.

    L’Assistant **Configurer votre téléphone** s’affiche.

    ![Configurer le code de votre pays ou de votre région, et votre numéro de téléphone](media/security-info/security-info-keep-secure-setup-phone.png)

2. Dans la liste déroulante, sélectionnez **Pays ou région**, puis entrez votre numéro de téléphone (y compris l’indicatif, le cas échéant) dans la zone **Numéro de téléphone**. Sélectionnez ensuite l’option **M’appeler**, puis sélectionnez **Suivant**.

    Vous recevez un appel téléphonique pour vérifier que vous avez tapé le bon numéro de téléphone. À ce stade, il vous est demandé d’appuyer sur la touche du signe dièse (#) pour confirmer et terminer votre configuration.

    ![Vérification de l’écran de votre téléphone indiquant que l’appel a bien été reçu](media/security-info/security-info-keep-secure-verify-phone-call.png)

    Vos informations de sécurité sont alors mises à jour pour utiliser le numéro de téléphone, afin de contrôler votre identité lorsque vous utilisez la vérification en deux étapes ou une réinitialisation du mot de passe en libre-service.

    >[!Note]
    >Si vous préférez recevoir un SMS plutôt qu’un appel téléphonique sur votre appareil mobile, suivez les étapes indiquées dans l’article [Configurer les informations de sécurité pour utiliser la messagerie texte (SMS)](security-info-setup-text-msg.md).

## <a name="additional-security-info-options"></a>Options d’informations de sécurité supplémentaires

Vous avez la possibilité de choisir la façon d’être contacté par votre organisation pour la vérification de votre identité, en fonction de ce que vous essayez de faire. Ces options sont les suivantes :

- **Application d’authentification.** Téléchargez et utilisez une application d’authentification pour obtenir une notification d’approbation ou un code d’approbation généré de manière aléatoire pour la réinitialisation du mot de passe ou la vérification en deux étapes. Pour obtenir des instructions détaillées sur la configuration et l’utilisation de l’application Microsoft Authenticator, consultez [Configurer les informations de sécurité pour utiliser une application d’authentification](security-info-setup-auth-app.md).

- **SMS sur appareil mobile.** Entrez votre numéro de téléphone mobile et recevez un code par SMS, à utiliser pour la vérification en deux étapes ou la réinitialisation de mot de passe. Pour obtenir des instructions détaillées sur la vérification de votre identité par SMS, consultez [Configurer les informations de sécurité pour utiliser la messagerie texte (SMS)](security-info-setup-text-msg.md).

- **Adresse e-mail.** Entrez votre adresse e-mail professionnelle ou scolaire pour recevoir un e-mail de réinitialisation de mot de passe. Cette option n’est pas disponible pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer votre e-mail, consultez [Configurer les informations de sécurité pour utiliser l’e-mail](security-info-setup-email.md).

- **Questions de sécurité.** Répondez à certaines questions de sécurité créées par votre administrateur pour votre organisation. Cette option est uniquement disponible pour la réinitialisation du mot de passe et non pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer vos questions de sécurité, consultez l’article [Configurer les informations de sécurité pour utiliser les questions de sécurité](security-info-setup-questions.md).
    
    >[!Note]
    >Si certaines de ces options ne sont pas disponibles, il est très probable que votre organisation n’autorise pas ces méthodes. Si tel est le cas, vous devez choisir une autre méthode ou contacter votre administrateur pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes

- Si vous devez mettre à jour vos informations de sécurité, suivez les instructions dans l’article [Manage your security info](security-info-manage-settings.md) (Gérer vos informations de sécurité).

- Si vous avez perdu ou oublié votre mot de passe, réinitialisez-le à partir du [portail de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/), ou suivez les étapes de l’article [Réinitialiser votre mot de passe professionnel ou scolaire](user-help-reset-password.md).

- Obtenez des conseils de dépannage et de l’aide pour les problèmes de connexion en consultant l’article [Quand vous ne pouvez pas vous connecter à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).