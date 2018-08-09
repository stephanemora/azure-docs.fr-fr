---
title: Activation de l’authentification unique entre applications sur Android à l’aide de la bibliothèque ADAL | Microsoft Docs
description: 'En savoir plus sur l’utilisation des fonctionnalités de votre Kit de développement logiciel (SDK) ADAL pour activer l’authentification unique sur l’ensemble de vos applications. '
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 06/13/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 4abf6bd2d82753e22d4fde92e219109274ce36be
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39580215"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Activation de l’authentification unique entre applications sur Android à l’aide de la bibliothèque ADAL
Fournir l’authentification unique (SSO) afin que les utilisateurs n’aient à entrer qu’une seule fois leurs informations d’identification, lesquelles restent automatiquement actives sur l’ensemble des applications, est désormais une pratique standard du secteur. La difficulté à entrer le nom d’utilisateur et le mot de passe sur des petits formats d’écrans, à laquelle s’ajoute souvent un facteur supplémentaire (2FA) comme un appel téléphonique ou un code par SMS, mécontente les utilisateurs contraints de se connecter plusieurs fois.

En outre, si vous appliquez une plateforme d’identité que d’autres applications peuvent utiliser, comme les comptes Microsoft ou un compte professionnel de Microsoft 365, les clients s’attendent à ce que ces informations d’identification soient disponibles sur l’ensemble de leurs applications, quel que soit le fournisseur.

La plateforme Microsoft Identity, combinée aux SDK Microsoft Identity, vous permet d’offrir à vos utilisateurs l’authentification unique (SSO) au sein de votre propre suite d’applications ou, comme avec les applications de répartiteur et Authenticator, sur l’intégralité de l’appareil.

Cette procédure pas à pas vous indique comment configurer le SDK dans votre application afin de fournir l’authentification unique (SSO) à vos clients.

