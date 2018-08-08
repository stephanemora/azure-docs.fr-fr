---
title: Configurer les informations de sécurité pour utiliser la messagerie texte - Azure Active Directory | Microsoft Docs
description: Configurez vos informations de sécurité pour vérifier votre identité au moyen d’un message texte (SMS).
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
ms.openlocfilehash: f20bcb4245a2870d8eaa442c6f747ee533b362db
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347703"
---
# <a name="set-up-security-info-to-use-text-messaging-preview"></a>Configurer les informations de sécurité pour utiliser la messagerie texte (préversion)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Pour configurer vos informations de sécurité, vous devez vous connecter à votre compte professionnel ou scolaire, puis suivre le processus d’inscription. Si vous n’avez jamais configuré vos informations de sécurité, vous devrez le faire maintenant.

## <a name="set-up-text-messaging"></a>Configurer la messagerie texte

En fonction des paramètres de votre organisation, vous pouvez être invité à ajouter un message texte à vos informations de sécurité lors de la connexion. Sinon, pour commencer à configurer la messagerie texte dans les informations de sécurité, suivez les étapes indiquées dans l’article [Manage your security info](security-info-manage-settings.md) (Gérer vos informations de sécurité).

L’option de message texte fait partie des options téléphoniques, ce qui signifie que vous devez configurer tous les éléments de la même manière que pour votre numéro de téléphone. Mais dans ce cas de figure, au lieu de demander à Microsoft de vous appeler, vous devez choisir d’utiliser un message texte. Si vous ne voyez pas l’option de téléphone, il est possible que votre organisation ne vous autorise pas à utiliser un numéro de téléphone à des fins de vérification. Si c’est le cas, vous devez choisir une autre méthode ou contacter votre administrateur pour plus d’informations.

### <a name="to-use-a-text-message"></a>Pour utiliser un message texte

1. Sélectionnez l’option **Téléphone**.

    L’Assistant **Configurer votre téléphone** s’affiche.

    ![Configurer le code de votre pays ou de votre région et votre numéro de téléphone](media/security-info/security-info-keep-secure-setup-text.png)

2. Dans la liste déroulante, sélectionnez **Pays ou région**, puis saisissez votre numéro de téléphone (y compris l’indicatif, le cas échéant) dans la zone **Numéro de téléphone**. Sélectionnez ensuite l’option **M’envoyer le code par SMS**, puis sélectionnez **Suivant**.

    Vous recevrez un SMS contenant le code que vous devrez saisir dans une page de vérification.

    ![Page de vérification pour la saisie du code reçu par SMS](media/security-info/security-info-keep-secure-verify-text-msg.png)

    Vos informations de sécurité sont alors mises à jour pour vous envoyer un SMS afin de vérifier votre identité lorsque vous utilisez la vérification en deux étapes ou une réinitialisation du mot de passe en libre-service.

    >[!Note]
    >Si vous souhaitez recevoir un appel téléphonique plutôt qu’un SMS, suivez les étapes indiquées dans l’article [Set up security info to use phone calls](security-info-setup-phone-number.md) (Configurer les informations de sécurité pour utiliser des appels téléphoniques).

## <a name="additional-security-info-options"></a>Options supplémentaires disponibles pour les informations de sécurité

Vous disposez de plusieurs options pour permettre à votre organisation de vous contacter afin de vérifier votre identité, selon ce que vous souhaitez faire. Ces options sont les suivantes :

- **Application d’authentification.** Téléchargez et utilisez une application d’authentification pour obtenir une notification d’approbation ou un code d’approbation généré de manière aléatoire pour la réinitialisation du mot de passe ou la vérification en deux étapes. Pour obtenir des instructions détaillées sur la configuration et l’utilisation de l’application Microsoft Authenticator, consultez [Set up security info to use an authenticator app](security-info-setup-auth-app.md) (Configurer des informations de sécurité pour utiliser une application d’authentification).

- **Appel sur un appareil mobile ou un téléphone professionnel.** Entrez votre numéro de téléphone portable pour obtenir un appel téléphonique afin de réinitialiser votre mot de passe ou de suivre le processus de vérification en deux étapes. Pour obtenir des instructions détaillées sur la vérification de votre identité au moyen d’un numéro de téléphone, consultez [Set up security info to use phone calls](security-info-setup-phone-number.md) (Configurer les informations de sécurité pour utiliser des appels téléphoniques).

- **Adresse e-mail.** Entrez votre adresse e-mail professionnelle ou scolaire pour recevoir un e-mail de réinitialisation du mot de passe. Cette option n’est pas disponible pour la vérification en deux étapes. Pour obtenir des instructions détaillées sur la façon de configurer votre adresse de messagerie, consultez [Set up security info to use email](security-info-setup-email.md) (Configurer les informations de sécurité pour utiliser le courrier électronique).
   
    >[!Note]
    >Si certaines de ces options ne sont pas disponibles, votre organisation n’autorise très probablement pas ces méthodes. Si c’est le cas, vous devez choisir une méthode disponible ou contacter votre administrateur pour plus d’informations.

- **Questions de sécurité.** Répondez à certaines questions de sécurité créées par votre administrateur pour votre organisation. Cette option est uniquement disponible pour la réinitialisation du mot de passe et non pour la vérification en deux étapes. Pour obtenir des instructions détaillées sur la façon de configurer vos questions de sécurité, consultez l’article [Set up security info to use security questions](security-info-setup-questions.md) (Configurer les informations de sécurité pour utiliser des questions de sécurité).

## <a name="next-steps"></a>Étapes suivantes

- Si vous devez mettre à jour vos informations de sécurité, suivez les instructions dans l’article [Manage your security info](security-info-manage-settings.md) (Gérer vos informations de sécurité).

- Réinitialisez votre mot de passe si vous l’avez perdu ou oublié à partir du [portail de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/), ou suivez les étapes de l’article [Reset your work or school password](user-help-reset-password.md) (Réinitialiser le mot de passe de votre compte professionnel ou scolaire).

- Suivez les conseils de dépannage et obtenez de l’aide en cas de problème de connexion dans l’article [Quand vous ne pouvez pas vous connecter à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).