---
title: Créer une application React pour ajouter des utilisateurs à un service Visage
titleSuffix: Azure Cognitive Services
description: Découvrez comment configurer votre environnement de développement et déployer une application Visage pour obtenir le consentement des clients.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: ad79bbd166e12c24339e13f38b4e10ca9ea2b549
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111966607"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>Créer une application React pour ajouter des utilisateurs à un service Visage

Ce guide vous montre comment prendre en main l’exemple d’application d’inscription Visage. L’application illustre les bonnes pratiques en matière d’obtention du consentement explicite permettant d’ajouter des utilisateurs à un service de reconnaissance faciale et d’acquérir des données de visage de haute précision. Un système intégré peut utiliser une application comme celle-ci pour proposer un contrôle d’accès sans contact, une vérification de l’identité, un suivi de l’assiduité ou une borne de personnalisation en fonction de ses données de visage.

Une fois lancée, l’application présente un écran de consentement détaillé aux utilisateurs. Si l’utilisateur donne son consentement, l’application l’invite à entrer un nom d’utilisateur et un mot de passe, puis capture une image de haute qualité de son visage à l’aide de la caméra de l’appareil.

L’exemple d’application est écrit à l’aide de JavaScript et du framework React Native. Il peut actuellement être déployé sur des appareils Android et iOS. D’autres options de déploiement seront disponibles prochainement.

## <a name="prerequisites"></a>Prérequis 

* Un abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/cognitive-services/).  
* Une fois que vous avez votre abonnement Azure, [créez une ressource Visage](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) dans le portail Azure pour obtenir votre clé et votre point de terminaison. À la fin du déploiement, sélectionnez **Accéder à la ressource**.  
  * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous avez créée pour connecter votre application à l’API Visage.  
  * Pour le développement et les tests locaux uniquement, la clé et le point de terminaison d’API sont des variables d’environnement. Pour le déploiement final, stockez la clé API en lieu sûr, mais jamais dans le code ou les variables d’environnement.  

### <a name="important-security-considerations"></a>Considérations importantes relatives à la sécurité
* Pour le développement et les premiers tests limités locaux, il est acceptable de stocker la clé et le point de terminaison d’API dans des variables d’environnement, même s’il ne s’agit pas d’une bonne pratique. Pour les déploiements pilote et final, la clé API doit être stockée de manière sécurisée, ce qui implique généralement l’utilisation d’un service intermédiaire pour la validation d’un jeton utilisateur généré à la connexion. 
* Ne stockez jamais la clé ou le point de terminaison d’API dans le code et ne les validez jamais dans un système de gestion de versions (comme Git). Si cela se produit par erreur, vous devez générer immédiatement une nouvelle clé/un nouveau point de terminaison d’API et révoquer les précédents.
* Une bonne pratique consiste à utiliser des clés API distinctes pour le développement et la production.

## <a name="set-up-the-development-environment"></a>Configuration de l’environnement de développement

#### <a name="android"></a>[Android](#tab/android)
 
