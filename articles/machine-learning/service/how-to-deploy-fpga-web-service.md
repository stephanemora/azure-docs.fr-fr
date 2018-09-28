---
title: Déployer un modèle en tant que service web sur un FPGA avec Azure Machine Learning
description: Découvrez comment déployer un service web avec un modèle en cours d’exécution sur un FPGA avec Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 09/24/2018
ms.openlocfilehash: ee67585a523ab96b1442d9eee3e9dfd55a758d32
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971482"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Déployer un modèle en tant que service web sur un FPGA avec Azure Machine Learning

Vous pouvez déployer un modèle en tant que service web sur des [FPGA (Field Programmable Gate Arrays)](concept-accelerate-with-fpgas.md).  L’utilisation de FGPA assure une inférence à très faible latence, même avec une taille de lot unique.   

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Un espace de travail Azure Machine Learning et le SDK Azure Machine Learning pour Python installé. Découvrez comment obtenir ces prérequis à l’aide du document [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md).
 
  - Votre espace de travail doit se trouver dans la région *USA Est 2*.

  - Installez les extras :

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Créer et déployer votre modèle
Créez un pipeline pour prétraiter l’image en entrée, caractérisez-la à l’aide de ResNet 50 sur un FPGA, puis exécutez les caractéristiques par le biais d’un classifieur entraîné sur le jeu de données ImageNet.

Suivez les instructions pour :

* Définir le pipeline de modèle
* Déployer le modèle
* Utiliser le modèle déployé
* Supprimer les services déployés

> [!IMPORTANT]
> Pour optimiser la latence et le débit, votre client doit se trouver dans la même région Azure que le point de terminaison.  Les API sont actuellement créées dans la région Azure USA Est.

### <a name="get-the-notebook"></a>Obtenir le bloc-notes

Pour des raisons pratiques, ce tutoriel est disponible en tant que bloc-notes Jupyter. Appliquez l’une de ces méthodes pour exécuter le bloc-notes `project-brainwave/project-brainwave-quickstart.ipynb` :

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

