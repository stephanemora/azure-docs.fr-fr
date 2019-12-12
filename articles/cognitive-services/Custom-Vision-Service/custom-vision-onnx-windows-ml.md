---
title: 'Didacticiel : Utiliser un modèle ONNX avec Windows ML - Service Custom Vision'
titleSuffix: Azure Cognitive Services
description: Découvrez comment créer une application UWP Windows qui utilise un modèle ONNX exporté à partir d’Azure Cognitive Services.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 3b56cd12aec21cffd98a0143f5cf3f083ec55e94
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74960551"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Didacticiel : Utiliser un modèle ONNX à partir de Custom Vision avec Windows ML (préversion)

Découvrez comment utiliser un modèle ONNX exporté à partir du service Custom Vision avec Windows ML (préversion).

Les informations dans ce document indiquent comment utiliser un fichier ONNX exporté à partir du service Custom Vision avec Windows ML. Un exemple d’application UWP Windows est fourni. Un modèle formé doté d’une fonctionnalité de reconnaissance est inclus dans l’exemple. Une procédure est également fournie pour vous expliquer comment vous pouvez utiliser votre propre modèle avec cet exemple.

> [!div class="checklist"]
> * À propos de l’exemple d’application
> * Obtenir l’exemple de code
> * Exécuter l’exemple
> * Utiliser votre propre modèle

## <a name="prerequisites"></a>Prérequis

* Windows 10 version 1809 ou ultérieure

* Microsoft Windows SDK pour build 17763 ou ultérieure

* Visual Studio 2017 version 15.7 ou ultérieure avec la charge de travail __Développement pour la plateforme Windows universelle__ activée.

* Mode Développeur activé. Pour plus d’informations, consultez le document [Activez votre appareil pour le développement](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>À propos de l’exemple d’application

L’application est une application UWP Windows générique. Elle vous permet de sélectionner une image à partir de votre ordinateur, qui est ensuite envoyée au modèle. Les balises et les scores renvoyés par le modèle sont affichés en regard de l’image.

## <a name="get-the-example-code"></a>Obtenir l’exemple de code

L’exemple d’application est disponible à l’adresse [https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample).

## <a name="run-the-example"></a>Exécuter l’exemple

1. Utilisez la touche `F5` pour démarrer l’application à partir de Visual Studio. Il peut vous être demandé d’activer le mode Développeur. Pour plus d’informations, consultez le document [Activez votre appareil pour le développement](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

1. Lorsque l’application démarre, utilisez le bouton pour sélectionner une image pour le scoring.

## <a name="use-your-own-model"></a>Utiliser votre propre modèle

Pour utiliser votre propre modèle, procédez comme suit :

1. [Créez un classifieur et effectuez son apprentissage](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) avec Service Vision personnalisée. Pour exporter le modèle, sélectionnez un domaine __compact__ comme **General (compact)** (Général (compact)). Pour exporter un classifieur existant, convertissez le domaine en domaine compact en sélectionnant l’icône d’engrenage en haut à droite. Dans __Paramètres__, choisissez un modèle compact, enregistrez-le et effectuez l’apprentissage de votre projet.  

1. [Exportez votre modèle](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) en accédant à l’onglet Performances. Sélectionnez une itération formée avec un domaine compact ; un bouton « Exporter » va s’afficher. Sélectionnez *Exporter*, *ONNX*, puis *Exporter*. Une fois que le fichier est prêt, sélectionnez le bouton *Télécharger*.

1. Déposez le fichier ONNX dans le dossier __Ressources__ de votre projet. 

1. Dans l’Explorateur de solutions, cliquez sur le dossier Ressources, puis sélectionnez __Ajouter un élément existant__. Sélectionnez le fichier ONNX.

1. Dans l’Explorateur de solutions, sélectionnez le fichier ONNX dans le dossier Ressources. Modifiez les propriétés suivantes du fichier :

    * __Action de génération__ -> __Contenu__
    * __Copier dans le répertoire de sortie__ -> __Copier si plus récent__

1. Remplacez la variable `_onnxFileNames` par le nom du fichier ONNX. Remplacez également `ClassLabel` par le nombre d’étiquettes que le modèle contient.

1. Créez et exécutez.

1. Cliquez sur le bouton pour sélectionner l’image à évaluer.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir d’autres façons d’exporter et d’utiliser un modèle Custom Vision, consultez les documents suivants :

* [Exporter votre modèle](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Utiliser le modèle Tensorflow exporté dans une application Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Utiliser le modèle CoreML exporté dans une application Swift iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Utiliser le modèle CoreML exporté dans une application iOS avec Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Pour plus d’informations sur l’utilisation de modèles ONNX avec Windows ML, consultez le document [Intégrer un modèle dans votre application avec Windows ML](/windows/ai/windows-ml/integrate-model).
