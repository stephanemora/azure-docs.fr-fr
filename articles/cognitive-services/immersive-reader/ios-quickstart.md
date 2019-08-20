---
title: 'Démarrage rapide : Créer une application iOS qui lance le lecteur immersif (Swift)'
titlesuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez générer une application iOS à partir de zéro et ajouter la fonctionnalité Lecteur immersif.
services: cognitive-services
author: MeganRoach
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: t-meroa
ms.openlocfilehash: 64b3cab857a541d0bede88e7fdf21c00526b9a43
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965144"
---
# <a name="quickstart-create-an-ios-app-that-launches-the-immersive-reader-swift"></a>Démarrage rapide : Créer une application iOS qui lance le lecteur immersif (Swift)

Le [lecteur immersif](https://www.onenote.com/learningtools) est un outil conçu de façon inclusive qui implémente des techniques éprouvées pour améliorer la compréhension de la lecture.

Dans ce guide de démarrage rapide, vous allez générer une application iOS à partir de zéro et intégrer le lecteur immersif à l’aide du kit SDK Lecteur immersif. Un exemple complet fonctionnel de ce guide de démarrage rapide est disponible [ici](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/quickstart-swift).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Ressource Lecteur immersif configurée pour l’authentification Azure Active Directory (Azure AD). Suivez [ces instructions](./azure-active-directory-authentication.md) pour la configurer. Vous aurez besoin de certaines des valeurs créées ici lors de la configuration des propriétés de l’exemple de projet. Enregistrez la sortie de votre session dans un fichier texte pour référence ultérieure.

## <a name="create-an-xcode-project"></a>Créer un projet Xcode

Créez un projet dans Xcode.

![Nouveau projet](./media/ios/xcode-create-project.png)

Choisissez **Application avec affichage unique**.

![Nouvelle application avec affichage unique](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Obtenir l’outil CocoaPod pour le SDK
Le moyen le plus simple d’utiliser le kit SDK Lecteur immersif est via CocoaPods. Pour procéder à l’installation via Cocoapods :
1. [Installez CocoaPods](http://guides.cocoapods.org/using/getting-started.html) : suivez le guide de démarrage pour installer CocoaPods.
2. Créez un Podfile en exécutant `pod init` dans le répertoire racine de votre projet XCode.
3.  Ajoutez le CocoaPod à votre Podfile en ajoutant `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`. Votre Podfile doit ressembler à ce qui suit, avec le nom de votre cible qui remplace quickstart-swift :
 ```ruby
  platform :ios, '9.0'

  target 'quickstart-swift' do
  use_frameworks!
  # Pods for quickstart-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. Dans le terminal, dans le répertoire de votre projet Xcode, exécutez la commande `pod install` pour installer le pod du kit SDK Lecteur immersif.
5. Ajoutez `import immersive_reader_sdk` à tous les fichiers qui doivent référencer le kit SDK.
6. Veillez à ouvrir le projet en ouvrant le fichier `.xcworkspace`, et non le fichier `.xcodeproj`.

## <a name="acquire-an-azure-ad-authentication-token"></a>Acquérir un jeton d’authentification Azure AD

Vous avez besoin de certaines valeurs de l’étape prérequise de configuration de l’authentification Azure AD ci-dessus pour cette partie. Reportez-vous au fichier texte que vous avez enregistré pour cette session.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Dans le dossier de projet principal, qui contient le fichier ViewController.swift, créez un fichier de classe Swift appelé Constants.swift. Remplacez la classe par le code suivant, en y ajoutant vos valeurs le cas échéant. Conservez ce fichier en tant que fichier local qui existe uniquement sur votre ordinateur et veillez à ne pas valider ce fichier dans le contrôle de code source, car il contient des secrets qui ne doivent pas être rendus publics. Il est recommandé de ne pas conserver de secrets dans votre application. Au lieu de cela, nous vous recommandons d’utiliser un service back-end pour obtenir le jeton, où les secrets peuvent être conservés en dehors de l’application et de l’appareil. Le point de terminaison d’API back-end doit être sécurisé avec une certaine forme d’authentification (par exemple, [OAuth](https://oauth.net/2/)) pour empêcher les utilisateurs non autorisés d’obtenir des jetons à utiliser auprès de vos services Facturation et Lecteur immersif ; ce travail dépasse le cadre de ce guide de démarrage rapide.

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Configurer l’application pour qu’elle s’exécute sans plan conceptuel

Ouvrez AppDelegate.swift et remplacez le fichier par le code suivant.

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/AppDelegate.swift)]

## <a name="create-the-launch-view-controller-and-add-sample-content"></a>Créer le contrôleur de lancement de vue et ajouter un exemple de contenu

Renommez ViewController.swift en LaunchViewController.swift et remplacez le fichier par le code suivant.

[!code-swift[LaunchViewController](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/LaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Définissez le modèle d’archive dans Xcode en sélectionnant un simulateur ou une cible d’appareil.
![Modèle d’archive](./media/ios/xcode-archive-scheme.png)<br/>
![Sélectionner la cible](./media/ios/xcode-select-target.png)

Dans Xcode, appuyez sur Ctrl+R ou cliquez sur le bouton de lecture pour exécuter le projet et l’application doit être lancée sur le simulateur ou l’appareil spécifié.

Dans votre application, vous devriez voir :

![Exemple d'application](./media/ios/sample-app-ipad.png)

Lorsque vous cliquez sur le bouton « Lecteur immersif », vous voyez le lecteur immersif lancé avec le contenu de l’application.

![Lecteur immersif](./media/ios/immersive-reader-ipad.png)

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [kit SDK iOS Lecteur immersif](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) et les [informations de référence sur le kit SDK iOS Lecteur immersif](./ios-reference.md)
