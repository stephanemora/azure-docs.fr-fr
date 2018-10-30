---
title: 'Démarrage rapide : Mettre à jour une base de connaissances - REST, Python - QnA Maker'
description: Ce démarrage rapide basé sur REST vous aide à mettre à jour votre exemple de base de connaissances QnA Maker par programmation. La définition JSON que vous utilisez pour mettre à jour une base de connaissances vous permet d’ajouter, de modifier ou de supprimer des paires de questions/réponses.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: 35ef0222de6093e7bae3b41377db2ea3fd3c3d75
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645912"
---
# <a name="quickstart-update-a-knowledge-base-in-qna-maker-using-python"></a>Démarrage rapide : Mettre à jour une base de connaissances dans QnA Maker à l’aide de Python

Ce démarrage rapide vous aide à mettre à jour une base de connaissances QnA Maker existante par programmation.  Ce code JSON vous permet de mettre à jour une base de connaissances en ajoutant de nouvelles sources de données, en modifiant ou supprimant des sources de données.

Cette API correspond aux actions de modification, puis d’utilisation du bouton **Save and train** (Enregistrer et effectuer l’apprentissage) dans le portail QnA Maker.

Ce démarrage rapide fait appel aux API QnA Maker :
* [Mettre à jour](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) : le modèle de la base de connaissances est défini dans le code JSON envoyé dans le corps de la requête d’API. 
* [Obtenir les détails de l’opération](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

Si vous n’avez pas encore de base de connaissances, vous pouvez créer un exemple à utiliser pour ce démarrage rapide : [Créer une base de connaissances](create-new-kb-python.md).

1. Créez un projet Python dans votre IDE favori.
1. Ajoutez le code ci-dessous.
1. Remplacez la valeur `subscriptionKey` par une clé d’abonnement valide.
1. Remplacez la valeur `kb` par un ID de base de connaissances valide. Vous trouverez cette valeur en accédant à l’une de vos [bases de connaissances QnA Maker](https://www.qnamaker.ai/Home/MyServices). Sélectionnez la base de connaissances que vous souhaitez mettre à jour. Une fois sur cette page, recherchez « kdid = » dans l’URL, comme indiqué ci-dessous. Utilisez sa valeur pour votre exemple de code.

    ![ID de base de connaissances QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

1. Exécutez le programme.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ADD KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ADD ID HERE';

# Represents the various elements used to create HTTP request path
# for QnA Maker operations.
host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

'''
Formats and indents JSON for display.
:param content: The JSON to format and indent.
:type: string
:return: Formatted and indented JSON.
:rtype: string
'''
def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

'''
Sends a PATCH HTTP request.
:param path: The URL path of your request.
:param content: The contents of your PATCH request.
:type: string
:return: URL with status of PATCH request in updating the kb, actual response.
:rtype: string, string
'''
def update_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PATCH", path, content, headers)
    response = conn.getresponse ()
    return response.getheader('Location'), response.read ()

'''
Gets the status of the specified QnA Maker operation.
:param path: The URL of the request.
:type: string
:return: Header from retrying of the request (if retry is needed), response of the retry.
:rtype: string, string
'''
def check_status (path):
    print('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, None, headers)
    response = conn.getresponse ()
    # If the operation is not finished, /operations returns an HTTP header named
    # 'Retry-After' with the number of seconds to wait before querying the operation again.
    return response.getheader('Retry-After'), response.read ()

'''
Dictionary that holds the knowledge base.
Modifications to the knowledge base are made here, using 'update', 'delete' and so on.
'''
req = {
    'add': {
        'qnaList': [
            {
              'id': 1,
              'answer': 'You can change the default message if you use the QnAMakerDialog. '
                      + 'See this for details:  https://docs.botframework.com/en-us'
                      + '/azure-bot-service/templates/qnamaker/#navtitle',
              'source': 'Custom Editorial',
              'questions': [
                  'How can I change the default message from QnA Maker?'
              ],
              'metadata': []
            }
        ],
        'urls': []
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

# Builds the path URL.
path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
# Retrieve the operation ID to check status, and JSON result.
operation, result = update_kb(path, content)
# Print request response in JSON with presentable formatting.
print(pretty_print(result))

'''
Iteratively gets the operation state, updating the knowledge base.
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
        time.sleep (int(wait))
    else:
        done = True # request has been processed, if successful, knowledge base is updated
```

## <a name="understand-what-qna-maker-returns"></a>Comprendre ce que retourne QnA Maker

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant. Vos résultats peuvent différer légèrement. Si l’appel final retourne un état « Réussi », cela signifie que votre base de connaissances a été mise à jour avec succès. Pour résoudre les problèmes, reportez-vous aux codes de réponse [Mettre à jour la base de connaissances](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) de l’API QnA Maker.

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917rt4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917rt4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

Une fois votre base de connaissances mise à jour, vous pouvez l’afficher sur votre portail QnA Maker, à la page [Mes bases de connaissances](https://www.qnamaker.ai/Home/MyServices). Notez qu’une nouvelle paire QnA a été ajoutée et que le nom de votre base de données est maintenant « Nouveau nom de la base de connaissances ».

Pour modifier d’autres éléments de votre base de connaissances, reportez-vous au [schéma JSON](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) QnA Maker et modifiez la chaîne `req`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
