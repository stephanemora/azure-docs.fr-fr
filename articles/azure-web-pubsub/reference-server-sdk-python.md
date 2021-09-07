---
title: Référence – Kit de développement logiciel (SDK) de serveur Python pour le service Azure Web PubSub
description: La référence décrit le kit de développement logiciel (SDK) de serveur Python pour le service Azure Web PubSub
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: c394258a49e73055f6848eed87dd15e75bd1c069
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116244"
---
# <a name="python-server-sdk-for-azure-web-pubsub-service"></a>Kit de développement logiciel (SDK) de serveur Python pour le service Azure Web PubSub

Utilisez la bibliothèque pour :

- Envoyer des messages à des hubs et des groupes.
- Envoyer des messages à des utilisateurs et des connexions particuliers.
- Organiser des utilisateurs et des connexions en groupes.
- Fermer les connexions
- Accorder, révoquer ou vérifier des autorisations pour une connexion existante

[Code source](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [Package (Pypi)][package] | [Documentation de référence de l’API](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice) | [Documentation du produit][webpubsubservice_docs] |
[Exemples][samples_ref]

## <a name="getting-started"></a>Prise en main

### <a name="installations-the-package"></a>Installation du package

```bash
python -m pip install azure-messaging-webpubsubservice
```

#### <a name="prerequisites"></a>Prérequis

- Python 2.7 ou 3.6 ou version ultérieure est requis pour utiliser ce package.
- Pour suivre ce didacticiel, vous avez besoin d’un [abonnement Azure][azure_sub] et d’une [instance de service Azure Search][webpubsubservice_docs].
- Une instance de service Azure Web PubSub existante.

### <a name="authenticating-the-client"></a>Authentification du client

Pour interagir avec le service Azure Web PubSub, vous devez créer une instance de la classe [WebPubSubServiceClient][webpubsubservice_client_class]. Pour vous authentifier auprès du service, vous devez passer une instance AzureKeyCredential avec un point de terminaison et une clé d’accès. Le point de terminaison et la clé d’accès se trouvent sur le portail Azure.

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> client
<WebPubSubServiceClient endpoint:'<endpoint>'>
```

## <a name="examples"></a>Exemples

### <a name="sending-a-request"></a>Envoi d’une requête

```python
>>> from azure.messaging.webpubsubservice import WebPubSubServiceClient
>>> from azure.core.credentials import AzureKeyCredential
>>> from azure.messaging.webpubsubservice.rest import build_send_to_all_request
>>> client = WebPubSubServiceClient(endpoint='<endpoint>', credential=AzureKeyCredential('somesecret'))
>>> request = build_send_to_all_request('default', json={ 'Hello':  'webpubsub!' })
>>> request
<HttpRequest [POST], url: '/api/hubs/default/:send?api-version=2020-10-01'>
>>> response = client.send_request(request)
>>> response
<RequestsTransportResponse: 202 Accepted>
>>> response.status_code 
202
>>> with open('file.json', 'r') as f:
>>>    request = build_send_to_all_request('ahub', content=f, content_type='application/json')
>>>    response = client.send_request(request)
>>> print(response)
<RequestsTransportResponse: 202 Accepted>
```

## <a name="key-concepts"></a>Concepts clés

[!INCLUDE [Terms](includes/terms.md)]

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="logging"></a>Journalisation

Ce kit de développement logiciel (SDK) utilise la bibliothèque de journalisation standard Python.
Vous pouvez configurer la journalisation des informations de débogage de l’impression dans StdOut ou tout emplacement de votre choix.

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Les détails de la requête et de la réponse HTTP sont imprimés dans StdOut avec cette configuration de journalisation.

[webpubsubservice_docs]: https://aka.ms/awps/doc
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[webpubsubservice_client_class]: https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/webpubsub/azure-messaging-webpubsubservice/azure/messaging/webpubsubservice/__init__.py
[package]: https://pypi.org/project/azure-messaging-webpubsubservice/
[default_cred_ref]: https://aka.ms/azsdk-python-identity-default-cred-ref
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]
