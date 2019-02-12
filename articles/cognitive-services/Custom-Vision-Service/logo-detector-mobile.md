---
title: 'Didacticiel : Utiliser le détecteur de logo personnalisé pour reconnaître les services Azure - Vision personnalisée'
titlesuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez examiner un exemple d’application qui utilise le service Vision personnalisée d’Azure dans le cadre d’un scénario de détection de logo. Découvrez comment le service Vision personnalisée est utilisé avec d’autres composants pour fournir une application de bout en bout.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pafarley
ms.openlocfilehash: 520e1981544f6ad0a6f1412a47dc8ea50fdd53a5
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771433"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Tutoriel : Reconnaître les logos des services Azure dans les images de l’appareil photo

Dans ce tutoriel, vous allez examiner un exemple d’application qui utilise le service Vision personnalisée d’Azure dans le cadre d’un scénario plus vaste. L’application AI Visual Provision, une application Xamarin.Forms pour plateformes mobiles, analyse les images d’appareil photo des logos des services Azure, puis déploie les services eux-mêmes sur le compte Azure de l’utilisateur. Ici, vous verrez comment elle utilise le service Vision personnalisée en coordination avec d’autres composants pour fournir une application de bout en bout utile. Vous pouvez envisager d’exécuter l’application entière pour vous-même ou vous pouvez simplement effectuer la partie Vision personnalisée de la configuration et explorer la manière dont l’application l’utilise.

Ce didacticiel vous explique comment :

> [!div class="checklist"]
> - Créer un détecteur d’objet personnalisé afin de reconnaître les logos des services Azure
> - Connecter votre application aux services Azure Vision par ordinateur et Vision par ordinateur
> - Créer un compte de principal de service Azure pour déployer des services Azure à partir de l’application

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. 

