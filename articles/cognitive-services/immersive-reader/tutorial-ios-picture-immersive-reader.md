---
title: 'Tutoriel : Créer une application iOS qui prend une photo et la lance dans le lecteur immersif (Swift)'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez générer une application iOS à partir de zéro et ajouter la fonctionnalité Photo avec le lecteur immersif.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 69ff58d6cdabe49000b00afecfc6b4ad1a3f2daa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841844"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Tutoriel : Créer une application iOS qui lance le lecteur immersif avec le contenu d’une photo (Swift)

Le [lecteur immersif](https://www.onenote.com/learningtools) est un outil conçu de façon inclusive qui implémente des techniques éprouvées pour améliorer la compréhension de la lecture.

L’[API Lire Vision par ordinateur Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) détecte le contenu textuel d’une image à l’aide des derniers modèles de reconnaissance de Microsoft et convertit le texte identifié en flux de caractères lisibles par ordinateur.

Dans ce tutoriel, vous allez générer une application iOS à partir de zéro et intégrer l’API Lire ainsi que le lecteur immersif à l’aide du kit SDK Lecteur immersif. Un exemple complet fonctionnel de ce tutoriel est disponible [ici](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/picture-to-immersive-reader-swift).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Une ressource Lecteur immersif configurée pour l’authentification Azure Active Directory. Suivez [ces instructions](./how-to-create-immersive-reader.md) pour la configurer. Vous aurez besoin de certaines des valeurs créées ici lors de la configuration des propriétés de l’exemple de projet. Enregistrez la sortie de votre session dans un fichier texte pour référence ultérieure.
* L’utilisation de cet exemple nécessite un abonnement Azure au service Vision par ordinateur Cognitive Services. [Créez une ressource Vision par ordinateur Cognitive Services dans le portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Créer un projet Xcode

Créez un projet dans Xcode.

![Nouveau projet](./media/ios/xcode-create-project.png)

Choisissez **Application avec affichage unique**.

![Nouvelle application avec affichage unique](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Obtenir l’outil CocoaPod pour le SDK
Le moyen le plus simple d’utiliser le kit SDK Lecteur immersif est via CocoaPods. Pour procéder à l’installation via Cocoapods :
1. [Installez CocoaPods](http://guides.cocoapods.org/using/getting-started.html) : suivez le guide de démarrage pour installer CocoaPods.
2. Créez un Podfile en exécutant `pod init` dans le répertoire racine de votre projet XCode.
3.  Ajoutez le CocoaPod à votre Podfile en ajoutant `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`. Votre Podfile doit ressembler à ce qui suit, avec le nom de votre cible qui remplace picture-to-immersive-reader-swift :
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
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

Dans le dossier de projet principal, qui contient le fichier ViewController.swift, créez un fichier de classe Swift appelé Constants.swift. Remplacez la classe par le code suivant, en y ajoutant vos valeurs le cas échéant. Conservez ce fichier en tant que fichier local qui existe uniquement sur votre ordinateur et veillez à ne pas valider ce fichier dans le contrôle de code source, car il contient des secrets qui ne doivent pas être rendus publics. Il est recommandé de ne pas conserver de secrets dans votre application. Au lieu de cela, nous vous recommandons d’utiliser un service back-end pour obtenir le jeton, où les secrets peuvent être conservés en dehors de l’application et de l’appareil. Le point de terminaison d’API back-end doit être sécurisé avec une certaine forme d’authentification (par exemple, [OAuth](https://oauth.net/2/)) pour empêcher les utilisateurs non autorisés d’obtenir des jetons à utiliser auprès de vos services Facturation et Lecteur immersif ; ce travail dépasse le cadre de ce tutoriel.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Configurer l’application pour qu’elle s’exécute sans plan conceptuel

Ouvrez AppDelegate.swift et remplacez le fichier par le code suivant.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Ajouter des fonctionnalités pour la prise et le chargement de photos

Renommez ViewController.swift en PictureLaunchViewController.swift et remplacez le fichier par le code suivant.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Définissez le modèle d’archive dans Xcode en sélectionnant un simulateur ou une cible d’appareil.
![Modèle d’archive](./media/ios/xcode-archive-scheme.png)<br/>
![Sélectionner la cible](./media/ios/xcode-select-target.png)

Dans Xcode, appuyez sur Ctrl+R ou cliquez sur le bouton de lecture pour exécuter le projet et l’application doit être lancée sur le simulateur ou l’appareil spécifié.

Dans votre application, vous devriez voir :

![Exemple d'application](./media/ios/picture-to-immersive-reader-ipad-app.png)

Dans l’application, prenez ou chargez une photo de texte en appuyant sur le bouton Prendre une photo ou Choisir une photo dans la photothèque pour que le lecteur immersif soit lancé et affiche le texte de la photo.

![Lecteur immersif](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [Guide de référence du SDK du Lecteur immersif](./reference.md)
