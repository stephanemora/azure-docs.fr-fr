---
title: Configurer les informations de sécurité pour utiliser les questions de sécurité - Azure Active Directory | Microsoft Docs
description: Configurez vos informations de sécurité pour vérifier votre identité au moyen de questions de sécurité prédéfinies.
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
ms.openlocfilehash: f003d7ad87cbcaca6ca22aab169a92403e1182e8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149507"
---
# <a name="set-up-security-info-to-use-pre-defined-security-questions-preview"></a>Configurer les informations de sécurité pour utiliser les questions de sécurité prédéfinies (préversion)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Pour configurer vos informations de sécurité, vous devez vous connecter à votre compte professionnel ou scolaire, puis suivre la procédure d’inscription. Si vous n’avez jamais configuré vos informations de sécurité, vous êtes amené à le faire maintenant.

## <a name="set-up-security-questions"></a>Régler les questions de sécurité

En fonction des paramètres de votre organisation, vous pouvez être invité à ajouter des questions de sécurité à vos informations de sécurité lorsque vous vous connectez. Sinon, la configuration des questions de sécurité dans les informations de sécurité peut s’effectuer en suivant les étapes de [Gérer vos informations de sécurité](security-info-manage-settings.md).

Si vous utilisez des questions de sécurité, nous vous recommandons de les utiliser conjointement avec une autre méthode. Les questions de sécurité peuvent s’avérer moins sécurisées que d’autres méthodes, car certaines personnes peuvent connaître les réponses aux questions d’une personne.

>[!Note]
>Les questions de sécurité sont stockées de façon privée et sécurisée dans un objet utilisateur du répertoire, elles ne peuvent être posées qu’à vous au moment de l’inscription. L’administrateur ne peut pas lire ni modifier vos questions ou vos réponses.<br>Si vous ne voyez pas l’option des questions de sécurité, il est possible que votre organisation ne vous autorise pas à l’utiliser comme moyen de vérification. Si c’est le cas, vous devez choisir une autre méthode ou contacter votre administrateur pour obtenir de l’aide.

### <a name="to-choose-and-answer-your-security-questions"></a>Pour choisir et répondre à vos questions de sécurité

1. Sélectionnez **Questions de sécurité**, puis choisissez les questions de sécurité auxquelles vous souhaitez répondre. 

    Le nombre de questions de sécurité que vous devez sélectionner est déterminé par votre administrateur.

    ![Page Informations de sécurité, sélection de vos questions de sécurité](media/security-info/security-info-keep-secure-setup-pick-questions.png)

2. Fournissez les réponses à vos questions choisies, puis sélectionnez **Terminé**.

## <a name="additional-security-info-options"></a>Options d’informations de sécurité supplémentaires

Vous avez la possibilité de choisir la façon d’être contacté par votre organisation pour la vérification de votre identité, en fonction de ce que vous essayez de faire. Ces options sont les suivantes :

- **Application d’authentification.** Téléchargez et utilisez une application d’authentification pour obtenir une notification d’approbation ou un code d’approbation généré de manière aléatoire pour la réinitialisation du mot de passe ou la vérification en deux étapes. Pour obtenir des instructions détaillées sur la configuration et l’utilisation de l’application Microsoft Authenticator, consultez [Configurer les informations de sécurité pour utiliser une application d’authentification](security-info-setup-auth-app.md).

- **SMS sur appareil mobile.** Entrez votre numéro de téléphone mobile et recevez un code par SMS, à utiliser pour la vérification en deux étapes ou la réinitialisation de mot de passe. Pour obtenir des instructions détaillées sur la vérification de votre identité par SMS, consultez [Configurer les informations de sécurité pour utiliser la messagerie texte (SMS)](security-info-setup-text-msg.md).

- **Appel sur téléphone mobile ou téléphone professionnel.** Entrez votre numéro de téléphone mobile et recevez un appel pour la réinitialisation de mot de passe ou la vérification en deux étapes. Pour des instructions pas à pas sur la façon de vérifier votre identité par téléphone, consultez [Configurer les informations de sécurité pour utiliser les appels téléphoniques](security-info-setup-phone-number.md).

- **Adresse e-mail.** Entrez votre adresse e-mail professionnelle ou scolaire pour recevoir un e-mail de réinitialisation de mot de passe. Cette option n’est pas disponible pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer votre e-mail, consultez [Configurer les informations de sécurité pour utiliser l’e-mail](security-info-setup-email.md).
   
    >[!Note]
    >Si certaines de ces options ne sont pas disponibles, votre organisation n’autorise très probablement pas ces méthodes. Si tel est le cas, vous devez choisir une autre méthode ou contacter votre administrateur pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes

- Si vous devez mettre à jour vos informations de sécurité, suivez les instructions dans l’article [Manage your security info](security-info-manage-settings.md) (Gérer vos informations de sécurité).

- Si vous avez perdu ou oublié votre mot de passe, réinitialisez-le à partir du [portail de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/), ou suivez les étapes de l’article [Réinitialiser votre mot de passe professionnel ou scolaire](user-help-reset-password.md).

- Obtenez des conseils de dépannage et de l’aide pour les problèmes de connexion en consultant l’article [Quand vous ne pouvez pas vous connecter à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
