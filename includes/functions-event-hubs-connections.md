---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 3d1ebc666a1d4c6763b080decdd50fd44b3837dc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993306"
---
## <a name="connections"></a>Connexions

La propriété `connection` est une référence à la configuration de l’environnement qui spécifie la façon dont l’application doit se connecter à Event Hubs. Elle peut spécifier :

- Nom d’un paramètre d’application contenant une [chaîne de connexion](#connection-string)
- Nom d’un préfixe partagé pour plusieurs paramètres d’application, définissant ensemble une [connexion basée sur l’identité](#identity-based-connections).

Si la valeur configurée est à la fois une correspondance exacte pour un paramètre unique et une correspondance de préfixe pour d’autres paramètres, la correspondance exacte est utilisée.

### <a name="connection-string"></a>Chaîne de connexion

Obtenez cette chaîne de connexion en cliquant sur le bouton **Informations de connexion** pour [l’espace de noms](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), pas sur l’Event Hub lui-même. La chaîne de connexion doit correspondre à un espace de noms Event Hubs, pas au Event Hub lui-même.

Quand la chaîne de connexion est utilisée pour les déclencheurs, elle doit disposer au moins des autorisation d’accès en lecture pour activer la fonction. Quand elle est utilisée pour les liaisons de sortie, la chaîne de connexion doit disposer des autorisations « Envoyer » pour envoyer des messages au flux d’événements.

Cette chaîne de connexion doit être stockée dans un paramètre d’application dont le nom correspond à la valeur spécifiée par la propriété `connection` de la configuration de liaison.

### <a name="identity-based-connections"></a>Connexions basées sur l’identité

Si vous utilisez la [version 5.x ou une version ultérieure de l’extension](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher), au lieu d’utiliser une chaîne de connexion avec un secret, l’application peut utiliser une [identité Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md). Pour ce faire, vous devez définir les paramètres sous un préfixe commun qui correspond à la propriété `connection` dans le déclencheur et la configuration de liaison.

Dans ce mode, l’extension nécessite les propriétés suivantes :

| Propriété                  | Modèle de variable d’environnement                       | Description                                | Valeur d'exemple                                        |
|---------------------------|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| Espace de noms complet | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | Espace de noms complet Event Hubs. | <event_hubs_namespace>.servicebus.windows.net |

Des propriétés supplémentaires peuvent être définies pour personnaliser la connexion. Consultez [Propriétés communes pour les connexions basées sur l’identité](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-event-hubs-permissions](./functions-event-hubs-permissions.md)]