1. Clonez le référentiel Git pour l’[exemple d’application](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Pour configurer votre environnement de développement, suivez la <a href="https://reactnative.dev/docs/environment-setup"  title="documentation React Native"  target="_blank">documentation React Native <span class="docon docon-navigate-external x-hidden-focus"></span></a>. Sélectionnez **React Native CLI Quickstart** (Démarrage rapide : Interface CLI de React Native). Sélectionnez votre système d’exploitation de développement et **Android** comme système d’exploitation cible. Suivez les indications des sections **Installing dependencies** (Installation des dépendances) et **Android development environment** (Environnement de développement Android).
1. Téléchargez l’éditeur de texte de votre choix, par exemple [Visual Studio Code](https://code.visualstudio.com/).
1. Récupérez le point de terminaison et la clé de votre API Visage dans le portail Azure sous l’onglet **Vue d’ensemble** de votre ressource. N’archivez pas votre clé API Visage dans votre dépôt distant.
1. Exécutez l’application à l’aide de l’émulateur d’appareil virtuel Android à partir d’Android Studio ou de votre propre appareil Android. Pour tester votre application sur un appareil physique, suivez la <a href="https://reactnative.dev/docs/running-on-device"  title="documentation React Native"  target="_blank">documentation React Native <span class="docon docon-navigate-external x-hidden-focus"></span></a> correspondante.

#### <a name="ios"></a>[iOS](#tab/ios)

1. Clonez le référentiel Git pour l’[exemple d’application](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Pour configurer votre environnement de développement, suivez la <a href="https://reactnative.dev/docs/environment-setup"  title="documentation React Native"  target="_blank">documentation React Native <span class="docon docon-navigate-external x-hidden-focus"></span></a>. Sélectionnez **React Native CLI Quickstart** (Démarrage rapide : Interface CLI de React Native). Sélectionnez **macOS** comme système d’exploitation de développement et **iOS** comme système d’exploitation cible. Suivez la section **Installing dependencies** (Installation des dépendances).
1. Téléchargez l’éditeur de texte de votre choix, par exemple [Visual Studio Code](https://code.visualstudio.com/). Vous devrez également télécharger Xcode. 
1. Récupérez le point de terminaison et la clé de votre API Visage dans le portail Azure sous l’onglet **Vue d’ensemble** de votre ressource. N’archivez pas votre clé API Visage dans votre dépôt distant.
1. Exécutez l’application à l’aide d’un appareil simulé à partir de Xcode ou de votre propre appareil iOS. Pour tester votre application sur un appareil physique, suivez la <a href="https://reactnative.dev/docs/running-on-device"  title="documentation React Native"  target="_blank">documentation React Native <span class="docon docon-navigate-external x-hidden-focus"></span></a> correspondante.

---

## <a name="create-a-user-add-experience"></a>Créer une expérience d’ajout d’utilisateur  

Maintenant que vous avez configuré l’exemple d’application, vous pouvez l’adapter à vos propres besoins.

Par exemple, vous pouvez éventuellement ajouter des informations propres à votre situation dans votre page de consentement :

> [!div class="mx-imgBorder"]
> ![page de consentement de l’application](./media/enrollment-app/1-consent-1.jpg)

Le service propose des contrôles de la qualité d’image pour vous aider à déterminer si l’image est d’une qualité suffisante pour ajouter le client ou tenter une reconnaissance faciale. Cette application montre comment accéder aux images issues de la caméra de l’appareil, sélectionner celles dont la qualité est optimale et ajouter le visage détecté dans le service API Visage. 

De nombreux problèmes de reconnaissance faciale sont dus à des images de référence de faible qualité. Voici des facteurs pouvant dégrader les performances du modèle :
* Taille du visage (visages éloignés de la caméra)
* Orientation du visage (visages tournés ou inclinés ne regardant pas la caméra)
* Conditions d’éclairage médiocres (faible éclairage ou contre-jour) qui produisent une image sous-exposée ou bruitée
* Obstruction (visages partiellement cachés ou recouverts), notamment avec des accessoires comme des chapeaux ou des lunettes à monture épaisse)
* Flou (par exemple à cause d’un mouvement de visage rapide au moment où la photo a été prise) 

> [!div class="mx-imgBorder"]
> ![Page d’instructions sur la capture d’image dans l’application](./media/enrollment-app/4-instruction.jpg)

Remarquez que l’application offre également des fonctionnalités permettant de supprimer les informations de l’utilisateur et de le rajouter.

> [!div class="mx-imgBorder"]
> ![page de gestion des profils](./media/enrollment-app/10-manage-2.jpg)

Pour étendre les fonctionnalités de l’application afin de couvrir toute l’expérience, consultez la [vue d’ensemble](enrollment-overview.md) pour connaître les autres fonctionnalités à implémenter et les bonnes pratiques.

## <a name="deploy-the-app"></a>Déployer l’application

#### <a name="android"></a>[Android](#tab/android)

Pour commencer, vérifiez que votre application est prête pour le déploiement en production : supprimez les clés ou les secrets du code de l’application et vérifiez que vous avez suivi les [bonnes pratiques en matière de sécurité](../cognitive-services-security.md?tabs=command-line%2ccsharp).

Quand vous êtes prêt à publier votre application en production, vous générez un fichier APK prêt pour la mise en production, qui correspond au format de fichier de package pour les applications Android. Ce fichier APK doit être signé avec une clé privée. Avec cette build, vous pouvez commencer à distribuer l’application directement à vos appareils. 

Pour savoir comment générer une clé privée, signer votre application et générer un fichier APK de mise en production, suivez la documentation <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title="Prepare for release"  target="_blank">Prepare for release <span class="docon docon-navigate-external x-hidden-focus"></span></a> (Préparer la mise en production).  

Une fois que vous avez créé un fichier APK signé, consultez la documentation <a href="https://developer.android.com/studio/publish"  title="Publish your app"  target="_blank">Publish your app<span class="docon docon-navigate-external x-hidden-focus"></span></a> (Publier votre application) pour en savoir plus sur la publication de votre application.

#### <a name="ios"></a>[iOS](#tab/ios)

Pour commencer, vérifiez que votre application est prête pour le déploiement en production : supprimez les clés ou les secrets du code de l’application et vérifiez que vous avez suivi les [bonnes pratiques en matière de sécurité](../cognitive-services-security.md?tabs=command-line%2ccsharp). Afin de préparer votre application pour la distribution, vous devez créer une icône d’application et un écran de lancement et configurer les paramètres de déploiement. Suivez la [documentation Xcode](https://developer.apple.com/documentation/Xcode/preparing_your_app_for_distribution) pour préparer votre application pour la distribution. 

Quand vous serez prêt à mettre votre application en production, vous générerez une archive pour votre application. Suivez la [documentation Xcode](https://developer.apple.com/documentation/Xcode/distributing_your_app_for_beta_testing_and_releases) pour savoir comment créer une build d’archive et connaître les options de distribution de votre application.  

---

## <a name="next-steps"></a>Étapes suivantes  

Dans ce guide, vous avez appris à configurer votre environnement de développement et à bien démarrer avec l’exemple d’application. Si vous ne connaissez pas bien React Native, vous pouvez lire sa [documentation de prise en main](https://reactnative.dev/docs/getting-started) pour obtenir plus d’informations générales. Il peut également s’avérer utile de vous familiariser avec l’[API Visage](Overview.md). Lisez les autres sections sur l’ajout d’utilisateurs avant de commencer le développement.