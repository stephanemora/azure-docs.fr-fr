---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 1318c47bcded47159006977db09604bb53674973
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487920"
---
[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

L’**exemple de bannière d’appel de groupe pour iOS** d’Azure Communication Services illustre de quelle façon la bibliothèque cliente Calling pour iOS de Communication Services peut être utilisée pour créer une expérience d’appels vocaux et vidéo. Dans cet exemple de démarrage rapide, vous allez apprendre à configurer et à exécuter l’exemple. Une vue d’ensemble de l’exemple est fournie ci-après pour le contexte.

## <a name="overview"></a>Vue d’ensemble

L’exemple est une application iOS native qui utilise les bibliothèques clientes iOS d’Azure Communication Services pour créer une expérience d’appels vocaux et vidéo. L’application se sert d’un composant côté serveur pour provisionner des jetons d’accès qui sont ensuite utilisés pour initialiser une bibliothèque cliente d’Azure Communication Services. Pour configurer ce composant côté serveur, n’hésitez pas à suivre le tutoriel pour [créer un service d’authentification approuvé avec Azure Functions](../../tutorials/trusted-service-tutorial.md).

Voici à quoi ressemble l’exemple :

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="Capture d’écran montrant la page d’arrivée de l’exemple d’application.":::

Lorsque vous appuyez sur le bouton « Lancer un nouvel appel », l’application iOS crée un appel et le rejoint. L’application vous permet également de rejoindre un appel Azure Communication Services en cours en spécifiant l’ID de cet appel.

Après avoir rejoint un appel, vous êtes invité à autoriser l’application à accéder à votre caméra et votre microphone. Vous êtes également invité à fournir un nom d’affichage.

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="Capture d’écran montrant l’écran de pré-appel de l’exemple d’application.":::

Une fois que vous avez configuré votre nom d’affichage et vos appareils, vous pouvez rejoindre l’appel. Vous voyez alors le canevas d’appel principal où réside l’expérience d’appel de base.

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="Capture d’écran montrant l’écran principal de l’exemple d’application.":::

Composants de l’écran principal d’appel :

- **Galerie multimédia** : scène principale montrant les participants. Si une caméra est activée pour un participant, son flux vidéo est affiché ici. Chaque participant a une vignette individuelle qui comporte son nom d’affichage et son flux vidéo (le cas échéant). La galerie prend en charge plusieurs participants et est mise à jour au fur et à mesure de l’ajout ou de la suppression de participants à l’appel.
- **Barre d’action** : elle contient les principales commandes d’appel. Ces commandes vous permettent d’activer et de désactiver votre caméra et votre microphone, de partager votre écran et de quitter l’appel.

Vous trouverez ci-dessous des informations supplémentaires sur les prérequis et les étapes à suivre pour configurer l’exemple.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Un Mac exécutant [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), ainsi qu’un certificat de développeur valide installé dans votre trousseau
- Une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer des ressources Azure Communication Services](../../quickstarts/create-communication-resource.md).
- Une fonction Azure qui exécute la [logique du service approuvé](../../tutorials/trusted-service-tutorial.md) pour récupérer les jetons d’accès.

## <a name="running-sample-locally"></a>Exécution de l’exemple localement

L’exemple d’appel de groupe peut être exécuté localement en utilisant XCode. Les développeurs ont le choix d’utiliser leur appareil physique ou un émulateur pour tester l’application.

### <a name="before-running-the-sample-for-the-first-time"></a>Avant d’exécuter l’exemple pour la première fois

1. Installez les dépendances en exécutant `pod install`.
2. Ouvrez `ACSCall.xcworkspace` dans XCode.
3. Mettez à jour `AppSettings.plist`. Définissez la valeur de la clé `acsTokenFetchUrl` sur l’URL du point de terminaison d’authentification.

### <a name="run-sample"></a>Exécuter l’exemple

Créez et exécutez l’exemple dans XCode.

## <a name="optional-securing-an-authentication-endpoint"></a>(Facultatif) Sécurisation d’un point de terminaison d’authentification

À des fins de démonstration, cet exemple utilise par défaut un point de terminaison accessible publiquement pour extraire un jeton Azure Communication Services. Dans les scénarios de production, nous vous recommandons d’utiliser votre propre point de terminaison sécurisé pour provisionner vos propres jetons.

Avec une configuration supplémentaire, cet exemple prend en charge la connexion à un point de terminaison **Azure Active Directory** (Azure AD) protégé, qui requiert une connexion de l’utilisateur pour que l’application puisse récupérer un jeton Azure Communication Services. Consultez les étapes ci-dessous :

1. Activez l’authentification Azure Active Directory dans votre application.  
   - [Inscrire votre application sous Azure Active Directory (en utilisant les paramètres de plateforme iOS/macOS)](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [Configurer votre application App Service ou Azure Functions pour utiliser une connexion Azure AD](../../../app-service/configure-authentication-provider-aad.md)
2. Accédez à la page de vue d’ensemble de l’application inscrite sous Azure Active Directory > Inscriptions des applications. Notez les valeurs de `Application (client) ID`, `Directory (tenant) ID`, `Application ID URI`

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Configuration d’Azure Active Directory dans le portail Azure.":::

3. Ouvrez `AppSettings.plist` dans Xcode, et ajoutez les valeurs de clé suivantes :
   - `acsTokenFetchUrl` : URL pour demander un jeton Azure Communication Services 
   - `isAADAuthEnabled` : valeur booléenne indiquant si l’authentification avec un jeton Azure Communication Services est requise ou non
   - `aadClientId` : ID de l’application (client)
   - `aadTenantId` : ID de l’annuaire (locataire)
   - `aadRedirectURI` : URI de redirection, qui doit être au format suivant : `msauth.<app_bundle_id>://auth`
   - `aadScopes` : tableau des étendues des autorisations requises pour autoriser les utilisateurs. Ajouter `<Application ID URI>/user_impersonation` au tableau pour accorder l’accès au point de terminaison d’authentification

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un abonnement Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- Familiarisez-vous avec la [bibliothèque de client d’appel](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Apprenez-en davantage sur le [fonctionnement des appels](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Documentation supplémentaire

- [Exemples](./../overview.md) : Recherchez d’autres exemples sur notre page Vue d’ensemble des exemples.