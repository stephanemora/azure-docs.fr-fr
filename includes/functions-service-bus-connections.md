---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 288539542c5b811206bd3e128dbceca317b2a27a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130137855"
---
## <a name="connections"></a>Connexions

La propriété `connection` est une référence à la configuration de l’environnement qui spécifie la façon dont l’application doit se connecter au Service Bus. Elle peut spécifier :

- Nom d’un paramètre d’application contenant une [chaîne de connexion](#connection-string)
- Nom d’un préfixe partagé pour plusieurs paramètres d’application, définissant ensemble une [connexion basée sur l’identité](#identity-based-connections).

Si la valeur configurée est à la fois une correspondance exacte pour un paramètre unique et une correspondance de préfixe pour d’autres paramètres, la correspondance exacte est utilisée.

### <a name="connection-string"></a>Chaîne de connexion

Pour obtenir une chaîne de connexion, suivez les étapes indiquées à la section [Obtenir les informations d’identification de gestion](../articles/service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). La chaîne de connexion doit être destinée à un espace de noms Service Bus, et non limitée à une file d’attente ou une rubrique spécifique.

Cette chaîne de connexion doit être stockée dans un paramètre d’application dont le nom correspond à la valeur spécifiée par la propriété `connection` de la configuration de liaison.

Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom. Par exemple, si vous définissez `connection` sur « MyServiceBus », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyServiceBus ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion Service Bus par défaut dans le paramètre d’application nommé « AzureWebJobsServiceBus ».

### <a name="identity-based-connections"></a>Connexions basées sur l’identité

Si vous utilisez la [version 5.x ou une version ultérieure de l’extension](../articles/azure-functions/functions-bindings-service-bus.md#service-bus-extension-5x-and-higher), au lieu d’utiliser une chaîne de connexion avec un secret, l’application peut utiliser une [identité Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md). Pour ce faire, vous devez définir les paramètres sous un préfixe commun qui correspond à la propriété `connection` dans le déclencheur et la configuration de liaison.

Dans ce mode, l’extension nécessite les propriétés suivantes :

| Propriété                  | Modèle de variable d’environnement                       | Description                                | Valeur d'exemple |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| Espace de noms complet | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | Espace de noms complet Service Bus. | <service_bus_namespace>.servicebus.windows.net  |

Des propriétés supplémentaires peuvent être définies pour personnaliser la connexion. Consultez [Propriétés communes pour les connexions basées sur l’identité](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-service-bus-permissions](./functions-service-bus-permissions.md)]