---
title: 'Démarrage rapide : API et Python - Créer une base de connaissances - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ce démarrage rapide vous guide dans la création d’un exemple de base de connaissances QnA Maker, par programmation, qui apparaîtra dans le tableau de bord Azure de votre compte d’API Cognitive Services.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: diberry
ms.openlocfilehash: 9a3b801058a2614b9b484a3ebf414a9225c5371e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033292"
---
# <a name="create-a-new-knowledge-base-in-python"></a>Créer une base de connaissances dans Python

Ce démarrage rapide vous guide dans la création d’un exemple de base de connaissances QnA Maker, par programmation, qui apparaîtra dans le tableau de bord Azure de votre compte d’API Cognitive Services.

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

Deux exemples d’URL de FAQ sont fournis ci-dessous (dans l’URL d’élément de dictionnaire **req**). QnA Maker extrait automatiquement les questions et les réponses à partir du contenu semi-structuré, telles que les FAQ, comme expliqué plus en détail dans ce document sur les [sources de données](../Concepts/data-sources-supported.md). Vous pouvez également utiliser vos propres URL de FAQ dans ce démarrage rapide.

## <a name="prerequisites"></a>Prérequis

Pour exécuter ce code, vous devez disposer de [Python 3.x](https://www.python.org/downloads/).

Vous devez avoir un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec **QnA Maker** sélectionné comme ressource. Vous aurez besoin d’une clé d’abonnement payant que vous pouvez trouver sur votre [tableau de bord Azure](https://ms.portal.azure.com/). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord. N’importe quelle clé fonctionnera pour ce démarrage rapide.

![Clé de service du tableau de bord Azure](../media/sub-key.png)

Pour obtenir de l’aide supplémentaire avec Visual Studio et Python : [Utiliser Python dans Visual Studio sur Windows](https://docs.microsoft.com/en-us/visualstudio/python/overview-of-python-tools-for-visual-studio).

## <a name="create-knowledge-base"></a>Créer une base de connaissances

Le code suivant crée une base de connaissances à l’aide de la méthode [Create](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `subscriptionKey` par votre clé d’abonnement valide.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'YOUR SUBSCRIPTION KEY HERE'

# Represents the various elements used to create HTTP request path
# for QnA Maker operations.
host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/create'

'''
Formats and indents JSON for display.
:param content: The JSON to format and indent.
:type: string
:return: A string containing formatted and indented JSON.
:rtype: string
'''
def pretty_print(content):
  # Note: We convert content to and from an object so we can pretty-print it.
  return json.dumps(json.loads(content), indent=4)

'''
Sends the POST request to create the knowledge base.
:param path: The URL path being called.
:type: string
:param content: The contents of your POST.
:type: string
:return: A header that creates the knowledge base, the JSON response
:rtype: string, string
'''
def create_kb(path, content):
  print('Calling ' + host + path + '.')
  headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json',
    'Content-Length': len (content)
  }
  conn = http.client.HTTPSConnection(host)
  conn.request ("POST", path, content, headers)
  response = conn.getresponse ()
  # /knowledgebases/create returns an HTTP header named Location that contains a URL
  # to check the status of the operation in creating the knowledge base.
  return response.getheader('Location'), response.read ()

'''
Checks the status of the request to create the knowledge base.
:param path: The URL path being checked
:type: string
:return: The header Retry-After if request is not finished, the JSON response
:rtype: string, string
'''
def check_status(path):
  print('Calling ' + host + path + '.')
  headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
  conn = http.client.HTTPSConnection(host)
  conn.request("GET", path, None, headers)
  response = conn.getresponse ()
  # If the operation is not finished, /operations returns an HTTP header named Retry-After
  # that contains the number of seconds to wait before we query the operation again.
  return response.getheader('Retry-After'), response.read ()

'''
Dictionary that holds the knowledge base.
The data source includes a QnA pair with metadata, the URL for the
QnA Maker FAQ article, and the URL for the Azure Bot Service FAQ article.
'''
req = {
  "name": "QnA Maker FAQ",
  "qnaList": [
    {
      "id": 0,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    }
  ],
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/en-us/bot-framework/resources-bot-framework-faq"
  ],
  "files": []
}

# Builds the path URL.
path = service + method
# Convert the request to a string.
content = json.dumps(req)
# Retrieve the operation ID to check status, and JSON result
operation, result = create_kb(path, content)
# Print request response in JSON with presentable formatting
print(pretty_print(result))

'''
Iteratively gets the operation state, creating the knowledge base.
Once state is no longer "Running" or "NotStarted", the loop ends.
'''
done = False
while False == done:
  path = service + operation
  # Gets the status of the operation.
  wait, status = check_status(path)
  # Print status checks in JSON with presentable formatting
  print(pretty_print(status))

  # Convert the JSON response into an object and get the value of the operationState field.
  state = json.loads(status)['operationState']
  # If the operation isn't finished, wait and query again.
  if state == 'Running' or state == 'NotStarted':
    print('Waiting ' + wait + ' seconds...')
    time.sleep(int(wait))
  else:
    done = True # request has been processed, if successful, knowledge base is created
```

## <a name="understand-what-qna-maker-returns"></a>Comprendre ce que retourne QnA Maker

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant. Vos résultats peuvent différer légèrement. Si l’appel final retourne un état « Réussi », cela signifie que votre base de connaissances a été créée avec succès. Pour résoudre les problèmes, reportez-vous à [Détails de l’opération Get](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) de l’API QnA Maker.

```json
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/knowledgebases/create.
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917tb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bt4ebha1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Waiting 30 seconds...
Calling https://westus.api.cognitive.microsoft.com/qnamaker/v4.0/operations/d9d40918-01bd-49f4-88b4-129fbc434c94.
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bt4ebfa1aae40fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
Press any key to continue.
```

Une fois votre base de connaissances créée, vous pouvez l’afficher dans votre portail QnA Maker, sur la page [Mes bases de connaissances](https://www.qnamaker.ai/Home/MyServices). Sélectionnez le nom de votre base de connaissances, par exemple QnA Maker FAQ, pour l’afficher.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)