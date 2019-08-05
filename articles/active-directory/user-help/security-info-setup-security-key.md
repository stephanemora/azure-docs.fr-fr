---
title: Configurer des informations de sécurité pour utiliser une clé de sécurité (préversion) - Azure Active Directory | Microsoft Docs
description: Comment configurer vos informations de sécurité pour vérifier votre identité à l’aide d’une clé de sécurité Fast Identity Online (FIDO2).
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d2f06b054e433c0320019548c56539d102beaad
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68386726"
---
# <a name="set-up-security-info-to-use-a-security-key-preview"></a>Configurer des informations de sécurité pour utiliser une clé de sécurité (préversion)

Vous pouvez utiliser des clés de sécurité comme méthode de connexion avec mot de passe dans votre organisation. Une clé de sécurité est un appareil physique qui est utilisé avec un code confidentiel unique pour se connecter à votre compte professionnel ou scolaire. Étant donné que les clés de sécurité nécessitent que vous disposiez de l’appareil physique et d’un nom que vous seul connaissez, il s’agit d’une méthode d’authentification plus puissante qu’un nom d’utilisateur et un mot de passe.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Important]
>Ce contenu est destiné aux utilisateurs. Si vous êtes un administrateur, vous trouverez plus d’informations sur la méthode à suivre pour configurer et gérer votre environnement Azure Active Directory (Azure AD) dans le [documentation relative à Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

## <a name="what-is-a-security-key"></a>Qu’est-ce qu’une clé de sécurité ?

Nous prenons actuellement en charge plusieurs conceptions et fournisseurs de clés de sécurité utilisant la méthode d’authentification par mot de passe [Fast Identity Online (Rex) (FIDO2)](https://fidoalliance.org/fido2/). Cette méthode vous permet de vous connecter une seule fois à votre compte professionnel ou scolaire pour accéder à toutes les ressources cloud de votre organisation et aux navigateurs pris en charge.

Votre administrateur ou votre organisation vous fournit une clé de sécurité si elle en a besoin pour votre compte professionnel ou scolaire. Il existe différents types de clés de sécurité que vous pouvez utiliser, par exemple une clé USB que vous connectez à votre appareil ou une clé NFC que vous connectez à un lecteur NFC. Vous pouvez obtenir plus d’informations sur votre clé de sécurité, notamment son type, dans la documentation du fabricant.

> [!Note]
> Si vous ne parvenez pas à utiliser une clé de sécurité FIDO2, vous pouvez utiliser d’autres méthodes d’authentification par mot de passe, telles que l’application Microsoft Authenticator ou Windows Hello. Pour plus d’informations sur l’application Microsoft Authenticator, consultez [Qu’est-ce que l’application Microsoft Authenticator ?](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) Pour plus d’informations sur Windows Hello, consultez [Vue d’ensemble de Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer à inscrire votre clé de sécurité, vérifiez les éléments suivants :

- Votre administrateur a activé cette fonctionnalité pour une utilisation au sein de votre organisation.

- Vous êtes sur un appareil exécutant au moins Windows 10 version 1903 et utilisant le navigateur Microsoft Edge.

- Vous avez reçu une clé de sécurité physique de votre administrateur ou de votre organisation. Votre clé de sécurité doit être à la fois conforme à FIDO2 et à Microsoft. Si vous avez des questions sur votre clé de sécurité et pour vérifier si elle est compatible, contactez le support technique de votre organisation.

## <a name="register-your-security-key"></a>Inscrire votre clé de sécurité

Vous devez préparer votre clé de sécurité pour qu’elle fonctionne avec Windows et un code confidentiel unique pour pouvoir vous connecter à votre compte professionnel ou scolaire à l’aide de la clé.

1. Accédez à la page **Mon profil** à l’adresse https://myprofile.microsoft.com et connectez-vous si vous ne l’avez pas déjà fait.

2. Sélectionnez **Informations de sécurité**, **Ajouter une méthode**, puis **Clé de sécurité** dans la liste déroulante **Ajouter une méthode**.

    ![Zone Ajouter une méthode, avec la clé de sécurité sélectionnée](media/security-info/security-info-security-key-add-method.png)

3. Sélectionnez **Ajouter**, puis le type de clé de sécurité dont vous disposez : **appareil USB** ou **appareil NFC**.

    ![Indiquer si vous disposez d’un type de clé de sécurité USB ou NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Si vous ne savez pas de quel type de clé de sécurité vous disposez, reportez-vous à la documentation du fabricant. Si vous n’êtes pas sûr du fabricant, contactez le support technique de votre organisation pour obtenir de l’aide.

4. Mettez votre clé de sécurité dans un emplacement facilement accessible, puis dans la zone **Clé de sécurité**, sélectionnez **Suivant**.

    ![Zone Démarrer l’inscription de la clé de sécurité](media/security-info/security-info-security-key-start-setup.png)

    Une nouvelle zone s’affiche pour vous guider dans la configuration de votre nouvelle méthode de connexion.

5. Dans la zone **Configuration de votre nouveau mode de connexion**, sélectionnez **Suivant**, puis :

    - Si votre clé de sécurité est un appareil USB, insérez-la dans le port USB de votre appareil.

    - Si votre clé de sécurité est un appareil NFC, connectez-la à votre lecteur.

6. Entrez le code confidentiel unique de votre clé de sécurité dans la zone **Sécurité Windows**, puis sélectionnez **OK**.

    Vous revenez alors à la zone **Configuration de votre nouveau mode de connexion**.

7. Sélectionnez **Suivant**.

8. Revenez à la page **Informations de sécurité**, tapez un nom que vous reconnaîtrez plus tard pour votre nouvelle clé de sécurité, puis sélectionnez **Suivant**.

    ![Page Informations de sécurité, nommer votre clé de sécurité](media/security-info/security-info-security-key-name.png)

    Votre clé de sécurité est inscrite et vous pouvez l’utiliser pour vous connecter à votre compte professionnel ou scolaire.

9. Sélectionnez **Terminé** pour fermer la zone **Clé de sécurité**.

    La page **Informations de sécurité** est mise à jour avec les informations de votre clé de sécurité.

    ![Page Informations de sécurité, avec toutes les méthodes inscrites affichées](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Supprimer une clé de sécurité de vos informations de sécurité

Si vous ne souhaitez plus utiliser votre clé de sécurité, vous pouvez la supprimer de vos informations de sécurité. Bien que cela empêche l’utilisation de la clé de sécurité avec votre compte professionnel ou scolaire, la clé de sécurité continue de stocker vos données et informations d’identification. Pour supprimer vos données et vos informations d’identification de la clé de sécurité elle-même, vous devez suivre les instructions de la section [Réinitialiser une clé de sécurité compatible Microsoft](#reset-your-security-key) dans cet article.

1. Sélectionnez le lien **Supprimer** de la clé de sécurité à supprimer.

2. Sélectionnez **OK** dans la zone **Supprimer la clé de sécurité**.

    Votre clé de sécurité est supprimée et vous ne pouvez plus l’utiliser pour vous connecter à votre compte professionnel ou scolaire.

>[!Important]
>Si vous avez supprimé cette clé de sécurité par erreur, vous devez l’inscrire à nouveau en suivant les instructions de la section [Inscrire votre clé de sécurité](#register-your-security-key) dans cet article.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Gérer vos paramètres de clé de sécurité à partir des paramètres Windows

Vous pouvez gérer vos paramètres de clé de sécurité à partir de l’application **Paramètres Windows**, notamment la réinitialisation de votre clé de sécurité et la création d’un code confidentiel pour celle-ci.

### <a name="reset-your-security-key"></a>Réinitialiser votre clé de sécurité

Si vous souhaitez supprimer toutes les informations de compte stockées sur votre clé de sécurité physique, vous devez rétablir les paramètres d’usine de la clé. La réinitialisation de votre clé de sécurité supprime tous les éléments de la clé, ce qui vous permet de recommencer.

>[!IMPORTANT]
>La réinitialisation de votre clé de sécurité entraîne la suppression de tous les éléments de la clé et sa réinitialisation aux paramètres d’usine.
>
> **Toutes les données et toutes les informations d’identification sont effacées.**

#### <a name="to-reset-your-security-key"></a>Pour réinitialiser votre clé de sécurité

1. Ouvrez l’application Paramètres Windows, sélectionnez **Comptes**, **Options de connexion**, **Clé de sécurité**, puis **Gérer**.

2. Insérez votre clé de sécurité dans le port USB ou connectez votre lecteur NFC pour vérifier votre identité.

3. Suivez les instructions à l’écran en fonction du fabricant de votre clé de sécurité. Si le fabricant de votre clé n’est pas listé dans les instructions à l’écran, reportez-vous au site du fabricant pour plus d’informations.

4. Sélectionnez **Fermer** pour fermer l’écran **Gérer**.

### <a name="create-a-new-security-key-pin"></a>Créer un nouveau code confidentiel pour une clé de sécurité

Vous pouvez créer un nouveau code confidentiel pour votre clé de sécurité.

#### <a name="to-create-a-new-security-key-pin"></a>Pour créer un nouveau code confidentiel pour une clé de sécurité

1. Ouvrez l’application Paramètres Windows, sélectionnez **Comptes**, **Options de connexion**, **Clé de sécurité**, puis **Gérer**.

2. Insérez votre clé de sécurité dans le port USB ou connectez votre lecteur NFC pour vérifier votre identité.5
3. Sélectionnez **Ajouter** dans la zone **Security Key PIN (Code confidentiel de la clé de sécurité)** , tapez et confirmez le nouveau code confidentiel de la clé de sécurité, puis sélectionnez **OK**.

    La clé de sécurité est mise à jour avec le nouveau code confidentiel de clé de sécurité à utiliser avec votre compte professionnel ou scolaire. Si vous décidez à nouveau de modifier votre code confidentiel, vous pouvez sélectionner le bouton **Modifier**.6
4. Sélectionnez **Fermer** pour fermer l’écran **Gérer**.

## <a name="additional-security-info-methods"></a>Autres méthodes d’informations de sécurité

Vous avez la possibilité de choisir la façon d’être contacté par votre organisation pour la vérification de votre identité, en fonction de ce que vous essayez de faire. Ces options sont les suivantes :

- **Application d’authentification.** Téléchargez et utilisez une application d’authentification pour obtenir une notification d’approbation ou un code d’approbation généré de manière aléatoire pour la réinitialisation du mot de passe ou la vérification en deux étapes. Pour obtenir des instructions détaillées sur la configuration et l’utilisation de l’application Microsoft Authenticator, consultez [Configurer les informations de sécurité pour utiliser une application d’authentification](security-info-setup-auth-app.md).

- **SMS sur appareil mobile.** Entrez votre numéro de téléphone mobile et recevez un code par SMS, à utiliser pour la vérification en deux étapes ou la réinitialisation de mot de passe. Pour obtenir des instructions détaillées sur la vérification de votre identité par SMS, consultez [Configurer les informations de sécurité pour utiliser la messagerie texte (SMS)](security-info-setup-text-msg.md).

- **Appel sur téléphone mobile ou téléphone professionnel.** Entrez votre numéro de téléphone mobile et recevez un appel pour la réinitialisation de mot de passe ou la vérification en deux étapes. Pour des instructions pas à pas sur la façon de vérifier votre identité par téléphone, consultez [Configurer les informations de sécurité pour utiliser les appels téléphoniques](security-info-setup-phone-number.md).

- **Adresse e-mail.** Entrez votre adresse e-mail professionnelle ou scolaire pour recevoir un e-mail de réinitialisation de mot de passe. Cette option n’est pas disponible pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer votre e-mail, consultez [Configurer les informations de sécurité pour utiliser l’e-mail](security-info-setup-email.md).

- **Questions de sécurité.** Répondez à certaines questions de sécurité créées par votre administrateur pour votre organisation. Cette option est uniquement disponible pour la réinitialisation du mot de passe et non pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer vos questions de sécurité, consultez l’article [Configurer les informations de sécurité pour utiliser les questions de sécurité](security-info-setup-questions.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les méthodes d’authentification par mot de passe, consultez le billet de blog [Microsoft’s Azure AD begins public preview of FIDO2 security keys, enabling passwordless logins (Azure AD de Microsoft commence la préversion publique des clés de sécurité FIDO2, en activant les connexions sans mot de passe)](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) ou lisez les articles [Présentation de l’application Microsoft Authenticator ?](https://docs.microsoft.com/azure/active-directory/user-help8user-help-auth-app-overview) et [Vue d’ensemble de Windows Hello](https://www.microsoft.com/windows/windows-hello).

- Plus d’informations sur les [clés de sécurité conformes Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Si vous avez perdu ou oublié votre mot de passe, réinitialisez-le à partir du [portail de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/), ou suivez les étapes de l’article [Réinitialiser votre mot de passe professionnel ou scolaire](user-help-reset-password.md).

- Obtenez des conseils de dépannage et de l’aide pour les problèmes de connexion en consultant l’article [Quand vous ne pouvez pas vous connecter à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
