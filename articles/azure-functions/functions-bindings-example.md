---
title: Exemple de déclencheur et la liaison de fonctions Azure
description: Apprenez à configurer les liaisons de fonction Azure
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 03/04/2019
ms.author: v-junlch
ms.openlocfilehash: 6d5f9b171a4efc5e52d281655de143ac9d40d437
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437886"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Exemple de déclencheur et la liaison de fonctions Azure

Cet article montre comment configurer un [déclencheur et les liaisons](./functions-triggers-bindings.md) dans une fonction Azure.

Imaginons que vous souhaitiez écrire une nouvelle ligne dans Stockage Table Azure à chaque fois qu’un nouveau message s’affiche dans Stockage File d’attente Azure. Ce scénario peut être implémenté à l’aide d’un déclencheur Stockage de file d’attente Azure et d’une liaison de sortie Stockage Table. 

Voici un fichier *function.json* pour ce scénario. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Le premier élément du tableau `bindings` est le déclencheur Stockage de file d’attente. Les propriétés `type` et `direction` identifient le déclencheur. La propriété `name` identifie le paramètre de fonction qui reçoit le contenu du message de la file d’attente. Le nom de la file d’attente à surveiller se trouve dans `queueName`, et la chaîne de connexion dans le paramètre d’application identifié par `connection`.

Le deuxième élément du tableau `bindings` est la liaison de sortie de Stockage Table Azure. Les propriétés `type` et `direction` identifient la liaison. La propriété `name` spécifie de quelle façon la fonction fournit la nouvelle ligne de table, dans ce cas à l’aide de la valeur de retour de fonction. Le nom de la table se trouve dans `tableName`, et la chaîne de connexion dans le paramètre d’application identifié par `connection`.

Pour afficher et modifier le contenu de *function.json* dans le portail Azure, cliquez sur l’option **Éditeur avancé** dans l’onglet **Intégrer** de votre fonction.

> [!NOTE]
> La valeur de `connection` est le nom d’un paramètre d’application qui contient la chaîne de connexion, et non la chaîne de connexion elle-même. Les liaisons utilisent des chaînes de connexion stockées dans les paramètres d’application pour appliquer la meilleure pratique qui consiste à ce que *function.json* ne contienne aucun secret de service.

## <a name="c-script-example"></a>Exemple de script C#

Voici le code de script C# qui fonctionne avec ce déclencheur et la liaison. Notez que le nom du paramètre qui fournit le contenu de message de file d’attente est `order` ; ce nom est obligatoire, car la valeur de propriété `name` dans *function.json* est `order`. 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>Exemple JavaScript

Le même *function.json* fichier peut être utilisé avec une fonction JavaScript :

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Exemple de bibliothèque de classes

Dans une bibliothèque de classes, les mêmes informations de déclencheur et de liaison &mdash; noms de file d’attente et de tables, comptes de stockage, paramètres de fonction pour l’entrée et la sortie &mdash; sont fournies par les attributs et non par un fichier .json. Voici un exemple :

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Vous disposez maintenant d’une fonction de travail qui est déclenchée par stockage Table Azure qui envoie des données à une file d’attente.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Les modèles d’expression de liaison d’Azure Functions](./functions-bindings-expressions-patterns.md)

