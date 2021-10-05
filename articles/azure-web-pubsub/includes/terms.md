---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.openlocfilehash: 4fc2131e57d89a99b44a8ba345b901af5bc8890a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "126056425"
---
- **Connexion** : une connexion, également appelée client ou connexion cliente, représente une connexion WebSocket individuelle connectée au service Web PubSub. Une fois la connexion réussie, un ID de connexion unique est affecté à cette connexion par le service Web PubSub.

- **Hub** : un hub est un concept logique pour un ensemble de connexions clientes. En règle générale, vous utilisez un hub dans un seul but, par exemple un hub de *conversations* ou un hub de *notifications*. Quand une connexion cliente est établie, le client se connecte à un hub et, pendant toute sa durée de vie, il appartient à ce hub. Différentes applications peuvent partager un service Azure Web PubSub en utilisant différents noms de hubs.

- **Groupe** : un groupe est un sous-ensemble de connexions au hub. Vous pouvez ajouter une connexion cliente à un groupe, ou la supprimer du groupe, quand vous le souhaitez. Par exemple, quand un client rejoint une salle de conversation ou quand il la quitte, cette salle de conversation peut être considérée comme un groupe. Un client peut rejoindre plusieurs groupes, et un groupe peut contenir plusieurs clients.

- **Utilisateur** : les connexions à Web PubSub ne peuvent appartenir qu’à un seul utilisateur. Un utilisateur peut avoir plusieurs connexions. C’est le cas, par exemple, quand un seul utilisateur est connecté sur plusieurs appareils ou plusieurs onglets de navigateur.

- **Message** : quand le client est connecté, il peut envoyer des messages à l’application en amont, ou recevoir des messages de l’application en amont, par le biais de la connexion WebSocket.