## <a name="prerequisites"></a>Prérequis

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Une charge de travail Xamarin pour Visual Studio (voir [Installation de Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- Un émulateur iOS ou Android pour Visual Studio
- [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (facultatif)

## <a name="get-the-source-code"></a>Obtenir le code source

Si vous souhaitez utiliser l’application web fournie, cloner ou télécharger le code source de l’application à partir du dépôt [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) sur GitHub. Ouvrez le fichier *Source/VisualProvision.sln* dans Visual Studio. Par la suite, vous apporterez les modifications nécessaires à certains des fichiers du projet pour être en mesure d’exécuter l’application.

## <a name="create-an-object-detector"></a>Créer un détecteur d’objet

Connectez-vous au [site web du service Vision personnalisée](https://customvision.ai/) et créez un nouveau projet. Spécifiez un projet de détection d’objets et utilisez le domaine Logo ; le service utilisera ainsi un algorithme optimisé pour la détection des logos. 

![boîte de dialogue Nouveau projet sur le site web du service Vision personnalisée dans le navigateur Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Ensuite, vous devez entraîner l’algorithme de détection de logo en chargeant les images des logos des services Azure et en les marquant manuellement. Le dépôt AIVisualProvision inclut un ensemble d’images d’entraînement que vous pouvez utiliser. Sur le site web, sélectionnez le bouton **Add images** (Ajouter des images) situé sous l’onglet **Training Images** (Images d’entraînement), puis accédez au dossier **Documents/Images/Training_DataSet** du référentiel. Vous devez marquer manuellement les logos dans chaque image. Ainsi, si vous testez seulement ce projet, vous pouvez ne charger qu’un sous-ensemble des images. Au minimum, vous devez charger 15 instances de chaque balise que vous envisagez d’utiliser.

Une fois que vous avez chargé les images d’entraînement, sélectionnez la première affichée. Cela fera apparaître la fenêtre de marquage. Dessinez des rectangles et assignez des balises pour chaque logo dans chaque image. 

![image de logos avec des balises appliquées sur le site web du service Vision personnalisée](media/azure-logo-tutorial/tag-logos.png)

L’application est configurée pour utiliser des chaînes de balises spécifiques. Reportez-vous aux définitions dans le fichier *Source\VisualProvision\Services\Recognition\RecognitionService.cs* :

[!code-csharp[tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

Lorsque vous avez marqué une image, naviguez vers la droite pour marquer la suivante. Quittez la fenêtre de marquage une fois terminé.

## <a name="train-the-object-detector"></a>Entraîner le détecteur d’objet

Dans le volet gauche, affectez au commutateur **Tags** (Balises) la valeur **Tagged** (Identifiées) et vous devriez voir toutes vos images. Ensuite, cliquez sur le bouton vert en haut de la page pour entraîner le modèle. Cela apprend à l’algorithme à reconnaître les mêmes balises dans les nouvelles images. Cela teste également le modèle sur certaines de vos images existantes pour générer des scores de précision.

![site web du service Vision personnalisée, sous l’onglet Training Images (Images d’entraînement) ; le bouton Train (Entraîner) est activé](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Obtenir l’URL de prédiction

Une fois votre modèle entraîné, vous êtes prêt à l’intégrer dans votre application. Pour ce faire, vous devez obtenir l’URL du point de terminaison (l’adresse de votre modèle, que l’application interrogera) et la clé de prédiction (pour accorder à l’application l’accès aux demandes de prédiction). Sous l’onglet **Performances**, cliquez sur le bouton **Prediction URL** (URL de prédiction) en haut de la page.

![site web du service Vision personnalisée avec un écran API de prédiction, affichant une adresse URL et une clé API](media/azure-logo-tutorial/cusvis-endpoint.png)

Copiez l’URL du fichier image et la valeur `Prediction-key` dans les champs appropriés, dans le fichier *Source\VisualProvision\AppSettings.cs* :

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Examiner l’utilisation du service Vision personnalisée

Ouvrez le fichier *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* pour voir comment vos URL de point de terminaison et clé de service Vision personnalisée sont utilisées dans l’application. La méthode **PredictImageContentsAsync** prend un flux d’octets d’un fichier image et un jeton d’annulation (pour la gestion des tâches asynchrones), appelle l’API de prédiction du service Vision personnalisée et retourne le résultat de la prédiction. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Ce résultat prend la forme d’une instance **PredictionResult**, qui contient elle-même une liste d’instances **Prediction**. Une **prédiction** contient une balise détectée et l’emplacement de son cadre englobant dans l’image.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Si vous souhaitez en savoir plus sur la façon dont l’application gère ces données, commencez à la méthode **GetResourcesAsync**, définie dans le fichier *Source/VisualProvision/Services/Recognition/RecognitionService.cs*. 

## <a name="add-computer-vision"></a>Ajouter le service Vision par ordinateur

La partie Vision personnalisée de ce tutoriel est terminée, mais si vous souhaitez exécuter l’application, vous devez également intégrer le service Vision par ordinateur. L’application utilise la fonctionnalité de reconnaissance de texte du service Vision par ordinateur pour compléter le processus de détection des logos. Un logo Azure peut être reconnu par son apparence _ou_ par le texte imprimé à côté. Contrairement aux modèles de vision personnalisée, le service Vision par ordinateur est pré-entraîné pour effectuer certaines opérations sur les images ou vidéos.

Abonnez-vous simplement au service Vision par ordinateur pour obtenir une clé et une URL de point de terminaison. Consultez [Guide pratique pour obtenir des clés d’abonnement](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe) si vous avez besoin d’aide pour cette étape.

![service Vision par ordinateur dans le portail Azure, avec le menu de démarrage rapide sélectionné ; un lien pour les clés est mis en surbrillance, comme l’URL du point de terminaison d’API](media/azure-logo-tutorial/comvis-keys.png)

Ensuite, ouvrez le fichier *Source\VisualProvision\AppSettings.cs* et affectez des valeurs correctes aux variables `ComputerVisionEndpoint` et `ComputerVisionKey`.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]


## <a name="create-a-service-principal"></a>Créer un principal du service

L’application nécessite un compte de principal de service Azure pour déployer des services dans le cadre de votre abonnement Azure. Un principal de service vous permet de déléguer des autorisations spécifiques à une application à l’aide du contrôle d’accès en fonction du rôle. Consultez le [guide des principaux de service](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals) si vous souhaitez en savoir plus.

Vous pouvez créer un principal de service à l’aide d’Azure Cloud Shell ou de l’interface de ligne de commande Azure (comme suit). Tout d’abord, connectez-vous et sélectionnez l’abonnement que vous souhaitez utiliser.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Créez ensuite votre principal de service (notez que cela peut prendre un certain temps).

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Après cela, vous devriez voir la sortie JSON suivante contenant les informations d’identification nécessaires.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```
Notez les valeurs `clientId` et `tenantId`. Ajoutez-les dans les champs appropriés, dans le fichier *Source\VisualProvision\AppSettings.cs*.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Exécution de l'application

À ce stade, vous avez donné à l’application l’accès :
* à un modèle entraîné de vision personnalisée
* au service Vision par ordinateur
* à un compte de principal de service 

Pour exécuter l’application, procédez comme suit :

1. Dans l’Explorateur de solutions Visual Studio, sélectionnez le projet VisualProvision.Android ou VisualProvision.iOS, et choisissez un émulateur correspondant ou un appareil mobile connecté dans le menu déroulant de la barre d’outils principale (notez que vous avez besoin d’un appareil MacOS pour exécuter un émulateur iOS). Ensuite, exécutez l’application.

1. Dans le premier écran qui se charge, entrez votre ID client de principal de service, votre ID de locataire et votre mot de passe. Cliquez sur le bouton **Connexion**.

    > [!NOTE]
    > Sur certains émulateurs, le bouton **Connexion** peut ne pas être activé à cette étape. Si cela se produit, arrêtez l’application, ouvrez le fichier _Source/VisualProvision/Pages/LoginPage.xaml_, recherchez l’élément `Button` étiqueté LOGIN BUTTON (bouton de connexion) et supprimez la ligne :
      ```xaml
      IsEnabled="{Binding IsValid}"
      ```
    
    > Ensuite, exécutez de nouveau l’application.

    ![écran d’application avec des champs pour les informations d’identification de principal de service](media/azure-logo-tutorial/app-credentials.png)

1. Dans l’écran suivant, sélectionnez votre abonnement Azure dans le menu déroulant (il doit contenir tous les abonnements auxquels votre principal de service a accès). Cliquez sur le bouton **Continuer**.

    ![écran de l’application avec un champ de liste déroulante pour l’abonnement Azure cible](media/azure-logo-tutorial/app-az-subscription.png)

    À ce stade, l’application peut vous demander d’octroyer à l’appareil photo et au stockage de photos l’accès aux appareils. Acceptez ces autorisations.

1. Ensuite, l’appareil photo est activé sur votre appareil. Prenez une photo d’un des logos des services Azure que vous avez utilisés pour l’entraînement. Une boîte de dialogue de déploiement doit vous inviter à sélectionner une région et un groupe de ressources pour les nouveaux services (comme vous le feriez si vous les déployiez à partir du portail Azure). 

    ![écran de l’appareil photo du smartphone avec deux découpages papier des logos Azure dans la vue](media/azure-logo-tutorial/app-camera-capture.png)

    ![écran de l’application avec des champs pour la saisie du groupe de ressources et de la région de déploiement](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Supprimer des ressources 

Si vous avez suivi toutes les étapes de ce scénario et utilisé l’application pour déployer des services Azure dans votre compte, veillez à accéder au [portail Azure](https://ms.portal.azure.com/) lorsque vous avez terminé et à annuler les services que vous ne souhaitez pas utiliser.

En outre, si vous envisagez de créer à l’avenir votre propre projet de détection d’objet avec le service Vision personnalisée, vous pouvez supprimer le projet de détection de logo que vous avez créé dans ce tutoriel. Un essai gratuit pour le service Vision personnalisée permet d’utiliser deux projets. Sur le [site web du service Vision personnalisée](https://customvision.ai), accédez à **Projects** et sélectionnez la corbeille sous **My New Project**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez configuré et exploré une application Xamarin.Forms complète qui a recours au service Vision personnalisée pour détecter des logos dans les images d’appareil photo d’appareil mobile. Ensuite, découvrez les bonnes pratiques de création d’un modèle de vision personnalisée afin que vous puissiez en créer un efficace et précis pour votre propre application.

> [!div class="nextstepaction"]
> [Guide pratique pour améliorer votre classifieur](getting-started-improving-your-classifier.md)