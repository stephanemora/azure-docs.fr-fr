---
title: Connexion sans mot de passe avec l’application Microsoft Authenticator - Azure Active Directory
description: Activer la connexion sans mot de passe pour Azure AD à l’aide de l’application Microsoft Authenticator (préversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5dbf26bc636107576af15a0217eb16302f63f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85601630"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Activer la connexion sans mot de passe avec l'application Microsoft Authenticator (préversion)

L’application Microsoft Authenticator vous permet de vous connecter à n’importe quel compte Azure AD sans utiliser de mot de passe. À l’instar de la technologie de [Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator a recours à l’authentification par clé pour activer une information d’identification utilisateur qui est liée à un appareil et utilise un code biométrique ou confidentiel. Cette méthode d’authentification peut être utilisée sur n’importe quelle plateforme d’appareil, notamment mobile, et avec une application ou un site Web qui s’intègre avec les bibliothèques d’authentification Microsoft. 

![Exemple de connexion dans un navigateur demandant à l’utilisateur d’approuver la connexion](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Au lieu d’obtenir une invite de mot de passe après avoir entré un nom d’utilisateur, une personne qui a activé la connexion par téléphone dans l’application Microsoft Authenticator voit s’afficher un message lui demandant d’entrer un nombre dans son application. Dans l’application, l’utilisateur doit alors sélectionner le nombre correspondant, choisir Approuver, puis fournir son code confidentiel ou biométrique, ce qui achève l’authentification.

> [!NOTE]
> Cette fonctionnalité est disponible dans l'application Microsoft Authenticator depuis mars 2017. Il est donc possible que lorsque la stratégie est activée pour un répertoire, les utilisateurs rencontrent ce flux immédiatement et voient un message d'erreur s'ils n'ont pas été activés par la stratégie. Gardez ce point à l’esprit et préparez vos utilisateurs à cette modification.

## <a name="prerequisites"></a>Prérequis

- Azure Multi-Factor Authentication, avec notifications push autorisées en tant que méthode de vérification 
- Installation de la dernière version de Microsoft Authenticator sur des appareils exécutant iOS 8.0 ou une version ultérieure, ou Android 6.0 ou une version ultérieure.

> [!NOTE]
> Si vous avez activé l'aperçu de connexion sans mot de passe précédent de l'application Microsoft Authenticator avec Azure AD PowerShell, il était activé pour l'ensemble de votre répertoire. Si vous activez l’utilisation de cette nouvelle méthode, elle remplace la stratégie PowerShell. Nous vous recommandons d'activer cette fonctionnalité pour tous les utilisateurs de votre client hébergé via les nouvelles méthodes d'authentification, sinon les utilisateurs ne figurant pas dans la nouvelle stratégie ne pourront plus se connecter sans mot de passe. 

## <a name="enable-passwordless-authentication-methods"></a>Activer les méthodes d’authentification sans mot de passe

### <a name="enable-the-combined-registration-experience"></a>Activer l’expérience d’inscription combinée

Les fonctionnalités d’inscription pour les méthodes d’authentification sans mot de passe s’appuient sur la fonctionnalité d'inscription combinée. Suivez les étapes de l’article [Activer l’inscription d’informations de sécurité combinées](howto-registration-mfa-sspr-combined.md)pour activer l’inscription combinée.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Activer les méthodes d’authentification sans mot de passe par téléphone

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Recherchez et sélectionnez *Azure Active Directory*. Sélectionner **Sécurité** > **Méthodes authentification** > **Stratégie des méthodes authentification (Préversion)**
1. Sous **Authentification sans mot de passe par téléphone**, choisissez les options suivantes
   1. **Activer** - Oui ou Non
   1. **Cible** - Tous les utilisateurs ou les utilisateurs sélectionnés
1. **Enregistrer** pour définir la nouvelle stratégie

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Inscription des utilisateurs et gestion de l’application Microsoft Authenticator

1. Accédez à [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Connectez-vous si ce n’est pas déjà fait
1. Ajoutez une application Authenticator en cliquant sur **Ajouter une méthode**, en choisissant **Application Authenticator**, puis en cliquant sur **Ajouter**.
1. Suivez les instructions pour installer et configurer l’application Microsoft Authenticator sur votre appareil
1. Cliquez sur **Terminé** pour terminer le processus d’installation de l’application MFA d’Authenticator. 
1. Dans **Microsoft Authenticator**, choisissez **Activer l’authentification** par téléphone dans le menu déroulant Compte.
1. Suivez les instructions de l’application pour terminer de vous inscrire à l’authentification sans mot de passe. 

Les organisations peuvent diriger leurs utilisateurs l'article [Se connecter à l'aide de votre téléphone et non de votre mot de passe](../user-help/microsoft-authenticator-app-phone-signin-faq.md) pour obtenir une assistance supplémentaire lors de la configuration dans l'application Microsoft Authenticator et de l'activation de l’authentification par téléphone. Pour appliquer ces paramètres, vous devrez peut-être vous déconnecter du locataire et vous y reconnecter. 

## <a name="sign-in-with-passwordless-credential"></a>Se connecter avec les informations d’identification sans mot de passe

Dans le cadre de la préversion publique, il n’existe aucun moyen d’imposer aux utilisateurs de créer ou d’utiliser cette nouvelle information d’identification. La connexion sans mot de passe n’est proposée à un utilisateur qu’une fois qu’un administrateur a activé son locataire **et** que l’utilisateur a mis à jour son application Microsoft Authenticator pour activer la connexion par téléphone.

Une fois que vous avez tapé votre nom d' utilisateur sur le Web et que vous avez sélectionné **Suivant**, les utilisateurs reçoivent un nombre et sont invités par l’application Microsoft Authenticator à sélectionner le nombre approprié afin de s’authentifier au lieu d’utiliser leur mot de passe. 

![Exemple de connexion à un navigateur à l’aide de l’application Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Problèmes connus

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>L’utilisateur n’est pas autorisé par la stratégie mais peut toujours s’authentifier par téléphone sans mot de passe dans Microsoft Authenticator

Il est possible qu’un utilisateur ait créé des informations d’identification de connexion par téléphone dans son application Microsoft Authenticator actuelle ou sur un appareil antérieur. Une fois que l’administrateur a activé la stratégie de méthode d’authentification pour l’authentification par téléphone sans mot de passe, tout utilisateur disposant d’une information d’identification inscrite commence à accéder à la nouvelle invite de connexion, qu’il ait ou non activé l’utilisation de la stratégie. Si l’utilisateur n’a pas été autorisé à utiliser les informations d’identification par la stratégie, il verra une erreur s’afficher après avoir terminé le processus d’authentification. 

L’administrateur peut choisir d’autoriser l’utilisateur à utiliser l’authentification par téléphone, ou l’utilisateur doit supprimer la méthode. Si l’utilisateur n’a plus l’appareil inscrit, il peut accéder à [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) et le supprimer. S’il utilise toujours Authentificator pour la MFA, ils peuvent choisir **Désactiver l’authentification** par téléphone à partir de Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>Intégration AD FS

Lorsqu’un utilisateur a activé l’information d’identification sans mot de passe Microsoft Authenticator, l’authentification de cet utilisateur implique toujours par défaut l’envoi d’une notification pour approbation. Cette logique empêche les utilisateurs d’un locataire hybride d’être dirigés vers AD FS pour la vérification de la connexion si ces utilisateurs ne suivent pas une étape supplémentaire consistant à cliquer sur « Utiliser votre mot de passe à la place ». Ce processus contourne également les stratégies d’accès conditionnel locales, ainsi que les flux d’authentification directe. 

Si la vérification d’authentification par téléphone sans mot de passe est en attente pour un utilisateur et que celui-ci tente de se connecter à nouveau, l’utilisateur peut accéder à AD FS pour saisir un mot de passe à la place.  

### <a name="azure-mfa-server"></a>Serveur Azure MFA

Les utilisateurs finaux qui sont activés pour MFA par le biais d’un serveur Azure MFA local d’une organisation peuvent toujours créer et utiliser une information d’identification de connexion par téléphone sans mot de passe. Si l’utilisateur tente de mettre à niveau plusieurs installations (supérieures à 5) de Microsoft Authenticator avec cette information d’identification, cette modification peut générer une erreur.  

### <a name="device-registration"></a>Enregistrement de l’appareil

L’une des conditions requises pour la création de cette information d’identification forte est que l’appareil sur lequel l’application Microsoft Authenticator est installée soit inscrit dans le locataire Azure AD pour un utilisateur individuel. Du fait des restrictions en matière d’inscription d’appareil, un appareil ne peut être inscrit que dans un seul locataire. Cette limite signifie qu’un seul compte professionnel ou scolaire de l’application Microsoft Authenticator peut être activé pour la connexion par téléphone.

### <a name="intune-mobile-application-management"></a>Gestion des applications mobiles Intune 

Les utilisateurs finaux soumis à une stratégie exigeant une gestion des applications mobiles (GAM) ne peuvent pas inscrire d’informations d’identification sans mot de passe dans l’application Microsoft Authenticator. 

> [!NOTE]
> L’inscription de l’appareil n’est pas la même que la gestion des appareils ou « MDM ». Il associe uniquement un ID d’appareil et un ID d’utilisateur dans le répertoire Azure AD.  

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur le fonctionnement de l’authentification par mot de passe](concept-authentication-passwordless.md)

[En savoir plus sur l’inscription des appareils](../devices/overview.md#getting-devices-in-azure-ad)

[En savoir plus sur Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