Le document ci-dessus suppose que vous savez comment intégrer votre application au kit [Microsoft Identity Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Concepts de l’authentification unique dans la plateforme Microsoft Identity
### <a name="microsoft-identity-brokers"></a>Répartiteurs Microsoft Identity
Sur l’ensemble des plateformes mobiles, Microsoft fournit des applications qui prennent en charge le portage des informations d’identification entre les applications de différents fournisseurs, ainsi que des fonctionnalités spéciales avancées nécessitant un emplacement unique sécurisé de validation des informations d’identification. Le SDK appelle ces **répartiteurs**. Sur iOS et Android, les répartiteurs sont fournis par le biais d’applications téléchargeables que les clients installent indépendamment ou peuvent faire l’objet d’un push vers l’appareil par une entreprise qui gère certains ou la totalité des appareils de ses employés. Ces répartiteurs gèrent la sécurité d’une partie des applications ou de l’intégralité de l’appareil, en fonction des besoins des administrateurs informatiques. Dans Windows, cette fonctionnalité est fournie par un sélecteur de compte intégré au système d’exploitation, désigné techniquement sous l’appellation « Répartiteur d’authentification web ».

#### <a name="broker-assisted-logins"></a>Connexions assistées avec répartiteur
Les connexions assistées avec répartiteur sont des expériences de connexion se produisant au sein de l’application de répartiteur, qui utilisent le stockage et la sécurité de ce composant pour partager l’ensemble des applications sur l’appareil qui applique la plateforme Microsoft Identity. Il est sous-entendu que vos applications s’appuient sur le répartiteur pour connecter les utilisateurs. Sur iOS et Android, ces répartiteurs sont fournis par le biais d’applications téléchargeables que les clients installent indépendamment ou peuvent faire l’objet d’un push vers l’appareil par une entreprise qui gère l’appareil de ses employés. Comme exemple de ce type d’application, citons Microsoft Authenticator sur iOS. Dans Windows, cette fonctionnalité est fournie par un sélecteur de compte intégré au système d’exploitation, désigné techniquement sous l’appellation « Répartiteur d’authentification web ».
L’expérience, qui varie en fonction des plateformes, peut parfois perturber les utilisateurs en cas de gestion inappropriée. Vous connaissez probablement davantage ce modèle si vous avez installé l’application Facebook et que vous utilisez Facebook Connect depuis une autre application. La plateforme Microsoft Identity utilise le même modèle.

Sur Android, le sélecteur de compte s’affiche dans la partie supérieure de votre application ; l’utilisateur est ainsi moins perturbé.

#### <a name="how-the-broker-gets-invoked"></a>Appel du répartiteur
Si un répartiteur compatible, tel que l’application Microsoft Authenticator, est installé sur l’appareil, les Kits de développement logiciel (SDK) Microsoft Identity effectuent automatiquement pour vous l’opération d’appel du répartiteur lorsqu’un utilisateur souhaite se connecter à l’aide d’un compte de la plateforme Microsoft Identity. 
 
 #### <a name="how-microsoft-ensures-the-application-is-valid"></a>Garantie de la validité de l’application par Microsoft
 
 Pour sécuriser les connexions assistées avec répartiteur, il est essentiel de s’assurer de l’identité de l’application appelant le répartiteur. iOS et Android ne fournissent pas d’identificateurs uniques valides uniquement pour une application donnée. Ainsi, des applications malveillantes peuvent « usurper » l’identité d’une application légitime en utilisant son identificateur et recevoir les jetons destinés à l’application légitime. Pour garantir que Microsoft communique toujours avec la bonne application au moment de l’exécution, le développeur est invité à fournir un URI de redirection (redirectURI) personnalisé quand il inscrit son application auprès de Microsoft. **Nous expliquons ci-dessous comment les développeurs doivent créer cet URI de redirection.** Cet URI de redirection personnalisé contient l’empreinte de certificat de l’application et est garanti comme étant propre à l’application par Google Play Store. Lorsqu’une application appelle le répartiteur, celui-ci demande au système d’exploitation Android de lui fournir l’empreinte du certificat qui a appelé le répartiteur. Le répartiteur fournit cette empreinte de certificat à Microsoft dans le cadre de l’appel au système d’identité. Si l’empreinte de certificat de l’application ne correspond pas à l’empreinte de certificat fournie par le développeur au moment de l’inscription, l’accès aux jetons de la ressource demandés par l’application est refusé. Cette vérification permet de s’assurer que seule l’application inscrite par le développeur reçoit les jetons.

Les connexions réparties avec authentification unique présentent les avantages suivants :

* L’utilisateur profite de l’authentification unique sur l’ensemble de ses applications, quel que soit le fournisseur.
* Votre application peut utiliser des fonctionnalités commerciales plus avancées, comme l’accès conditionnel, et prendre en charge les scénarios Intune.
* Votre application peut prendre en charge l’authentification par certificat des utilisateurs professionnels.
* L’expérience de connexion est plus sécurisée, dans la mesure où les identités de l’application et de l’utilisateur sont vérifiées par l’application du répartiteur à l’aide d’algorithmes de sécurité et d’un chiffrement supplémentaires.

Voici une représentation de la manière dont les Kits de développement logiciel (SDK) Microsoft Identity fonctionnent avec les applications de répartiteur pour prendre en charge l’authentification unique :

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

En vous appuyant sur ces informations de base, vous devriez être en mesure de mieux comprendre et d’implémenter l’authentification unique au sein de votre application à l’aide de la plateforme et des Kits de développement logiciel (SDK) Microsoft Identity.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Activation de l’authentification unique assistée avec répartiteur
La capacité d’une application à utiliser un répartiteur installé sur l’appareil est **désactivée par défaut**. Pour utiliser votre application avec le répartiteur, vous devez effectuer une configuration supplémentaire et ajouter du code à votre application.

Voici la procédure à suivre :

1. Activer le mode de répartiteur dans votre appel du code de votre application vers le SDK MS
2. Établir un nouvel URI de redirection à fournir à l’application et à votre inscription d’application
3. Configuration des autorisations appropriées dans le manifeste Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Étape 1 : Activer le mode répartiteur dans votre application
La capacité de votre application à utiliser le répartiteur est activée lorsque vous créez les paramètres, ou la configuration initiale, de votre instance d’authentification. Pour ce faire dans votre application :

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Étape 2 : Établissez un nouvel URI de redirection avec votre schéma d’URL
Pour garantir que l’application appropriée reçoit les jetons d’identification retournés, il est nécessaire de respecter la procédure de rappel de votre application pouvant être vérifiée par votre système d’exploitation Android. Le système d’exploitation Android utilise le hachage du certificat dans Google Play Store. Ce hachage du certificat ne peut pas être falsifié par une application non fiable. Avec l’URI de l’application de répartiteur, Microsoft vérifie que les jetons sont retournés à l’application appropriée. Une URI de redirection unique est nécessaire pour être inscrit sur l’application.

Votre URI de redirection doit présenter la forme appropriée suivante :

`msauth://packagename/Base64UrlencodedSignature`

ex. : *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Vous pouvez inscrire cet URI de direction dans l’inscription de votre application avec le [portail Azure](https://portal.azure.com/). Pour plus d’informations sur l’inscription d’applications Azure AD, consultez [Intégration avec Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Étape 3: Configurer les autorisations appropriées dans votre application
Dans Android, l’application de répartiteur utilise la fonctionnalité de Gestionnaire de comptes du système d’exploitation Android pour gérer les informations d’identification entre les applications. Pour pouvoir utiliser le répartiteur dans Android, le manifeste de votre application doit être autorisé à utiliser les comptes AccountManager. Ces autorisations sont décrites en détail dans la [documentation Google pour AccountManager disponible ici](http://developer.android.com/reference/android/accounts/AccountManager.html)

Ces autorisations sont les suivantes :

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Vous avez configuré l’authentification unique !
Désormais, le Kit de développement logiciel (SDK) Microsoft Identity partage automatiquement les informations d’identification entre vos applications et appelle l’éventuel répartiteur existant sur l’appareil.

