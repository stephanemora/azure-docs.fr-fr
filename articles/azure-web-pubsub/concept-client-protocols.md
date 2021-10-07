---
title: Protocoles clients WebSocket pour Azure Web PubSub
description: Cet article présente une vue d’ensemble des protocoles clients pour Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 8f78dbcdecc552e94c3d871f610ef227a1795f8e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576257"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>Protocoles clients WebSocket pour Azure Web PubSub

Les clients se connectent à Azure Web PubSub à l’aide du protocole [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455) standard.

## <a name="service-endpoints"></a>Points de terminaison de service
Le service Web PubSub fournit deux types de points de terminaison auxquels les clients peuvent se connecter :
* `/client/hubs/{hub}`
* `/client/?hub={hub}`

`{hub}` est un paramètre obligatoire qui agit comme isolement pour différentes applications. Vous pouvez le définir soit dans le chemin d’accès, soit dans la requête.

## <a name="authorization"></a>Autorisation

Les clients se connectent au service avec un JSON Web Token (JWT). Le jeton peut être dans la chaîne de requête, sous la forme `/client/?hub={hub}&access_token={token}`, ou dans l’en-tête `Authorization`, sous la forme `Authorization: Bearer {token}`.

Voici un flux de travail d’autorisation générale :

1. Le client négocie avec votre serveur d’applications. Le serveur d’applications contient l’intergiciel d’autorisation, qui traite la demande du client et signe un JWT pour que le client puisse se connecter au service.
1. Le serveur d’applications renvoie le JWT et l’URL du service au client.
1. Le client tente de se connecter au service Web PubSub en utilisant l’URL et le JWT renvoyés par le serveur d’applications.

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>Client WebSocket simple

Un client WebSocket simple, comme son nom l’indique, est une connexion WebSocket simple. Il peut également avoir son propre sous-protocole personnalisé. 

Par exemple, dans JavaScript, vous pouvez créer un client WebSocket simple en utilisant le code suivant :

```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')
```

## <a name="the-pubsub-websocket-client"></a>Le client WebSocket PubSub

Le client WebSocket PubSub prend en charge deux sous-protocoles :
* `json.webpubsub.azure.v1`
* `protobuf.webpubsub.azure.v1`

#### <a name="the-json-subprotocol"></a>Le sous-protocole JSON

Par exemple, dans JavaScript, vous pouvez créer un client WebSocket PubSub avec un sous-protocole JSON à l’aide du code suivant :

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="the-protobuf-subprotocol"></a>Le sous-protocole protobuf

Les mémoires tampons de protocole (protobuf) désignent un protocole binaire indépendant du langage et de la plateforme qui simplifie l’envoi de données binaires. Protobuf fournit des outils permettant de générer des clients pour de nombreux langages, tels que Java, Python, Objective-C, C# et C++. [En savoir plus sur protobuf](https://developers.google.com/protocol-buffers).

Par exemple, dans JavaScript, vous pouvez créer un client WebSocket PubSub avec un sous-protocole protobuf à l’aide du code suivant :

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

Lorsque le client utilise un sous-protocole, les trames de données sortante et entrante doivent être des charges utiles JSON. Un client autorisé peut publier des messages sur d’autres clients directement par le biais du service Azure Web PubSub.

### <a name="permissions"></a>Autorisations

Comme vous l’avez probablement remarqué dans la description précédente du client WebSocket PubSub, un client ne peut publier vers d’autres clients que lorsqu’il est *autorisé* à le faire. Les autorisations d’un client peuvent être accordées au moment de la connexion ou pendant la durée de vie de la connexion.

| Rôle | Autorisation |
|---|---|
| Non spécifié | Le client peut envoyer des requêtes d’événements. |
| `webpubsub.joinLeaveGroup` | Le client peut rejoindre ou quitter n’importe quel groupe. |
| `webpubsub.sendToGroup` | Le client peut publier des messages dans n’importe quel groupe. |
| `webpubsub.joinLeaveGroup.<group>` | Le client peut rejoindre ou quitter le groupe `<group>`. |
| `webpubsub.sendToGroup.<group>` | Le client peut publier des messages dans le groupe `<group>`. |
| | |

Pour plus d’informations sur le sous-protocole JSON, consultez [Sous-protocole JSON](./reference-json-webpubsub-subprotocol.md).

Pour plus d’informations sur le sous-protocole protobuf, consultez [Sous-protocole protobuf](./reference-protobuf-webpubsub-subprotocol.md).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]