### <a name="preprocess-image"></a>Prétraiter l’image
La première étape du pipeline consiste à prétraiter les images.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```
### <a name="add-featurizer"></a>Ajouter un caractériseur
Initialisez le modèle et téléchargez un point de contrôle TensorFlow de la version quantifiée de ResNet50 à utiliser comme caractériseur.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Ajouter un classifieur
Ce classifieur a été entraîné sur le jeu de données ImageNet.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Créer une définition de service
Maintenant que vous avez défini le prétraitement d’image, le caractériseur et le classifieur qui s’exécute sur le service, vous pouvez créer une définition de service. La définition de service est un ensemble de fichiers générés à partir du modèle déployé sur le service FPGA. Elle est constituée d’un pipeline. Le pipeline est une série de phases qui sont exécutées dans l’ordre.  Les phases TensorFlow, Keras et BrainWave sont prises en charge.  Les phases sont exécutées dans l’ordre sur le service, la sortie de chaque phase étant fournie comme entrée à la phase suivante.

Pour créer une phase TensorFlow, spécifiez une session contenant le graphe (ici, le graphe par défaut est utilisé) et les tensors d’entrée et de sortie de cette phase.  Ces informations servent à enregistrer le graphe afin qu’il puisse être exécuté sur le service.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Déployer le modèle
Créez un service à partir de la définition de service.  Votre espace de travail doit se trouver dans la région USA Est 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>Tester le service
Pour envoyer une image à l’API et tester la réponse, ajoutez un mappage à partir de l’ID de classe de sortie au nom de la classe ImageNet.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Appelez votre service et remplacez le nom de fichier « your-image.jpg » ci-dessous par une image de votre machine. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Nettoyer le service
Supprimez le service.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Sécuriser les services web FPGA

Les modèles Azure Machine Learning exécutés sur des FPGA fournissent la prise en charge du protocole SSL et l’authentification basée sur la clé. Cela vous permet de restreindre l’accès à votre service et de sécuriser les données envoyées par vos clients.

> [!IMPORTANT]
> L’authentification est activée uniquement pour les services qui ont fourni un certificat SSL et une clé. 
>
> Si vous n’activez pas le protocole SSL, n’importe quel utilisateur sur Internet sera en mesure de passer des appels au service.
>
> Si vous activez SSL, une clé d’authentification est obligatoire pour accéder au service.

SSL chiffre les données envoyées entre le client et le service. Il est également utilisé par le client pour vérifier l’identité du serveur.

Vous pouvez déployer un service sur lequel SSL est activé ou mettre à jour un service déjà déployé de manière à activer le protocole. La procédure est la même :

1. Acquérir un nom de domaine.

2. Acquérir un certificat SSL.

3. Déployer ou mettre à jour le service sur lequel SSL est activé.

4. Mettre à jour votre serveur DNS pour pointer vers le service.

### <a name="acquire-a-domain-name"></a>Acquérir un nom de domaine

Si vous ne possédez pas déjà un nom de domaine, vous pouvez en acheter un auprès d’un __bureau d’enregistrement de noms de domaine__. La procédure et le coût varient selon les bureaux d’enregistrement. Le bureau d’enregistrement fournit également des outils qui vous permettent de gérer le nom de domaine. Ces outils sont utilisés pour mapper un nom de domaine complet (par exemple, www.contoso.com) à l’adresse IP sur laquelle votre service est hébergé.

### <a name="acquire-an-ssl-certificate"></a>Acquérir un certificat SSL

Il existe de nombreuses façons d’obtenir un certificat SSL. Le moyen le plus courant consiste à en acheter un auprès d’une __autorité de certification__ (CA). Quel que soit l’endroit où vous obtenez le certificat, vous avez besoin des fichiers suivants :

* Un __certificat__. Le certificat doit contenir la chaîne d’approbation et doit être codé en PEM.
* Une __clé__. La clé doit être codée en PEM.

> [!TIP]
> Si l’autorité de certification ne peut pas fournir le certificat et la clé sous forme de fichiers PEM, vous pouvez utiliser un utilitaire tel que [OpenSSL](https://www.openssl.org/) pour modifier le format.

> [!IMPORTANT]
> Les certificats auto-signés doivent être utilisés uniquement pour le développement. Ils ne doivent pas être utilisés en production.
>
> Si vous utilisez un certificat auto-signé, consultez la section [Utilisation des services avec des certificats auto-signés](#self-signed) pour obtenir des instructions spécifiques.

> [!WARNING]
> Lorsque vous demandez un certificat, vous devez fournir le nom de domaine complet (FQDN) de l’adresse que vous envisagez d’utiliser pour le service. Par exemple, contoso.com. L’adresse estampillée sur le certificat et l’adresse utilisée par les clients sont comparées pour valider l’identité du service.
>
> Si les adresses ne correspondent pas, les clients reçoivent une erreur. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Déployer ou mettre à jour le service sur lequel SSL est activé

Pour déployer le service avec le protocole SSL activé, définissez le paramètre `ssl_enabled` sur `True`. Définissez le paramètre `ssl_certificate` sur la valeur du fichier __certificate__ et le paramètre `ssl_key` sur la valeur du fichier __key__. L’exemple suivant illustre le déploiement d’un service sur lequel SSL est activé :

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

La réponse de l’opération `create_service` contient l’adresse IP du service. L’adresse IP est utilisée lors du mappage du nom DNS à l’adresse IP du service.

La réponse contient également une __clé primaire__ et une __clé secondaire__ qui permettent d’utiliser le service.

### <a name="update-your-dns-to-point-to-the-service"></a>Mettre à jour votre serveur DNS pour pointer vers le service

Utilisez les outils fournis par votre bureau d’enregistrement de noms de domaine pour mettre à jour l’enregistrement DNS pour votre nom de domaine. L’enregistrement doit pointer vers l’adresse IP du service.

> [!NOTE]
> Selon le bureau d’enregistrement et la durée de vie (TTL) configurée pour le nom de domaine, les clients peuvent avoir à patienter quelques minutes voire plusieurs heures avant de pouvoir résoudre le nom de domaine.

### <a name="consume-authenticated-services"></a>Utiliser des services authentifiés

Les exemples suivants montrent comment utiliser un service authentifié à l’aide de Python et de C# :

> [!NOTE]
> Remplacez `authkey` par la clé primaire ou secondaire retournée lors de la création du service.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

D’autres clients gRPC peuvent authentifier les demandes en définissant un en-tête d’autorisation. L’approche générale consiste à créer un objet `ChannelCredentials` qui associe `SslCredentials` à `CallCredentials`. Celui-ci est ajouté à l’en-tête d’autorisation de la demande. Pour plus d’informations sur l’implémentation de la prise en charge pour vos en-têtes spécifiques, consultez [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html).

Les exemples suivants montrent comment définir l’en-tête en C# et Go :

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>Utilisation des services avec des certificats auto-signés

Il existe deux façons de permettre au client de s’authentifier auprès d’un serveur sécurisé avec un certificat auto-signé :

* Sur le système client, définissez la variable d’environnement `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` sur le système client pour pointer vers le fichier de certificat.

* Lorsque vous construisez un objet `SslCredentials`, passez le contenu du fichier de certificat au constructeur.

Ces deux méthodes obligent gRPC à utiliser le certificat en tant que certificat racine.

> [!IMPORTANT]
> gRPC n’accepte pas les certificats non approuvés. L’utilisation d’un certificat non approuvé entraîne un échec avec un code d’état `Unavailable`. Les détails de l’échec contiennent `Connection Failed`.
