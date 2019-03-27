---
title: 'Démarrage rapide : Kit de développement logiciel (SDK) Python'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser le SDK Python pour effectuer des tâches courantes.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: pafarley
ms.openlocfilehash: ffecc07c49db8fd1b27cc2dd82192aa31a7fbd19
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194977"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>SDK Vision par ordinateur d’Azure Cognitive Services pour Python

Le service Vision par ordinateur offre aux développeurs un accès à des algorithmes avancés pour le traitement d’images et le renvoi d’informations. Les algorithmes du service Vision par ordinateur analysent le contenu d’une image de différentes manières, selon les composants visuels qui vous intéressent. 

* [Analyser une image](#analyze-an-image)
* [Obtenir une liste de domaines](#get-subject-domain-list)
* [Analyser une image par domaine](#analyze-an-image-by-domain)
* [Obtenir la description textuelle d’une image](#get-text-description-of-an-image)
* [Obtenir le texte manuscrit d’une image](#get-text-from-image)
* [Générer une miniature](#generate-thumbnail)

Pour plus d’informations sur ce service, consultez [Qu’est-ce que le service Vision par ordinateur ?][computervision_docs].

Vous cherchez plus de documentation ?

* [Documentation de référence du SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Documentation sur l’API Vision par ordinateur de Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Prérequis

* [Python 3.6+][python]
* [Clé Vision par ordinateur][computervision_resource] gratuite et région associée. Vous avez besoin de ces valeurs quand vous créez l’instance de l’objet client [ComputerVisionAPI][ref_computervisionclient]. Utilisez l’une des méthodes suivantes pour obtenir ces valeurs. 

### <a name="if-you-dont-have-an-azure-subscription"></a>Si vous n’avez pas d’abonnement Azure

Créez une clé gratuite valide pendant 7 jours avec l’expérience d’**[essai gratuit][computervision_resource]** pour le service Vision par ordinateur. Quand la clé est créée, copiez-la ainsi que le nom de la région. Vous en aurez besoin pour [créer le client](#create-client).

Gardez les éléments suivants une fois que la clé est créée :

* Valeur de la clé : chaîne de 32 caractères au format `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` 
* Région de la clé : sous-domaine de l’URL du point de terminaison, https://**westcentralus**.api.cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Si vous avez un abonnement Azure

La méthode la plus simple pour créer une ressource dans votre abonnement consiste à utiliser la commande [Azure CLI][azure_cli] suivante. Elle crée une clé Cognitive Services qui peut être utilisée dans de nombreux services cognitifs. Vous devez choisir le nom du groupe de ressources _existant_, par exemple, « my-cogserv-group » et le nom de la nouvelle ressource Vision par ordinateur, comme « my-computer-vision-resource ». 

```Bash
RES_REGION=westeurope 
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind CognitiveServices \
    --sku S0 \
    --yes
```

<!--
## Installation

Install the Azure Cognitive Services Computer Vision SDK with [pip][pip], optionally within a [virtual environment][venv].

### Configure a virtual environment (optional)

Although not required, you can keep your base system and Azure SDK environments isolated from one another if you use a [virtual environment][virtualenv]. Execute the following commands to configure and then enter a virtual environment with [venv][venv], such as `cogsrv-vision-env`:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```
-->

### <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

Installez le [package][pypi_computervision] du SDK Vision par ordinateur d’Azure Cognitive Services pour Python avec [pip][pip] :

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Une fois votre ressource Vision par ordinateur créée, utilisez sa **région** et l’une de ses **clés de compte** pour instancier l’objet client.

Utilisez ces valeurs quand vous créez l’instance de l’objet client [ComputerVisionAPI][ref_computervisionclient]. 

Par exemple, utilisez le terminal Bash pour définir les variables d’environnement :

```Bash
ACCOUNT_REGION=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-region"></a>Pour les utilisateurs avec un abonnement Azure, obtenir des informations d’identification pour la clé et de région

Si vous ne vous souvenez pas de votre région et de votre clé, vous pouvez utiliser la méthode suivante pour les rechercher. Si vous devez créer une clé et une région, vous pouvez utiliser la méthode pour les [titulaires d’abonnement Azure](#if-you-have-an-azure-subscription) ou pour les [utilisateurs sans abonnement Azure](#if-you-dont-have-an-azure-subscription).

Utilisez l’extrait [Azure CLI][cloud_shell] ci-dessous pour remplir les deux variables d’environnement avec la **région** et l’une des **clés** du compte Vision par ordinateur (ces valeurs sont également disponibles dans le [portail Azure][azure_portal]). L’extrait de code est mis en forme pour l’interpréteur de commandes Bash.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_REGION=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query location \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```


### <a name="create-client"></a>Créer un client

Obtenez la région et la clé des variables d’environnement, puis créez l’objet client [ComputerVisionAPI][ref_computervisionclient].  

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionAPI
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

# Get region and key from environment variables
import os
region = os.environ['ACCOUNT_REGION']
key = os.environ['ACCOUNT_KEY']

# Set credentials
credentials = CognitiveServicesCredentials(key)

# Create client
client = ComputerVisionAPI(region, credentials)
```

## <a name="examples"></a>Exemples

Vous avez besoin d’un objet client [ComputerVisionAPI][ref_computervisionclient] avant d’utiliser une des tâches suivantes.

### <a name="analyze-an-image"></a>Analyser une image

Vous pouvez analyser une image à la recherche de certaines caractéristiques avec [`analyze_image`][ref_computervisionclient_analyze_image]. Utilisez la propriété [`visual_features`][ref_computervision_model_visualfeatures] pour définir les types d’analyse à effectuer sur l’image. Les valeurs courantes sont `VisualFeatureTypes.tags` et `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Obtenir une liste de domaines

Passez en revue les domaines utilisés pour analyser votre image avec [`list_models`][ref_computervisionclient_list_models]. Ces noms de domaine sont utilisés lors de l’[analyse d’une image par domaine](#analyze-an-image-by-domain). `landmarks` est un exemple de domaine.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analyser une image par domaine

Vous pouvez analyser une image par domaine avec [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain]. Obtenez la [liste des domaines pris en charge](#get-subject-domain-list) pour utiliser le nom de domaine approprié.  

```Python
# type of prediction
domain = "landmarks"

# Public domain image of Eiffel tower
url = "https://images.pexels.com/photos/338515/pexels-photo-338515.jpeg"

# English language response
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>Obtenir la description textuelle d’une image

Vous pouvez obtenir la description textuelle en langage naturel d’une image avec [`describe_image`][ref_computervisionclient_describe_image]. Demandez plusieurs descriptions avec la propriété `max_description` si vous effectuez une analyse de texte à la recherche de mots clés associés à l’image. Voici des exemples de description textuelle pour l’image suivante : `a train crossing a bridge over a body of water`, `a large bridge over a body of water` et `a train crossing a bridge over a large body of water`.

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>Obtenir le texte d’une image

Vous pouvez obtenir n’importe quel texte manuscrit ou imprimé figurant dans une image. Pour cela, deux appels au SDK sont nécessaires : [`recognize_text`][ref_computervisionclient_recognize_text] et [`get_text_operation_result`][ref_computervisionclient_get_text_operation_result]. L’appel à recognize_text est asynchrone. Dans les résultats de l’appel à get_text_operation_result, vous devez vérifier si le premier appel s’est terminé avec [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes] avant d’extraire les données texte. Les résultats incluent le texte, ainsi que les coordonnées du cadre englobant le texte. 

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextRecognitionMode
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.recognize_text(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
while result.status in ['NotStarted', 'Running']:
    time.sleep(1)
    result = client.get_text_operation_result(operationId)

# Get data
if result.status == TextOperationStatusCodes.succeeded:

    for line in result.recognition_result.lines:
        print(line.text)
        print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Générer une miniature

Vous pouvez générer une miniature (JPG) d’une image avec [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]. La miniature ne doit pas forcément être dans les mêmes proportions que l’image d’origine. 

Installez **Pillow** pour utiliser cet exemple :

```bash
pip install Pillow
``` 

Une fois que Pillow est installé, utilisez le package de l’exemple de code suivant pour générer l’image miniature.

```Python
# Pillow package
from PIL import Image

# IO package to create local image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="general"></a>Généralités

Quand vous interagissez avec l’objet client [ComputerVisionAPI][ref_computervisionclient] à l’aide du SDK Python, utilisez la classe [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] pour retourner des erreurs. Les erreurs retournées par le service correspondent aux codes d’état HTTP retournés pour les demandes d’API REST.

Par exemple, si vous essayez d’analyser une image avec une clé non valide, une erreur `401` est retournée. Dans l’extrait suivant, l’[erreur][ref_httpfailure] est gérée correctement en interceptant l’exception et en affichant des informations supplémentaires sur l’erreur.

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and region are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Gérer les erreurs temporaires avec de nouvelles tentatives

Quand vous travaillez avec le client [ComputerVisionAPI][ref_computervisionclient], vous pouvez rencontrer des échecs passagers causés par des [limites de débit][computervision_request_units] appliquées par le service ou d’autres problèmes passagers comme des pannes du réseau. Pour plus d’informations sur la gestion de ces types d’échecs, consultez le [modèle Nouvelle tentative][azure_pattern_retry] dans le guide des modèles de conception de cloud et le [modèle Disjoncteur] [azure_pattern_circuit_breaker] connexe.

### <a name="more-sample-code"></a>Autres exemples de code

Plusieurs exemples du SDK Vision par ordinateur pour Python sont disponibles dans le dépôt GitHub du SDK. Vous y trouverez des exemples de code pour d’autres scénarios fréquents associés à l’utilisation du service Vision par ordinateur :

* [recognize_text][recognize-text]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application de balises de contenu aux images](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/en-us/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://azure.microsoft.com/en-us/try/cognitive-services/?

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python


[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-url--visual-features-none--details-none--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#list-models-custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-by-domain-model--url--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#describe-image-url--max-candidates--1---language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_recognize_text]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#recognize-text-url--mode--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#get-text-operation-result-operation-id--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#generate-thumbnail-width--height--url--smart-cropping-false--custom-headers-none--raw-false--callback-none----operation-config-


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/

[recognize-text]:https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/vision/computer_vision_samples.py

