---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.openlocfilehash: 2356b3af08577063326da7dbb3cf8141d52cea07
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597195"
---
- **Connexion** : une connexion, également appelée client ou connexion cliente, représente une connexion WebSocket individuelle connectée au service Web PubSub. Une fois la connexion réussie, un ID de connexion unique est affecté à cette connexion par le service Web PubSub.

- **Hub** : le hub est un concept logique pour un ensemble de connexions clientes. En règle générale, vous utilisez un hub dans un seul but, par exemple un hub de *conversations* ou un hub de *notifications*. Quand une connexion cliente est établie, le client se connecte à un hub et, pendant toute sa durée de vie, il appartient à ce hub. Différentes applications peuvent partager un service Azure Web PubSub en utilisant différents noms de hubs.

- **Groupe** : le groupe est un sous-ensemble de connexions au hub. Le groupe étant relativement léger, vous pouvez ajouter une connexion cliente à un groupe, ou la supprimer du groupe, quand vous le souhaitez. Par exemple, un client rejoint une salle de conversation qu’il quitte par la suite. Cette salle de conversation peut être un groupe. Un client peut rejoindre plusieurs groupes, et un groupe peut contenir plusieurs clients.

- **Utilisateur** : les connexions à Web PubSub ne peuvent appartenir qu’à un seul utilisateur. Un utilisateur peut avoir plusieurs connexions. C’est le cas, par exemple, quand un seul utilisateur est connecté sur plusieurs appareils ou plusieurs onglets de navigateur.

- **Message** : quand le client est connecté, il peut envoyer des messages à la partie amont, ou recevoir des messages de la partie amont à partir de la connexion WebSocket.
