---
title: Ajouter un compte Microsoft personnel à l’application Microsoft Authenticator – Azure AD
description: Ajoutez des comptes Microsoft personnels, tels que vos comptes Outlook.com ou Xbox LIVE, à l’application Microsoft Authenticator pour vérifier votre identité en utilisant la vérification à deux facteurs.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28fd29c1f3f393c37c9e9a8d0995766258c2eb45
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278898"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Ajouter des comptes Microsoft personnels à l’application Microsoft Authenticator

Ajoutez vos comptes Microsoft personnels, par exemple vos comptes Outlook.com et Xbox LIVE, à l’application Microsoft Authenticator pour le processus de vérification en deux étapes standard et la méthode de connexion par téléphone sans mot de passe.

- **Méthode de vérification en deux étapes standard.** Entrez votre nom d’utilisateur et votre mot de passe sur l’appareil sur lequel vous vous connectez, puis indiquez si l’application Microsoft Authenticator envoie une notification ou si vous préférez copier le code de vérification associé à partir de l’écran **Comptes** de l’application Microsoft Authenticator.

- **Méthode de connexion sans mot de passe.** Sur l’appareil sur lequel vous vous connectez, entrez votre nom d’utilisateur correspondant à votre compte Microsoft personnel, puis utilisez votre appareil mobile pour confirmer que c’est bien vous en utilisant votre empreinte digitale, votre visage ou votre code PIN. Pour cette méthode, vous n’avez pas besoin d’entrer votre mot de passe.

>[!Important]
>Avant de pouvoir ajouter votre compte, vous devez télécharger et installer l’application Microsoft Authenticator. Si ce n’est pas déjà fait, suivez la procédure décrite dans l’article [Télécharger et installer l’application](user-help-auth-app-download-install.md).

## <a name="add-your-personal-microsoft-account"></a>Ajout de votre compte Microsoft personnel

Vous pouvez ajouter votre compte Microsoft personnel en activant d’abord la vérification en deux étapes, puis en ajoutant le compte à l’application.

>[!Note]
>Si vous envisagez d’utiliser uniquement la connexion par téléphone sans mot de passe pour votre compte Microsoft personnel, vous n’avez pas à activer la vérification en deux étapes. Toutefois, pour assurer la sécurité de tout compte supplémentaire, nous vous recommandons de l’activer.

### <a name="turn-on-two-factor-verification"></a>Activation de la vérification en deux étapes

1. Sur votre ordinateur, accédez à la page [Security basics](https://account.microsoft.com/security) (Fonctions de base de la sécurité), puis connectez-vous à l'aide de votre compte Microsoft personnel. Par exemple : alain@outlook.com.

2. En bas de la page **Security basics** (Fonctions de base de la sécurité), cliquez sur le lien **more security options** (Autres options de sécurité).

    ![Page Security basics (Fonctions de base de la sécurité) dont le lien « more security options » (Autres options de sécurité) est mis en surbrillance](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Accédez à la section **Vérification en deux étapes**, puis **activez** la fonctionnalité. Vous pouvez également la désactiver ici si vous ne souhaitez plus l’utiliser avec votre compte personnel.

### <a name="add-your-microsoft-account-to-the-app"></a>Ajout de votre compte Microsoft à l’application

1. Ouvrez l’application Microsoft Authenticator sur votre appareil mobile.

2. Sélectionnez l’option **Ajouter un compte** à partir de l’icône **Customize and control** (Personnaliser et contrôler) située dans le coin supérieur droit.

    ![Page de comptes dont l’icône Customize and control (Personnaliser et contrôler) est mise en surbrillance](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. Dans la page **Ajouter un compte**, sélectionnez **Compte personnel**.

4. Connectez-vous à votre compte personnel à l’aide de l’adresse e-mail appropriée (par exemple alain@outlook.com), puis sélectionnez **Suivant**.

    >[!Note]
    >Si vous ne disposez pas d’un compte Microsoft personnel, vous pouvez en créer un ici.

5. Entrez votre mot de passe, puis sélectionnez **Se connecter**.

    Votre compte personnel est ajouté à l’application Microsoft Authenticator.

## <a name="next-steps"></a>Étapes suivantes

- Après avoir ajouté vos comptes à l’application, vous pouvez vous connecter à l’aide de l’application Authenticator sur votre appareil. Pour plus d’informations, consultez l’article [Se connecter à l’aide de l’application Microsoft Authenticator](user-help-auth-app-sign-in.md).

- Si vous ne parvenez pas à obtenir le code de vérification de votre compte Microsoft personnel, voir la section **Résolution des problèmes de code de vérification** de l’article [Informations de sécurité et codes de vérification des comptes Microsoft](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes).

- Pour les appareils exécutant iOS, vous pouvez également sauvegarder les informations d’identification de votre compte et les paramètres de l’application associée, comme l’ordre de vos comptes, dans le cloud. Pour plus d’informations, consultez l’article [Sauvegarder et récupérer des informations d’authentification de compte avec l’application Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
