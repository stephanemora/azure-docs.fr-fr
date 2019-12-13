---
title: 'Démarrage rapide : Créer un projet de classification d’images à l’aide du SDK Custom Vision pour Go'
titleSuffix: Azure Cognitive Services
description: Créez un projet, ajoutez des balises, chargez des images, entraînez votre projet et faites une prédiction avec le SDK Go.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: 458b4bfa99ccaf1f3a9bc884581df840ace46055
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969800"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-go-sdk"></a>Démarrage rapide : Créer un projet de classification d’images à l’aide du SDK Custom Vision pour Go

Cet article fournit des informations et un exemple de code pour vous aider à prendre en main le SDK Custom Vision avec Go, afin de générer un modèle de classification d’images. Après la création du projet, vous pouvez ajouter des étiquettes, charger des images, entraîner le projet, obtenir l’URL du point de terminaison de prédiction publié du projet et utiliser ce point de terminaison pour tester programmatiquement une image. Utilisez cet exemple comme modèle pour générer votre propre application Go. Si vous voulez générer et utiliser un modèle de classification _sans_ code, consultez le [guide basé sur navigateur](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prérequis

- [Go 1.8+](https://golang.org/doc/install)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>Installer le kit de développement logiciel (SDK) Vision personnalisée

Pour installer le SDK du service Custom Vision pour Go, exécutez la commande suivante dans PowerShell :

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

Ou, si vous utilisez `dep`, au sein de votre dépôt, exécutez :
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Ajouter le code

Créez un fichier nommé *sample.go* dans le répertoire de projet de votre choix.

### <a name="create-the-custom-vision-service-project"></a>Créer le projet Service Vision personnalisée

Pour créer un nouveau projet Service Vision personnalisée, ajoutez le code suivant à votre script. Insérez vos clés d’abonnement dans les définitions appropriées. Récupérez l’URL de votre point de terminaison à partir de la page Paramètres du site web Custom Vision.

Consultez la méthode [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) pour spécifier d’autres options quand vous créez votre projet (procédure expliquée dans le guide du portail web [Créer un classifieur](getting-started-build-a-classifier.md)).

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "<your endpoint URL>"
    project_name string = "Go Sample Project"
    iteration_publish_name = "classifyModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    project, err := trainer.CreateProject(ctx, project_name, "sample project", nil, string(training.Multilabel))
    if (err != nil) {
        log.Fatal(err)
    }
```

### <a name="create-tags-in-the-project"></a>Créer des balises dans un projet

Pour créer des balises de classification dans votre projet, ajoutez le code suivant à la fin du fichier *sample.go* :

```go
// Make two tags in the new project
hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

### <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Pour ajouter les exemples d’images au projet, insérez le code suivant après la création de mots clés. Ce code charge chaque image avec la balise correspondante. Vous pouvez charger jusqu’à 64 images dans un même lot.

> [!NOTE]
> Vous devez changer le chemin des images, selon l’emplacement auquel vous avez préalablement téléchargé l’exemple de projet du SDK Cognitive Services pour Go.

```go
fmt.Println("Adding images...")
japaneseCherryImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Japanese Cherry"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

hemLockImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Hemlock"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

for _, file := range hemLockImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Hemlock", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))

    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ hemlockTag.ID.String() })
}

for _, file := range japaneseCherryImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Japanese Cherry", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))
    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ cherryTag.ID.String() })
}
```

### <a name="train-the-classifier-and-publish"></a>Entraîner le classifieur et publier

Ce code crée la première itération dans le projet, puis la publie sur le point de terminaison de prédiction. Le nom donné à l’itération publiée peut être utilisé pour envoyer des requêtes de prédiction. Les itérations ne sont pas disponibles sur le point de terminaison de prédiction tant qu’elles n’ont pas été publiées.

```go
fmt.Println("Training...")
iteration, _ := trainer.TrainProject(ctx, *project.ID)
for {
    if *iteration.Status != "Training" {
        break
    }
    fmt.Println("Training status: " + *iteration.Status)
    time.Sleep(1 * time.Second)
    iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
}
fmt.Println("Training status: " + *iteration.Status)

trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Obtenir et utiliser l’itération publiée sur le point de terminaison de prédiction

Pour envoyer une image au point de terminaison de prédiction et récupérer la prédiction, ajoutez le code suivant à la fin du fichier :

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Exécution de l'application

Exécutez *sample.go*.

```shell
go run sample.go
```

Le résultat de l’application ressemble au texte suivant :

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Vous pouvez ensuite vérifier que l’image test (trouvée dans **<base_image_url>/Images/Test**) est balisée de façon appropriée. Vous pouvez aussi revenir sur le [site web Custom Vision](https://customvision.ai) et consulter l’état actuel de votre nouveau projet.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous savez maintenant comment effectuer la classification d’images dans le code. Cet exemple exécute une itération d’apprentissage unique, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour plus de précision.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](test-your-model.md)