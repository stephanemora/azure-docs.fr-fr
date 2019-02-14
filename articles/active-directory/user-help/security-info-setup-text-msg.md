---
title: Configurer les informations de sécurité pour utiliser la messagerie texte - Azure Active Directory | Microsoft Docs
description: Configurez vos informations de sécurité pour vérifier votre identité au moyen d’un message texte (SMS).
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9556c99e7e9655054ee5b834db36ba6e22e9e807
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182646"
---
# <a name="set-up-security-info-to-use-text-messaging-preview"></a>Configurer les informations de sécurité pour utiliser la messagerie texte (préversion)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Pour configurer vos informations de sécurité, vous devez vous connecter à votre compte professionnel ou scolaire, puis suivre le processus d’inscription. Si vous n’avez jamais configuré vos informations de sécurité, vous êtes amené à le faire maintenant.

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

Vous avez la possibilité de choisir la façon d’être contacté par votre organisation pour la vérification de votre identité, en fonction de ce que vous essayez de faire. Ces options sont les suivantes :

- **Application d’authentification.** Téléchargez et utilisez une application d’authentification pour obtenir une notification d’approbation ou un code d’approbation généré de manière aléatoire pour la réinitialisation du mot de passe ou la vérification en deux étapes. Pour obtenir des instructions détaillées sur la configuration et l’utilisation de l’application Microsoft Authenticator, consultez [Set up security info to use an authenticator app](security-info-setup-auth-app.md) (Configurer des informations de sécurité pour utiliser une application d’authentification).

- **Appel sur un appareil mobile ou un téléphone professionnel.** Entrez votre numéro de téléphone mobile et recevez un appel pour la réinitialisation de mot de passe ou la vérification en deux étapes. Pour des instructions pas à pas sur la façon de vérifier votre identité par téléphone, consultez [Configurer les informations de sécurité pour utiliser les appels téléphoniques](security-info-setup-phone-number.md).

- **Adresse e-mail.** Entrez votre adresse e-mail professionnelle ou scolaire pour recevoir un e-mail de réinitialisation de mot de passe. Cette option n’est pas disponible pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer votre e-mail, consultez [Configurer les informations de sécurité pour utiliser l’e-mail](security-info-setup-email.md).
   
    >[!Note]
    >Si certaines de ces options ne sont pas disponibles, votre organisation n’autorise très probablement pas ces méthodes. Si c’est le cas, vous devez choisir une méthode disponible ou contacter votre administrateur pour plus d’informations.

- **Questions de sécurité.** Répondez à certaines questions de sécurité créées par votre administrateur pour votre organisation. Cette option est uniquement disponible pour la réinitialisation du mot de passe et non pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer vos questions de sécurité, consultez l’article [Configurer les informations de sécurité pour utiliser les questions de sécurité](security-info-setup-questions.md).

## <a name="next-steps"></a>Étapes suivantes

- Si vous devez mettre à jour vos informations de sécurité, suivez les instructions dans l’article [Manage your security info](security-info-manage-settings.md) (Gérer vos informations de sécurité).

- Si vous avez perdu ou oublié votre mot de passe, réinitialisez-le à partir du [portail de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/), ou suivez les étapes de l’article [Réinitialiser votre mot de passe professionnel ou scolaire](user-help-reset-password.md).

- Obtenez des conseils de dépannage et de l’aide pour les problèmes de connexion en consultant l’article [Quand vous ne pouvez pas vous connecter à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
