---
title: Protocoles clients WebSocket pour Azure Web PubSub
description: Vue d’ensemble des protocoles clients pour Azure Web PubSub
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 06f4b3050b90f27d1f30ba59b26eeed4d76c6bc8
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122535303"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>Protocoles clients WebSocket pour Azure Web PubSub

Les clients se connectent à Azure Web PubSub à l’aide du protocole [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455) standard.

## <a name="service-endpoint"></a>Point de terminaison de service
Le service fournit deux types de points de terminaison auxquels les clients peuvent se connecter :
1. `/client/hubs/{hub}`
2. `/client/?hub={hub}`

`{hub}` est un paramètre obligatoire qui agit comme isolement pour différentes applications. Il peut être défini dans le chemin d’accès ou dans la requête.

## <a name="auth"></a>Auth
1. Le client se connecte au service avec le jeton JWT

### <a name="1-the-client-connects-using-the-jwt-token"></a>1. Le client se connecte à l’aide du jeton JWT

Le jeton JWT peut être dans une chaîne de requête `/client/?hub={hub}&access_token={token}` ou dans l’en-tête `Authorization` : `Authorization: Bearer {token}`.

Workflow général :
1. Le client négocie avec votre serveur d’applications. Le serveur d’application dispose d’un middleware Auth pour traiter la requête du client et signer un jeton JWT pour que le client puisse se connecter au service.
2. Le serveur d’applications renvoie le jeton JWT et l’URL du service au client
3. Le client tente de se connecter au service Web PubSub à l’aide de l’URL et du jeton JWT renvoyés par le serveur d’applications

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>Client WebSocket simple
Un client WebSocket simple, comme son nom l’indique, est une connexion WebSocket simple. Il peut également avoir son sous-protocole personnalisé. 

Par exemple, dans JS, un client WebSocket simple peut être créé à l’aide des éléments suivants :
```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')

```

## <a name="the-pubsub-websocket-client"></a>Le client WebSocket PubSub

Le client WebSocket PubSub se connecte au sous-protocole `json.webpubsub.azure.v1`.

Par exemple, dans JS, un client WebSocket PubSub peut être créé à l’aide des éléments suivants :
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

Lorsque le client utilise ce sous-protocole, la trame de données sortante et la trame de données entrante doivent être des charges utiles JSON. Un client authentifié peut publier des messages sur d’autres clients directement par le biais du service Azure Web PubSub.

### <a name="permissions"></a>Autorisations

Comme vous l’avez peut-être remarqué lorsque nous avons décrit les clients WebSocket PubSub, un client ne peut publier vers d’autres clients que s’il y est *autorisé*. L’autorisation du client peut être accordée lorsqu’il est connecté ou pendant la durée de vie de la connexion.

| Rôle | Autorisation |
|---|---|
| Non spécifié | Le client peut envoyer des requêtes d’événements.
| `webpubsub.joinLeaveGroup` | Le client peut rejoindre/quitter n’importe quel groupe.
| `webpubsub.sendToGroup` | Le client peut publier des messages dans n’importe quel groupe.
| `webpubsub.joinLeaveGroup.<group>` | Le client peut rejoindre/quitter le groupe `<group>`.
| `webpubsub.sendToGroup.<group>` | Le client peut publier des messages dans le groupe `<group>`.

Les détails du sous-protocole sont décrits dans [sous-protocole JSON](./reference-json-webpubsub-subprotocol.md).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]
