---
title: API REST (V4) - Python - QnA Maker
titleSuffix: Azure Cognitive Services
description: Obtenez des informations et des exemples de code Python pour vous aider à commencer rapidement à utiliser l’API de traduction de texte Translator Text Microsoft dans Microsoft Cognitive Services sur Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 0386482d7b46eb93c867b019abbf02231de1ddfe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051977"
---
# <a name="how-to-use-the-qna-maker-rest-api-with-python"></a>Guide pratique pour utiliser l’API REST QnA Maker avec Python
<a name="HOLTop"></a>

Cet article explique comment utiliser l’[API QnA Maker Microsoft](../Overview/overview.md)  avec Python pour effectuer les opérations suivantes.

- [Créer une base de connaissances](#Create)
- [Mettre à jour une base de connaissances](#Update)
- [Obtenir l’état d’une demande pour créer ou mettre à jour une base de connaissances](#Status)
- [Publier une base de connaissances](#Publish)
- [Remplacer le contenu d’une base de connaissances](#Replace)
- [Télécharger le contenu d’une base de connaissances](#GetQnA)
- [Obtenir des réponses à une question à l’aide d’une base de connaissances](#GetAnswers)
- [Obtenir des informations sur une base de connaissances](#GetKB)
- [Obtenir des informations sur toutes les bases de connaissances appartenant à l’utilisateur spécifié](#GetKBsByUser)
- [Supprimer une base de connaissances](#Delete)
- [Obtenir les clés du point de terminaison actuel](#GetKeys)
- [Régénérer les clés du point de terminaison actuel](#PutKeys)
- [Obtenir l'ensemble actuel d'altérations de mots qui ne respectent pas la casse](#GetAlterations)
- [Remplacer l'ensemble actuel d'altérations de mots qui ne respectent pas la casse](#PutAlterations)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour exécuter ce code, vous devez disposer de [Python 3.x](https://www.python.org/downloads/).

Vous devez avoir un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec l’**API QnA Maker Microsoft**. Vous aurez besoin d’une clé d’abonnement payant que vous pouvez trouver sur votre [tableau de bord Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

<a name="Create"></a>

## <a name="create-knowledge-base"></a>Créer une base de connaissances

Le code suivant crée une base de connaissances à l’aide de la méthode [Create](https://go.microsoft.com/fwlink/?linkid=2092179).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/create'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def create_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()
# /knowledgebases/create returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read ()

def check_status (path):
    print ('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, None, headers)
    response = conn.getresponse ()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read ()

req = {
  "name": "QnA Maker FAQ",
  "qnaList": [
    {
      "id": 0,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
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
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "files": []
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
operation, result = create_kb (path, content)
print (pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status (path)
    print (pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print ('Waiting ' + wait + ' seconds...')
        time.sleep (int(wait))
    else:
        done = True
```

**Créer une réponse de la base de connaissances**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
```

[Revenir en haut](#HOLTop)

<a name="Update"></a>

## <a name="update-knowledge-base"></a>Mettre à jour une base de connaissances

Le code suivant met à jour une base de connaissances à l’aide de la méthode [Update](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def update_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PATCH", path, content, headers)
    response = conn.getresponse ()
# PATCH /knowledgebases returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read ()

def check_status (path):
    print ('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, None, headers)
    response = conn.getresponse ()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read ()

req = {
  'add': {
    'qnaList': [
      {
        'id': 1,
        'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/azure-bot-service/templates/qnamaker/#navtitle',
        'source': 'Custom Editorial',
        'questions': [
          'How can I change the default message from QnA Maker?'
        ],
        'metadata': []
      }
    ],
    'urls': [
      'https://docs.microsoft.com/azure/cognitive-services/Emotion/FAQ'
    ]
  },
  'update' : {
    'name' : 'New KB Name'
  },
  'delete': {
    'ids': [
      0
    ]
  }
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
operation, result = update_kb (path, content)
print (pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status (path)
    print (pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print ('Waiting ' + wait + ' seconds...')
        time.sleep (int(wait))
    else:
        done = True
```

**Mettre à jour une réponse de la base de connaissances**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

[Revenir en haut](#HOLTop)

<a name="Status"></a>

## <a name="get-request-status"></a>Obtenir l’état d’une demande

Vous pouvez appeler la méthode [Operation](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails) pour vérifier l’état d’une demande de création ou de mise à jour d’une base de connaissances. Pour voir comment cette méthode est utilisée, examinez l’exemple de code pour la méthode [Create](#Create) ou [Update](#Update).

[Revenir en haut](#HOLTop)

<a name="Publish"></a>

## <a name="publish-knowledge-base"></a>Publier une base de connaissances

Le code suivant publie une base de connaissances en utilisant la méthode [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def publish_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + kb
result = publish_kb (path, '')
print (pretty_print(result))
```

**Publier une réponse de la base de connaissances**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "result": "Success."
}
```

[Revenir en haut](#HOLTop)

<a name="Replace"></a>

## <a name="replace-knowledge-base"></a>Remplacer une base de connaissances

Le code suivant remplace le contenu de la base de connaissances spécifiée à l’aide de la méthode [Replace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def replace_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PUT", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

req = {
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my Knowledge Base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ]
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
result = replace_kb (path, content)
print (pretty_print(result))
```

**Remplacer une réponse de la base de connaissances**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
    "result": "Success."
}
```

[Revenir en haut](#HOLTop)

<a name="GetQnA"></a>

## <a name="download-the-contents-of-a-knowledge-base"></a>Télécharger le contenu d’une base de connaissances

Le code suivant télécharge le contenu de la base de connaissances spécifiée à l’aide de la méthode [Download knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

# Replace this with "test" or "prod".
env = 'test';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/{0}/{1}/qna/'.format(kb, env);

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_qna (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_qna (path)
print (pretty_print(result))
```

**Télécharger une réponse de la base de connaissances**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "qnaDocuments": [
    {
      "id": 1,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
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
    },
    {
      "id": 2,
      "answer": "QnA Maker provides an FAQ data source that you can query from your bot or application. Although developers will find this useful, content owners will especially benefit from this tool. QnA Maker is a completely no-code way of managing the content that powers your bot or application.",
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "questions": [
        "Who is the target audience for the QnA Maker tool?"
      ],
      "metadata": []
    },
...
  ]
}
```

[Revenir en haut](#HOLTop)

<a name="GetAnswers"></a>

## <a name="get-answers-to-a-question-using-a-knowledge-base"></a>Obtenir des réponses à une question à l’aide d’une base de connaissances

Le code suivant obtient les réponses à une question en utilisant la base de connaissances spécifiée, à l’aide de la méthode **Generate answers**.

1. Créez un projet Python dans votre IDE favori.
1. Ajoutez le code ci-dessous.
1. Remplacez la valeur `host` par le nom de site web de votre abonnement QnA Maker. Pour plus d’informations, voir [Créer un service QnA Maker](../How-To/set-up-qnamaker-service-azure.md).
1. Remplacez la valeur `endpoint_key` par une clé de point de terminaison valide pour votre abonnement. Notez que celle-ci diffère de votre clé d’abonnement. Vous pouvez obtenir vos clés de point de terminaison à l’aide de la méthode [Get endpoint keys](#GetKeys).
1. Remplacez la valeur `kb` par l’ID de la base de connaissances que vous souhaitez interroger pour obtenir des réponses. Notez que cette base de connaissances doit avoir été publiée à l’aide de la méthode [Publish](#Publish).
1. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# NOTE: Replace this with a valid host name.
host = "ENTER HOST HERE"

# NOTE: Replace this with a valid endpoint key.
# This is not your subscription key.
# To get your endpoint keys, call the GET /endpointkeys method.
endpoint_key = "ENTER KEY HERE"

# NOTE: Replace this with a valid knowledge base ID.
# Make sure you have published the knowledge base with the
# POST /knowledgebases/{knowledge base ID} method.
kb = "ENTER KB ID HERE"

method = "/qnamaker/knowledgebases/" + kb + "/generateAnswer"

question = {
    'question': 'Is the QnA Maker Service free?',
    'top': 3
}

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_answers (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Authorization': 'EndpointKey ' + endpoint_key,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()
    return response.read ()

# Convert the request to a string.
content = json.dumps(question)
result = get_answers (method, content)
print (pretty_print(result))
```

**Obtenir une réponse à une question**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "answers": [
    {
      "questions": [
        "Can I use BitLocker with the Volume Shadow Copy Service?"
      ],
      "answer": "Yes. However, shadow copies made prior to enabling BitLocker will be automatically deleted when BitLocker is enabled on software-encrypted drives. If you are using a hardware encrypted drive, the shadow copies are retained.",
      "score": 17.3,
      "id": 62,
      "source": "https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-frequently-asked-questions",
      "metadata": []
    },
...
  ]
}
```

[Revenir en haut](#HOLTop)

<a name="GetKB"></a>

## <a name="get-information-about-a-knowledge-base"></a>Obtenir des informations sur une base de connaissances

Le code suivant obtient des informations sur la base de connaissances spécifiée en utilisant la méthode [Get knowledge base details](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_kb (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method + kb
result = get_kb (path)
print (pretty_print(result))
```

**Obtenir une réponse détaillée de la base de connaissances**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
  "hostName": "https://qna-docs.azurewebsites.net",
  "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
  "lastChangedTimestamp": "2018-04-12T22:58:01Z",
  "name": "QnA Maker FAQ",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

[Revenir en haut](#HOLTop)

<a name="GetKBsByUser"></a>

## <a name="get-all-knowledge-bases-for-a-user"></a>Obtenir toutes les bases de connaissances pour un utilisateur

Le code suivant obtient des informations concernant toutes les bases de connaissances pour un utilisateur spécifié, à l’aide de la méthode [Get knowledge bases for user](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/listall).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_kbs (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_kbs (path)
print (pretty_print(result))
```

**Obtenir des bases de connaissances pour répondre à un utilisateur**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "knowledgebases": [
    {
      "id": "081c32a7-bd05-4982-9d74-07ac736df0ac",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T11:51:58Z",
      "lastChangedTimestamp": "2018-04-12T11:51:58Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [],
      "sources": []
    },
    {
      "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
      "lastChangedTimestamp": "2018-04-12T22:58:01Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [
        "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
      ],
      "sources": [
        "Custom Editorial"
      ]
    },
...
  ]
}
Press any key to continue.
```

[Revenir en haut](#HOLTop)

<a name="Delete"></a>

## <a name="delete-a-knowledge-base"></a>Supprimer une base de connaissances

Le code suivant supprime la base de connaissances spécifiée, à l’aide de la méthode [Delete knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def delete_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("DELETE", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + kb
result = delete_kb (path, '')
print (pretty_print(result))
```

**Supprimer une réponse de la base de connaissances**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "result": "Success."
}
```

[Revenir en haut](#HOLTop)

<a name="GetKeys"></a>

## <a name="get-endpoint-keys"></a>Obtenir les clés d’un point de terminaison

Le code suivant obtient les clés du point de terminaison actuel à l’aide de la méthode [Get endpoint keys](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_keys (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_keys (path)
print (pretty_print(result))
```

**Obtenir une réponse concernant les clés d’un point de terminaison**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Revenir en haut](#HOLTop)

<a name="PutKeys"></a>

## <a name="refresh-endpoint-keys"></a>Actualiser les clés d’un point de terminaison

Le code suivant régénère les clés du point de terminaison actuel à l’aide de la méthode [Refresh endpoint keys](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/refreshkeys).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with "PrimaryKey" or "SecondaryKey."
key_type = "PrimaryKey";

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def refresh_keys (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PATCH", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + key_type
result = refresh_keys (path, '')
print (pretty_print(result))
```

**Actualiser une réponse concernant les clés d’un point de terminaison**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Revenir en haut](#HOLTop)

<a name="GetAlterations"></a>

## <a name="get-word-alterations"></a>Obtenir les altérations d’un mot

Le code suivant obtient les altérations du mot actif en utilisant la méthode [Download alterations](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/get).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_alterations (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_alterations (path)
print (pretty_print(result))
```

**Obtenir une réponse concernant les altérations d’un mot**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "wordAlterations": [
    {
      "alterations": [
        "botframework",
        "bot frame work"
      ]
    }
  ]
}
```

[Revenir en haut](#HOLTop)

<a name="PutAlterations"></a>

## <a name="replace-word-alterations"></a>Remplacer les altérations d’un mot

Le code suivant remplace les altérations du mot actuel à l’aide de la méthode [Replace alterations](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace).

1. Créez un projet Python dans votre IDE favori.
2. Ajoutez le code ci-dessous.
3. Remplacez la valeur `key` par une clé d’accès valide pour votre abonnement.
4. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def put_alterations (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PUT", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

req = {
  'wordAlterations': [
    {
      'alterations': [
        'botframework',
        'bot frame work'
      ]
    }
  ]
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
result = put_alterations (path, content)
print (pretty_print(result))
```

**Remplacer une réponse concernant les altérations d’un mot**

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "result": "Success."
}
```

[Revenir en haut](#HOLTop)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)

## <a name="see-also"></a>Voir aussi 

[Vue d’ensemble de QnA Maker](../Overview/overview.md)
