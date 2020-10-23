---
title: Connexion sans mot de passe avec l’application Microsoft Authenticator - Azure Active Directory
description: Activer la connexion sans mot de passe pour Azure AD à l’aide de l’application Microsoft Authenticator (préversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 053a489993c31344b96e83253c88eed93b27b145
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964823"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Activer la connexion sans mot de passe avec l'application Microsoft Authenticator (préversion)

L’application Microsoft Authenticator vous permet de vous connecter à n’importe quel compte Azure AD sans utiliser de mot de passe. À l’instar de la technologie de [Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator a recours à l’authentification par clé pour activer une information d’identification utilisateur qui est liée à un appareil et utilise un code biométrique ou confidentiel. Cette méthode d’authentification peut être utilisée sur n’importe quelle plateforme d’appareil, notamment mobile, et avec une application ou un site web qui s’intègre avec les bibliothèques d’authentification Microsoft.

![Exemple de connexion dans un navigateur demandant à l’utilisateur d’approuver la connexion](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Au lieu d’obtenir une invite de mot de passe après avoir entré un nom d’utilisateur, une personne qui a activé la connexion par téléphone dans l’application Microsoft Authenticator voit s’afficher un message lui demandant d’entrer un nombre dans son application. Pour terminer le processus d’authentification dans l’application, l’utilisateur doit sélectionner le nombre correspondant, choisir **Approuver**, puis fournir son code confidentiel ou biométrique.

## <a name="prerequisites"></a>Prérequis

Pour utiliser la connexion par téléphone sans mot de passe avec l’application Microsoft Authenticator, les prérequis suivants doivent être satisfaits :

- Azure Multi-Factor Authentication, avec notifications push autorisées en tant que méthode de vérification
- Installation de la dernière version de Microsoft Authenticator sur des appareils exécutant iOS 8.0 ou une version ultérieure, ou Android 6.0 ou une version ultérieure.

> [!NOTE]
> Si vous avez activé la préversion de connexion sans mot de passe de l’application Microsoft Authenticator avec Azure AD PowerShell, elle était activée pour l’ensemble de votre annuaire. Si vous activez l’utilisation de cette nouvelle méthode, elle remplace la stratégie PowerShell. Nous vous recommandons d’activer cette fonctionnalité pour tous les utilisateurs de votre locataire par le biais du nouveau menu *Méthodes d’authentification*, sinon les utilisateurs ne figurant pas dans la nouvelle stratégie ne pourront plus se connecter sans mot de passe.

## <a name="enable-passwordless-authentication-methods"></a>Activer les méthodes d’authentification sans mot de passe

Pour utiliser l’authentification sans mot de passe dans Azure AD, activez tout d’abord l’expérience d’inscription combinée, puis activez les utilisateurs pour la méthode sans mot de passe.

### <a name="enable-the-combined-registration-experience"></a>Activer l’expérience d’inscription combinée

Les fonctionnalités d’inscription pour les méthodes d’authentification sans mot de passe s’appuient sur la fonctionnalité d'inscription combinée. Pour permettre aux utilisateurs d’effectuer eux-mêmes l’inscription combinée, suivez les étapes pour [activer l’inscription combinée des informations de sécurité](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Activer les méthodes d’authentification sans mot de passe par téléphone

Azure AD vous permet de choisir les méthodes d’authentification qui peuvent être utilisées pendant le processus de connexion. Les utilisateurs s’inscrivent ensuite pour les méthodes qu’ils souhaitent utiliser.

Pour activer la méthode d’authentification pour la connexion par téléphone sans mot de passe, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte d’*administrateur général*.
1. Recherchez et sélectionnez *Azure Active Directory*, puis accédez à **Sécurité** > **Méthodes d’authentification** > **Stratégie de méthode d’authentification (préversion)**
1. Sous **Connexion par téléphone sans mot de passe**, choisissez les options suivantes :
   1. **Activer** - Oui ou Non
   1. **Cible** - Tous les utilisateurs ou les utilisateurs sélectionnés
1. Pour appliquer la nouvelle stratégie, sélectionnez **Enregistrer**.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Inscription des utilisateurs et gestion de l’application Microsoft Authenticator

Avec la méthode d’authentification sans mot de passe disponible pour une utilisation dans Azure AD, les utilisateurs doivent désormais s’inscrire à la méthode d’authentification sans mot de passe en effectuant les étapes suivantes :

1. Accédez à [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Connectez-vous, puis ajoutez l’application Authenticator en sélectionnant **Ajouter une méthode > Application d’authentification**, puis **Ajouter**.
1. Suivez les instructions pour installer et configurer l’application Microsoft Authenticator sur votre appareil.
1. Sélectionnez **Terminé** pour terminer la configuration d’Authenticator.
1. Dans l’application **Microsoft Authenticator**, choisissez **Activer la connexion par téléphone** dans le menu déroulant du compte inscrit.
1. Suivez les instructions de l’application pour terminer de vous inscrire à la connexion sans mot de passe.

Les organisations peuvent diriger leurs utilisateurs vers [Se connecter à l’aide de votre téléphone et non de votre mot de passe](../user-help/user-help-auth-app-sign-in.md) pour obtenir une assistance supplémentaire lors de la configuration de l’application Microsoft Authenticator et de l’activation de la connexion par téléphone.

> [!NOTE]
> Les utilisateurs que la stratégie n’autorise pas à utiliser la connexion par téléphone ne sont plus en mesure de l’activer dans l’application Microsoft Authenticator.  

## <a name="sign-in-with-passwordless-credential"></a>Se connecter avec les informations d’identification sans mot de passe

La connexion sans mot de passe n’est proposée à un utilisateur qu’une fois qu’un administrateur a activé son locataire et que l’utilisateur a mis à jour son application Microsoft Authenticator pour activer la connexion par téléphone.

Pour commencer à utiliser la connexion par téléphone, après avoir tapé un nom d’utilisateur dans la page de connexion et sélectionné **Suivant**, les utilisateurs devront peut-être sélectionner **Autres méthodes pour se connecter**, puis **Approuver une demande sur mon application Microsoft Authenticator**. Un nombre leur est ensuite présenté et ils sont invités par l’application Microsoft Authenticator à sélectionner le nombre approprié afin de s’authentifier au lieu d’utiliser leur mot de passe. Une fois que les utilisateurs ont utilisé la connexion par téléphone sans mot de passe, ils sont invités à l’utiliser à nouveau, jusqu’à ce qu’ils choisissent une autre méthode.

![Exemple de connexion à un navigateur à l’aide de l’application Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Problèmes connus

Les problèmes connus suivants existent dans l’expérience en préversion actuelle.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Aucune option pour la connexion par téléphone sans mot de passe n’est visible

Si la vérification de connexion par téléphone sans mot de passe est en attente pour un utilisateur et que celui-ci tente de se connecter à nouveau, il se peut que seule une option pour entrer un mot de passe soit visible. Ouvrez Microsoft Authenticator et répondez aux invites de notification pour continuer à utiliser la connexion par téléphone sans mot de passe.

### <a name="federated-accounts"></a>Comptes fédérés

Lorsqu’un utilisateur a activé des informations d’identification sans mot de passe, les connexions Azure AD cesseront d’utiliser login_hint pour accélérer l’accès de l’utilisateur à un emplacement de connexion fédérée. Cette logique empêche les utilisateurs d’un locataire hybride d’être dirigés vers AD FS pour la vérification de la connexion si ces utilisateurs ne suivent pas une étape supplémentaire consistant à cliquer sur « Utiliser votre mot de passe à la place ».

### <a name="azure-mfa-server"></a>Serveur Azure MFA

Les utilisateurs finaux qui sont activés pour MFA par le biais d’un serveur Azure MFA local d’une organisation peuvent toujours créer et utiliser une information d’identification de connexion par téléphone sans mot de passe unique. Si l’utilisateur tente de mettre à niveau plusieurs installations (supérieures à 5) de Microsoft Authenticator avec cette information d’identification, cette modification peut générer une erreur.  

### <a name="device-registration"></a>Enregistrement de l’appareil

L’une des conditions requises pour la création de cette information d’identification forte est que l’appareil sur lequel l’application Microsoft Authenticator est installée soit inscrit dans le locataire Azure AD pour un utilisateur individuel. Du fait des restrictions en matière d’inscription d’appareil, un appareil ne peut être inscrit que dans un seul locataire. Cette limite signifie qu’un seul compte professionnel ou scolaire de l’application Microsoft Authenticator peut être activé pour la connexion par téléphone.

> [!NOTE]
> L’inscription de l’appareil n’est pas la même que la gestion des appareils ou « MDM ». Il associe uniquement un ID d’appareil et un ID d’utilisateur dans le répertoire Azure AD.  

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’authentification Azure AD et les méthodes sans mot de passe, consultez les articles suivants :

* [En savoir plus sur le fonctionnement de l’authentification par mot de passe](concept-authentication-passwordless.md)
* [En savoir plus sur l’inscription des appareils](../devices/overview.md#getting-devices-in-azure-ad)
* [En savoir plus sur Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
