---
title: Créer un client pour consommer un service web déployé
titleSuffix: Azure Machine Learning service
description: Apprenez à utiliser un service web généré dans le cadre du déploiement d'un modèle Azure Machine Learning. Le service web expose une API REST. Créez des clients pour cette API en utilisant le langage de programmation de votre choix.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 4c5cb5a821d9bae6841c3229155c69fd2aa20e13
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55698307"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Utiliser un modèle Azure Machine Learning déployé en tant que service web

Le déploiement d’un modèle Azure Machine Learning en tant que service web crée une API REST. Vous pouvez envoyer des données à cette API et recevoir la prédiction retournée par le modèle. Dans ce document, découvrez comment créer des clients pour le service web en utilisant C#, Go, Java et Python.

Vous créez un service web lorsque vous déployez une image sur Azure Container Instances, Azure Kubernetes Service ou Project Brainwave (FPGA). Vous créez des images à partir de modèles inscrits et de fichiers de scoring. Vous récupérez l’URI utilisé pour accéder à un service web utilisant le [kit de développement logiciel (SDK) Azure Machine Learning](https://aka.ms/aml-sdk). Si l’authentification est activée, vous pouvez également utiliser le kit de développement logiciel (SDK) pour obtenir les clés d’authentification.

Le flux de travail général pour la création d’un client qui utilise un service web de Machine Learning est le suivant :

1. Utiliser le kit de développement logiciel (SDK) pour obtenir les informations de connexion.
1. Déterminer le type de données de demande utilisées par le modèle.
1. Créer une application qui appelle le service web.

## <a name="connection-information"></a>informations de connexion

> [!NOTE]
> Utilisez le kit de développement logiciel (SDK) Azure Machine Learning pour obtenir les informations du service web. Il s’agit d’un kit de développement logiciel (SDK) Python. Vous pouvez utiliser n’importe quel langage pour créer un client pour le service.

La classe [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) fournit les informations nécessaires pour créer un client. Les propriétés `Webservice` suivantes sont utiles pour créer une application cliente :

* `auth_enabled` -Si l’authentification est activée, `True` ; sinon, `False`.
* `scoring_uri` -L’adresse de l’API REST.

Il y a trois manières de récupérer ces informations pour les services web déployés :

* Lorsque vous déployez un modèle, un objet `Webservice` est retourné avec les informations sur le service :

    ```python
    service = Webservice.deploy_from_model(name='myservice',
                                           deployment_config=myconfig,
                                           models=[model],
                                           image_config=image_config,
                                           workspace=ws)
    print(service.scoring_uri)
    ```

* Vous pouvez utiliser `Webservice.list` afin de récupérer une liste de services web déployés pour les modèles dans votre espace de travail. Vous pouvez ajouter des filtres pour affiner la liste des informations retournées. Pour plus d’informations sur les éléments permettant de filtrer, voir la documentation de référence [Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py).

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    ```

* Si vous connaissez le nom du service déployé, vous pouvez créer une instance de `Webservice` et fournir les noms de l’espace de travail et du service en tant que paramètres. Le nouvel objet contient des informations sur le service déployé.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    ```

### <a name="authentication-key"></a>Clé d’authentification

Lorsque vous activez l’authentification pour un déploiement, vous créez automatiquement des clés d’authentification.

* L’authentification est activée par défaut lors du déploiement sur Azure Kubernetes Service.
* L’authentification est désactivée par défaut lors du déploiement sur Azure Container Instances.

Pour contrôler l’authentification, utilisez le paramètre `auth_enabled` pendant la création ou la mise à jour d’un déploiement.

Si l’authentification est activée, vous pouvez utiliser la méthode `get_keys` pour récupérer une clé d’authentification primaire et secondaire :

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Si vous devez régénérer une clé, utilisez [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a name="request-data"></a>Données de la demande

L’API REST attend que le corps de la demande soit un document JSON avec la structure suivante :

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> La structure des données doit correspondre à ce que le script de scoring et le modèle du service attendent. Le script de scoring peut modifier les données avant de les transmettre au modèle.

Par exemple, le modèle de l’exemple [Effectuer l'apprentissage dans un bloc-notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) attend un tableau de 10 chiffres. Le script de scoring pour cet exemple crée un tableau Numpy à partir de la demande et le transmet au modèle. L’exemple suivant montre les données que ce service attend :

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
``` 

Le service web peut accepter plusieurs jeux de données dans une même demande. Il retourne un document JSON contenant un tableau de réponses.

### <a name="binary-data"></a>Données binaires

Si votre modèle accepte les données binaires, comme une image, vous devez modifier le fichier `score.py` utilisé pour votre déploiement afin d’accepter les demandes HTTP brutes. Voici un exemple de `score.py` qui accepte des données binaires et retourne les octets inversés pour les requêtes POST. Pour les requêtes GET, il retourne l’URL complète dans le corps de réponse :

```python 
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> L’espace de noms `azureml.contrib` change fréquemment car nous travaillons à améliorer le service. Par conséquent, tout ce qui est compris dans cet espace de noms doit être considéré comme une préversion et n’est pas entièrement pris en charge par Microsoft.
>
> Si vous avez besoin d’effectuer ce test sur votre environnement de développement local, vous pouvez installer les composants dans l’espace de noms à l’aide de la commande suivante :
> 
> ```shell
> pip install azureml-contrib-services
> ```

## <a name="call-the-service-c"></a>Appeler le service (C#)

Cet exemple montre comment utiliser C# pour appeler le service web créé à partir de l’exemple [Train dans bloc-notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) :

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key
            string scoringUri = "<your web service URI>";
            string authKey = "<your key>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

Les résultats retournés sont similaires au document JSON suivant :

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Appeler le service (Go)

Cet exemple montre comment utiliser Go pour appeler le service web créé à partir de l’exemple [Train dans bloc-notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) :

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key (if any) for your service
var authKey string = "<your key>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

Les résultats retournés sont similaires au document JSON suivant :

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Appeler le service (Java)

Cet exemple montre comment utiliser Java pour appeler le service web créé à partir de l’exemple [Train dans bloc-notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) :

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key
        String key = "<your key>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

Les résultats retournés sont similaires au document JSON suivant :

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Appeler le service (Python)

Cet exemple montre comment utiliser Python pour appeler le service web créé à partir de l’exemple [Train dans bloc-notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) :

```python
import requests
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key
key = '<your key>'

# Two sets of data to score, so we get two results back
data = {"data": 
            [
                [
                    0.0199132141783263, 
                    0.0506801187398187, 
                    0.104808689473925, 
                    0.0700725447072635, 
                    -0.0359677812752396, 
                    -0.0266789028311707, 
                    -0.0249926566315915, 
                    -0.00259226199818282, 
                    0.00371173823343597, 
                    0.0403433716478807
                ],
                [
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303]
            ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = { 'Content-Type':'application/json' }
# If authentication is enabled, set the authorization header
headers['Authorization']=f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers = headers)
print(resp.text)

```

Les résultats retournés sont similaires au document JSON suivant :

```JSON
[217.67978776218715, 224.78937091757172]
```
