---
title: 'Tutoriel : Utiliser un modèle ONNX avec Windows ML - Service Vision personnalisée'
titlesuffix: Azure Cognitive Services
description: Découvrez comment créer une application UWP Windows qui utilise un modèle ONNX exporté à partir d’Azure Cognitive Services.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 3a9e9bc92ce38c4bb8d6d83c8017fa223342e7d2
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365602"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Tutoriel : utiliser un modèle ONNX à partir de Custom Vision avec Windows ML (préversion)

Découvrez comment utiliser un modèle ONNX exporté à partir du service Custom Vision avec Windows ML (préversion).

Les informations dans ce document indiquent comment utiliser un fichier ONNX exporté à partir du service Custom Vision avec Windows ML. Un exemple d’application UWP Windows est fourni. Un modèle formé capable de reconnaître les chiens et les chats est inclus avec l’exemple. Une procédure est également fournie pour vous expliquer comment vous pouvez utiliser votre propre modèle avec cet exemple.

> [!div class="checklist"]
> * À propos de l’exemple d’application
> * Obtenir l’exemple de code
> * Exécuter l’exemple
> * Utiliser votre propre modèle

## <a name="prerequisites"></a>Prérequis

* Un appareil Windows 10 avec :

    * Un appareil photo.

    * Visual Studio 2017 version 15.7 ou ultérieure avec la charge de travail __Développement pour la plateforme Windows universelle__ activée.

    * Mode Développeur activé. Pour plus d’informations, consultez le document [Activez votre appareil pour le développement](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

* (Facultatif) Un fichier ONNX exporté à partir du service Custom Vision. Pour plus d’informations, consultez le document [Exporter votre modèle pour l’utiliser avec des appareils mobiles](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

    > [!NOTE]
    > Pour utiliser votre propre modèle, suivez les étapes indiquées dans la section [Utiliser votre propre modèle](#use-your-own-model).

## <a name="about-the-example-app"></a>À propos de l’exemple d’application

L’application est une application UWP Windows générique. Elle utilise l’appareil photo sur votre appareil Windows 10 pour fournir des images au modèle. Les balises et les scores retournés par le modèle sont affichés sous l’aperçu vidéo.

* Lorsque les données parviennent l’appareil photo, [MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) est utilisé pour extraire des images individuelles. Les images sont envoyées au modèle pour calculer les scores.

* Le modèle retourne les balises sur lesquelles il a été formé et une valeur float qui indique la confiance selon laquelle l’image contient l’élément.

### <a name="the-ui"></a>L’interface utilisateur

L’interface utilisateur pour l’exemple d’application est créée à l’aide des contrôles __CaptureElement__ et __TextBlock__. CaptureElement affiche un aperçu de la vidéo à partir de l’appareil photo et TextBlock affiche les résultats retournés à partir du modèle. 

### <a name="the-model"></a>Le modèle

Le modèle (`cat-or-dog.onnx`) fourni avec l’exemple a été créé et formé à l’aide du service Custom Vision de Cognitive Services. Le modèle formé a ensuite été exporté sous forme de modèle ONNX. Pour plus d’informations sur l’utilisation de ce service, consultez les documents [Comment créer un classifieur](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) et [Exporter votre modèle pour l’utiliser avec des appareils mobiles](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

> [!IMPORTANT]
> Le modèle fourni avec l’exemple a été formé avec un petit ensemble d’images de chiens et de chats. Il n’est sans doute pas le meilleur au monde pour reconnaître des chiens et des chats.

### <a name="the-model-class-file"></a>Fichier de classe de modèle

Lorsque vous ajoutez un fichier ONNX à une application UWP Windows, un fichier .cs est créé. Ce fichier a le même nom que le fichier `.onnx` (`cat-or-dog` dans cet exemple) et contient les classes utilisées pour fonctionner avec le modèle issu de C#. Toutefois, les entités dans la classe générée peuvent avoir des noms tels que `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Vous pouvez renommer ces entités en toute sécurité (clic avec le bouton droit, renommer) et choisir un nom convivial.

> [!NOTE]
> L’exemple de code a refactorisé les noms de la classe et de la méthode générées sur ce qui suit :
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>Accès à l’appareil photo

L’onglet __Fonctionnalités__ dans le fichier `Package.appxmanifest` est configuré pour autoriser l’accès à la webcam et au microphone.

> [!NOTE]
> Même si cet exemple ne contient pas de son, j’ai dû activer le microphone avant de pouvoir accéder à l’appareil photo de mon appareil.

L’application essaye d’accéder à l’appareil photo à l’arrière de votre appareil, le cas échéant. Elle utilise la classe [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) pour commencer la capture vidéo avec l’appareil photo. [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) est utilisé pour capturer des images vidéo et les envoyer au modèle.

## <a name="get-the-example-code"></a>Obtenir l’exemple de code

L’exemple d’application est disponible à l’adresse [https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP).

## <a name="run-the-example"></a>Exécuter l’exemple

1. Utilisez la touche `F5` pour démarrer l’application à partir de Visual Studio. Il peut vous être demandé d’activer le mode Développeur. Pour plus d’informations, consultez le document [Activez votre appareil pour le développement](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

2. À l’invite, autorisez l’application à accéder à l’appareil photo et au microphone de votre appareil.

3. Dirigez l’appareil photo vers un chien ou un chat. Le score qui indique si l’image contient un chien ou un chat s’affiche en dessous de l’aperçu dans l’application.

    > [!TIP]
    > En cas d’absence de chien ou de chat à proximité, vous pouvez utiliser une photo représentant un chien ou un chat.

## <a name="use-your-own-model"></a>Utiliser votre propre modèle

Pour utiliser votre propre modèle, procédez comme suit :

> [!IMPORTANT]
> Les étapes dans cette section renomment le modèle actuel (cat-or-dog.cs) et refactorisent les noms de la classe et de la méthode du nouveau modèle. Ceci permet d’éviter les noms en double avec l’exemple de modèle.

1. Formez un modèle avec le service Custom Vision. Pour plus d’informations sur la manière de former un modèle, consultez [Comment créer un classifieur](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier).

2. Exportez le modèle formé en tant que modèle ONNX. Pour plus d’informations sur la manière d’exporter un modèle, consultez le document [Exporter votre modèle pour l’utiliser avec des appareils mobiles](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model).

3. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le fichier __cat-or-dog.cs__ et renommez-le __cat-or-dog.txt__. Le fait de le renommer permet d’éviter les noms en double avec le nouveau modèle.

    > [!TIP]
    > Vous pouvez aussi utiliser des noms différents pour les noms des classes dans le nouveau modèle, mais il est plus facile de réutiliser les noms existants.

4. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur l’entrée __VisionApp__, puis sélectionnez __Ajouter__ > __Élément existant...__.

5. Pour générer une classe pour le modèle, sélectionnez le fichier ONNX à importer, puis sélectionnez le bouton __Ajouter__. Une nouvelle classe avec le même nom que le fichier ONNX (mais avec une extension `.cs`) est ajoutée dans l’Explorateur de solutions.

6. Ouvrez le fichier .cs généré et recherchez les noms des éléments suivants :

    > [!IMPORTANT]
    > Utilisez l’exemple de fichier `cat-or-dog.txt` comme guide pour reconnaître les classes et les fonctions.

    * La classe qui définit l’entrée du modèle. Le nom généré peut ressembler à `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`. Renommez cette classe __ModelInput__.
    * La classe qui définit la sortie du modèle. Le nom généré peut ressembler à `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`. Renommez cette classe __ModelOutput__.
    * La classe qui définit le modèle. Le nom généré peut ressembler à `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Renommez cette classe __Model__.
    * La méthode qui crée le modèle. Le nom généré peut ressembler à `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`. Renommez cette méthode __CreateModel__.

7. Dans l’Explorateur de solutions, déplacez le fichier `.onnx` vers le dossier __Ressources__. 

8. Pour inclure le fichier ONNX dans le package d’application, sélectionnez le fichier `.onnx` et définissez __Action de génération__ sur __Contenu__ dans les propriétés.

9. Ouvrez le fichier __MainPage.xaml.cs__. Recherchez la ligne suivante et remplacez le nom de fichier par le nouveau fichier `.onnx` :

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    Cette modification charge le nouveau modèle lors de l’exécution.

10. Générez et exécutez l'application. Elle utilise désormais le nouveau modèle pour noter les images.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir d’autres façons d’exporter et d’utiliser un modèle Custom Vision, consultez les documents suivants :

* [Exporter votre modèle](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Utiliser le modèle Tensorflow exporté dans une application Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Utiliser le modèle CoreML exporté dans une application Swift iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Utiliser le modèle CoreML exporté dans une application iOS avec Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Pour plus d’informations sur l’utilisation de modèles ONNX avec Windows ML, consultez le document [Intégrer un modèle dans votre application avec Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model).
