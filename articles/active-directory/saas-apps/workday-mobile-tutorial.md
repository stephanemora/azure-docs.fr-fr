---
title: 'Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à une application mobile Workday | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et une application mobile Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 256da169761da486d8ac064a2f58a59be43bb5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754159"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Tutoriel : Intégration de l’authentification unique (SSO) Azure Active Directory à une application mobile Workday

Dans ce tutoriel, vous allez apprendre à intégrer Azure Active Directory (Azure AD), l’accès conditionnel et Intune aux applications mobiles Workday. Quand vous intégrez des applications mobiles de Workday à Microsoft, vous pouvez :

* Vous assurer que les appareils sont conformes à vos stratégies avant la connexion.
* Ajouter des contrôles à l’application de Workday afin que les utilisateurs accèdent de manière sécurisée aux données de l’entreprise. 
* Contrôler dans Azure AD qui a accès à Workday.
* Permettre à vos utilisateurs de se connecter automatiquement à Workday avec leur compte Azure AD.
* Gérer vos comptes à un emplacement central : le Portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Intégration de Workday à Azure AD
* Tutoriel : [Intégration de l’authentification unique (SSO) Azure Active Directory à Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester les stratégies d’accès conditionnel de Microsoft et Intune avec les applications mobiles de Workday.

* L’application fédérée Workday peut désormais être configurée avec Azure AD pour activer l’authentification unique. Pour plus d’informations sur la configuration, suivez [ce](workday-tutorial.md) lien.

> [!NOTE] 
> Workday ne prend pas en charge les stratégies App Protection d’Intune. Vous devez utiliser la gestion des appareils mobiles pour utiliser l’accès conditionnel.


## <a name="ensure-users-have-access-to-the-workday-mobile-app"></a>Vérifier que les utilisateurs ont accès à l’application mobile Workday

Configurez Workday pour autoriser l’accès à ses offres d’applications mobiles. Vous devrez configurer les stratégies suivantes pour l’application mobile :

Vous pouvez procéder en suivant les instructions ci-dessous :

1. Accédez au rapport Stratégies de sécurité de domaine pour la zone fonctionnelle.
2. Sélectionnez une stratégie de sécurité.
    * Utilisation mobile - Android
    * Utilisation mobile - iPad
    * Utilisation mobile - iPhone
3. Cliquez sur Modifier les autorisations.
4. Cochez la case View (Afficher) ou Modify (Modifier) pour accorder aux groupes de sécurité l’accès aux éléments sécurisables de rapport ou de tâche.
5. Cochez la case Get (Obtenir) ou Put (Placer) pour accorder aux groupes de sécurité l’accès aux actions sécurisables d’intégration et de rapport ou de tâche.

Activez les modifications de stratégie de sécurité en attente en exécutant la tâche **Activate Pending Security Policy Changes** (Activer les modifications de stratégie de sécurité en attente).

## <a name="open-workday-login-page-in-mobile-browser"></a>Ouvrir la page de connexion à Workday dans un navigateur mobile

Pour appliquer l’accès conditionnel à l’application mobile de Workday, il est nécessaire que celle-ci s’ouvre dans un navigateur externe. Pour ce faire, cochez la case **Enable Mobile Browser SSO for Native Apps** (Activer l’authentification unique du navigateur mobile pour les applications natives) dans **Edit Tenant Setup - Security** (Modifier la configuration du locataire - Sécurité). Cela nécessite l’installation d’un navigateur approuvé par Intune sur l’appareil pour iOS et dans le profil professionnel pour Android.

![Connexion du navigateur mobile](./media/workday-tutorial/mobile-browser.png)

## <a name="setup-conditional-access-policy"></a>Configurer la stratégie d’accès conditionnel

Cette stratégie n’affecte que la connexion sur un appareil iOS ou Android. Si vous souhaitez l’étendre à toutes les plateformes, il vous suffit de sélectionner **Any Device** (Tout appareil). Cette stratégie nécessite que l’appareil soit conforme à la stratégie et le vérifie par le biais de Microsoft Intune. Android ayant des profils professionnels, cela doit empêcher les utilisateurs de se connecter à Workday (web ou application) sauf s’ils opèrent via leur profil professionnel et ont installé l’application par le biais du Portail d’entreprise Intune. Il existe une étape supplémentaire pour iOS afin de garantir que la même situation s’applique. Voici quelques captures d’écran de la configuration de l’accès conditionnel.

**Workday prend en charge les contrôles d’accès suivants :**
* Exiger une authentification multifacteur
* Exiger que l’appareil soit marqué comme conforme

**L’application Workday ne prend pas en charge les contrôles suivants :**
* Demander une application cliente approuvée
* Exiger une stratégie de protection des applications (préversion)

Pour configurer **Workday** en tant qu’ **appareil géré** , effectuez les étapes suivantes :

![Configurer la stratégie d’accès conditionnel](./media/workday-tutorial/managed-devices-only.png)

1. Cliquez sur **Accueil > Microsoft Intune > Stratégies d’accès conditionnel > Appareils gérés uniquement**. 

1. Dans la page **Appareils gérés uniquement** , attribuez au champ **Nom** la valeur `Managed Devices Only`, puis cliquez sur **Applications ou actions cloud**.

1. Dans **Applications ou actions cloud** , effectuez les étapes suivantes.

    a. Définissez **Sélectionner ce à quoi cette stratégie s’applique** sur **Applications cloud**.

    b. Dans Inclure, cliquez sur **Sélectionner les applications**.

    c. Choisissez **Workday** dans la liste de sélection.

    d. Cliquez sur **Done**.

1. Activez **Activer la stratégie**.

1. Cliquez sur **Enregistrer**.

Pour l’accès **Octroyer** , effectuez les étapes suivantes :

![Workday - Configurer la stratégie d’accès conditionnel](./media/workday-tutorial/managed-devices-only-2.png)

1. Cliquez sur **Accueil > Microsoft Intune > Stratégies d’accès conditionnel > Appareils gérés uniquement**. 

1. Dans la page **Appareils gérés uniquement** , attribuez au champ **Nom** la valeur `Managed Devices Only`, puis cliquez sur **Contrôles d’accès > Octroyer**.

1. Dans la page **Octroyer** , effectuez les étapes suivantes :

    a. Pour la sélection des contrôles à appliquer, choisissez **Accorder l’accès**.

    b. Cochez la case **Exiger que l’appareil soit marqué comme conforme**.

    c. Sélectionnez **Demander un des contrôles sélectionnés**.

    d. Cliquez sur **Sélectionner**.

1. Activez **Activer la stratégie**.

1. Cliquez sur **Enregistrer**.

## <a name="set-up-device-compliance-policy"></a>Configurer la Stratégie de conformité de l’appareil

Pour que les appareils iOS ne puissent se connecter qu’à l’aide d’une application Workday gérée par MDM, vous devez bloquer l’application App Store en ajoutant **com.workday.workdayapp** à la liste des applications restreintes. Ainsi, seuls les appareils sur lesquels l’application Workday est installée via le portail d’entreprise peuvent accéder à Workday. Dans le cas d’un navigateur, ils peuvent uniquement accéder à Workday si l’appareil est géré par Intune et s’ils utilisent un navigateur géré.

![Workday - Configurer la stratégie de conformité de l’appareil](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-microsoft-intune-app-configuration-policies"></a>Configurer les stratégies de configuration d’application Microsoft Intune :

| Scénario | Paires clé/valeur |
|----------------------------------------------------------------------------------------   |-----------|
| Renseigner automatiquement les champs Locataire et Adresse web pour :<br>● Workday sur Android quand vous activez les profils Android for Work.<br>● Workday sur iPad et iPhone.     | Utilisez ces valeurs pour configurer votre locataire : <br>● Clé de configuration = UserGroupCode<br>●   Type de valeur = Chaîne <br>● Valeur de configuration = nom de votre locataire. Exemple : gms<br>Utilisez ces valeurs pour configurer votre adresse web :<br>●    Clé de configuration = AppServiceHost<br>● Type de valeur = Chaîne<br>●    Valeur de configuration = URL de base de votre locataire. Exemple : https://www.myworkday.com                              |   |
| Désactiver ces actions pour Workday sur iPad et iPhone :<br>●    Couper, copier et coller<br>●   Imprimer                       | Définissez la valeur (booléenne) sur False sur ces clés pour désactiver la fonctionnalité :<br>● AllowCutCopyPaste<br>●  AllowPrint  |
| Désactiver les captures d’écran pour Workday sur Android. |Définissez la valeur (booléenne) sur False sur la clé AllowScreenshots pour désactiver la fonctionnalité.|
| Désactiver les mises à jour suggérées pour vos utilisateurs.|Définissez la valeur (booléenne) sur False sur la clé AllowSuggestedUpdates pour désactiver la fonctionnalité.|
|Personnaliser l’URL de l’App Store pour diriger les utilisateurs mobiles vers l’App Store de votre choix.|Utilisez ces valeurs pour changer l’URL de l’App Store :<br>● Clé de configuration = AppUpdateURL<br>●   Type de valeur = Chaîne<br> ●   Valeur de configuration = URL de l’App Store|
|       |


## <a name="ios-configuration-policies"></a>Stratégies de configuration iOS

1. Accédez à https://portal.azure.com/ et connectez-vous.
2. Recherchez **Intune** ou cliquez sur le widget dans la liste.
3. Accédez à **Applications clientes-> Applications-> Stratégies de configuration des applications-> + Ajouter > Appareils gérés**.
4. Entrez un nom.
5. Sous **Plateforme** , choisissez **iOS/iPadOS**.
6. Sous **Application associée** , choisissez l’application Workday for iOS que vous avez ajoutée.
7. Cliquez sur **Paramètres de configuration** puis, sous **Format des paramètres de configuration** , choisissez **Entrer des données XML**.
8. Voici un exemple de fichier XML. Ajoutez les configurations que vous souhaitez appliquer. Remplacez **STRING_VALUE** par la chaîne que vous souhaitez utiliser. Remplacez `<true />` ou `<false />` par `<true />` ou `<false />`. Si vous n’ajoutez pas de configuration, cela équivaut à utiliser la valeur True.

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
9. Cliquez sur Ajouter.
10. Actualisez la page et cliquez sur la stratégie tout juste créée.
11. Cliquez sur Affectations et choisissez à qui vous souhaitez appliquer l’application.
12. Cliquez sur Enregistrer.

## <a name="android-configuration-policies"></a>Stratégie de configuration Android

1. Accédez à `https://portal.azure.com/` et connectez-vous.
2. Recherchez **Intune** ou cliquez sur le widget dans la liste.
3. Accédez à **Applications clientes-> Applications-> Stratégies de configuration des applications-> + Ajouter > Appareils gérés**.
5. Entrez un nom. 
6. Sous **Plateforme** , choisissez **Android**.
7. Sous **Application associée** , choisissez l’application Workday for Android que vous avez ajoutée.
8. Cliquez sur **Paramètres de configuration** puis, sous **Format des paramètres de configuration** , choisissez **Entrer des données JSON**.

