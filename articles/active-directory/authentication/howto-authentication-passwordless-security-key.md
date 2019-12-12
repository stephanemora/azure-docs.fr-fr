---
title: Connexion par clé de sécurité sans mot de passe (préversion) - Azure Active Directory
description: Activer la connexion par clé de sécurité sans mot de passe à Azure AD à l’aide de clés de sécurité FIDO2 (préversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 826b8e923575db3d6c6aee7ead230f87f1efb50e
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848440"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Activer la connexion par clé de sécurité sans mot de passe (préversion)

Pour les entreprises qui utilisent des mots de passe aujourd’hui et qui disposent d’un environnement de PC partagé, les clés de sécurité permettent aux employés de s’authentifier sans entrer de nom d’utilisateur ou de mot de passe. Les clés de sécurité améliorent la productivité des travailleurs et offrent une meilleure sécurité.

Ce document met l’accent sur l’activation de l’authentification sans mot de passe, basée sur une clé de sécurité. À la fin de cet article, vous serez en mesure de vous connecter aux applications web avec votre compte Azure AD, à l’aide d’une clé de sécurité FIDO2.

|     |
| --- |
| Les clés de sécurité FIDO2 sont une fonctionnalité d’évaluation publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Configuration requise

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Inscription d’informations de sécurité combinée – Préversion](concept-registration-mfa-sspr-combined.md)
- [Clés de sécurité FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatibles
- WebAuthN nécessite Windows 10 version 1809 ou plus**

Pour utiliser des clés de sécurité pour la connexion aux services et applications web, vous devez disposer d’un navigateur qui prend en charge le protocole WebAuthN. Il s’agit notamment de Microsoft Edge, Chrome, Firefox et Safari.

## <a name="prepare-devices-for-preview"></a>Préparer les appareils pour la préversion

Les appareils que vous utiliserez doivent exécuter Windows 10 version 1809 ou ultérieure. Vous bénéficierez de la meilleure expérience sur Windows 10 version 1903 ou ultérieure.

## <a name="enable-passwordless-authentication-method"></a>Activer les méthodes d’authentification sans mot de passe

### <a name="enable-the-combined-registration-experience"></a>Activer l’expérience d’inscription combinée

Les fonctionnalités d’inscription pour les méthodes d’authentification sans mot de passe s’appuient sur l’inscription en préversion. Suivez les étapes de l’article [Activer l’inscription d’informations de sécurité combinées (préversion)](howto-registration-mfa-sspr-combined.md)pour activer l’inscription combinée en préversion.

### <a name="enable-fido2-security-key-method"></a>Activer la méthode de clé de sécurité FIDO2

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à **Azure Active Directory** > **Sécurité** > **Méthodes d’authentification** > **Stratégie de méthode d’authentification (préversion)** .
1. Sous la méthode **Clé de sécurité FIDO2**, choisissez les options suivantes :
   1. **Activer** - Oui ou Non
   1. **Cible** - Tous les utilisateurs ou les utilisateurs sélectionnés
1. **Enregistrez** la configuration.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Inscription des utilisateurs et gestion des clés de sécurité FIDO2

1. Accédez à [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Connectez-vous si ce n’est pas déjà fait.
1. Cliquez **Informations de sécurité**.
   1. Si l’utilisateur possède dispose déjà d’au moins une méthode d’authentification multifacteur Azure inscrite, il peut inscrire immédiatement une clé de sécurité FIDO2.
   1. Sinon, il doit d’abord ajouter une méthode d'authentification multifacteur.
1. Ajoutez une clé de sécurité de FIDO2 en cliquant sur **Ajouter méthode** et en choisissant **Clé de sécurité**.
1. Choisissez **Périphérique USB** ou **Appareil NFC**.
1. Préparez votre clé et choisissez **Suivant**.
1. Une fenêtre s’affichera et demandera à l’utilisateur de créer/saisir un code confidentiel pour la clé de sécurité, puis d’effectuer le geste de requis pour la clé biométrique ou tactile.
1. L’utilisateur revient à l’expérience d’inscription combinée et est invité à fournir un nom explicite pour la clé, afin de pouvoir l’identifier s’il en possède plusieurs. Cliquez sur **Suivant**.
1. Cliquez sur **Terminé** pour terminer le processus.

## <a name="sign-in-with-passwordless-credential"></a>Se connecter avec les informations d’identification sans mot de passe

Dans l’exemple ci-dessous, un utilisateur a déjà approvisionné sa clé de sécurité FIDO2. L’utilisateur peut choisir de se connecter en ligne avec sa clé de sécurité FIDO2 depuis un navigateur pris en charge sur Windows 10 version 1809 ou ultérieure.

![Connexion par clé de sécurité dans Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Résolution des problèmes de commentaires

Si vous avez des commentaires ou si vous rencontrez des problèmes lors de l’affichage de la préversion de cette fonctionnalité, partagez vos remarques via l’application de concentrateur de commentaires Windows.

1. Lancez le **concentrateur de commentaires** et assurez-vous que vous êtes connecté.
1. Classez vos commentaires dans les catégories suivantes avant de les envoyer :
   1. Catégorie : Sécurité et confidentialité
   1. Sous-catégorie : FIDO
1. Pour capturer des journaux, utilisez l’option : **Recréer mon problème**

## <a name="known-issues"></a>Problèmes connus

### <a name="security-key-provisioning"></a>Approvisionnement de clé de sécurité

L’approvisionnement et le retrait de clés de sécurité par l’administrateur ne sont pas disponibles dans la préversion publique.

### <a name="upn-changes"></a>Modifications de l’UPN

Si l’UPN de l’utilisateur change, vous ne pouvez plus modifier les clés de sécurité FIDO2 pour en prendre en compte. La solution consiste à réinitialiser l’appareil et l’utilisateur doit réinscrire ses clés de sécurité FIDO2.

## <a name="next-steps"></a>Étapes suivantes

[Connexion par clé de sécurité FIDO2 Windows 10](howto-authentication-passwordless-security-key-windows.md)

[Activer l’authentification FIDO2 sur les ressources locales](howto-authentication-passwordless-security-key-on-premises.md)

[En savoir plus sur l’inscription des appareils](../devices/overview.md)

[En savoir plus sur Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
