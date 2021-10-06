---
title: Exporter un modèle par programmation
titleSuffix: Azure Cognitive Services
description: Utilisez la bibliothèque cliente Custom Vision pour exporter un modèle formé.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: pafarley
ms.openlocfilehash: b67c4e6165b90a580a3a29583bdfe93a98d327c8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361390"
---
# <a name="export-a-model-programmatically"></a>Exporter un modèle par programmation

Toutes les options d’exportation disponibles sur le [site web Custom Vision](https://www.customvision.ai/) peuvent être effectuées par programme par le biais des bibliothèques clientes. Vous pouvez effectuer cette opération pour automatiser entièrement le processus de reformation et de mise à jour de l’itération de modèle que vous utilisez sur un appareil local.

Ce guide vous montre comment exporter votre modèle vers un fichier ONNX avec le kit de développement logiciel (SDK) Python.

## <a name="create-a-training-client"></a>Créer un client de formation

Vous devez avoir un objet [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) pour exporter une itération de modèles. Créez des variables pour vos points de terminaisons et clés d’abonnement Azure des ressources de formation Custom Vision et utilisez-les pour créer l’objet client.

```python
ENDPOINT = "PASTE_YOUR_CUSTOM_VISION_TRAINING_ENDPOINT_HERE"
training_key = "PASTE_YOUR_CUSTOM_VISION_TRAINING_SUBSCRIPTION_KEY_HERE"

credentials = ApiKeyCredentials(in_headers={"Training-key": training_key})
trainer = CustomVisionTrainingClient(ENDPOINT, credentials)
```

> [!IMPORTANT]
> N’oubliez pas de supprimer les clés de votre code une fois que vous avez terminé et ne les postez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Pour plus d’informations, consultez l’article sur la [sécurité](../cognitive-services-security.md) Cognitive Services.

## <a name="call-the-export-method"></a>Appeler la méthode d’exportation

Appelez la méthode **export_iteration**.
* Fournissez l’ID de projet, l’ID d’itération du modèle que vous souhaitez exporter. 
* Le paramètre de *plateforme* spécifie la plateforme à exporter vers : les valeurs autorisées sont `CoreML`, `TensorFlow`, `DockerFile`, `ONNX`, `VAIDK` et `OpenVino`. 
* Le paramètre de *saveur* spécifie le format du modèle exporté : les valeurs autorisées sont `Linux`, `Windows`, `ONNX10`, `ONNX12`, `ARM`, `TensorFlowNormal` et `TensorFlowLite`.
* Le paramètre *brut* vous donne la possibilité de récupérer la réponse JSON brute avec la réponse du modèle objet.

```python
project_id = "PASTE_YOUR_PROJECT_ID"
iteration_id = "PASTE_YOUR_ITERATION_ID"
platform = "ONNX"
flavor = "ONNX10"
export = trainer.export_iteration(project_id, iteration_id, platform, flavor, raw=False)
```

Pour plus d’informations, consultez la section **[export_Iteration](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#export-iteration-project-id--iteration-id--platform--flavor-none--custom-headers-none--raw-false----operation-config-)** .

## <a name="download-the-exported-model"></a>Télécharger le modèle exporté

Appelez ensuite la méthode **get_exports** pour vérifier l’état de l’opération d’exportation. L’opération s’exécute de façon asynchrone. Vous devez donc interroger cette méthode jusqu’à ce que l’opération se termine. Une fois l’opération terminée, vous pouvez récupérer l’URI où vous pouvez télécharger l’itération du modèle sur votre appareil.

```python
while (export.status == "Exporting"):
    print ("Waiting 10 seconds...")
    time.sleep(10)
    exports = trainer.get_exports(project_id, iteration_id)
    # Locate the export for this iteration and check its status  
    for e in exports:
        if e.platform == export.platform and e.flavor == export.flavor:
            export = e
            break
    print("Export status is: ", export.status)
```

Pour plus d'informations, consultez la méthode **[get_exports](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#get-exports-project-id--iteration-id--custom-headers-none--raw-false----operation-config-)** .

Vous pouvez ensuite télécharger par programmation le modèle exporté vers un emplacement sur votre appareil.

```python
if export.status == "Done":
    # Success, now we can download it
    export_file = requests.get(export.download_uri)
    with open("export.zip", "wb") as file:
        file.write(export_file.content)
```

## <a name="next-steps"></a>Étapes suivantes

Intégrer votre modèle exporté dans une application en explorant un des articles ou exemples suivants :

* [Utiliser votre modèle Tensorflow avec Python](export-model-python.md)
* [Utiliser votre modèle ONNX avec Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Voir l’exemple de [modèle CoreML dans une application iOS](https://go.microsoft.com/fwlink/?linkid=857726) pour une classification des images en temps réel avec Swift.
* Voir l’exemple de [modèle Tensorflow dans une application Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) pour une classification des images en temps réel sur Android.
* Voir l’exemple de [modèle CoreML avec Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) pour une classification des images en temps réel dans une application Xamarin iOS.