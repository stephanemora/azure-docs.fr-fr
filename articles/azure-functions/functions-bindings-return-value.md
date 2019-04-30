---
title: À l’aide de la valeur de retour d’une fonction Azure
description: Apprenez à gérer les valeurs de retour pour Azure Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 01/14/2019
ms.date: 03/04/2019
ms.author: v-junlch
ms.openlocfilehash: 4ccfe192eaea94cb9b199bd3c6f0bdacf1685519
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437716"
---
# <a name="using-the-azure-function-return-value"></a>À l’aide de la valeur de retour de fonction Azure

Cet article explique comment des valeurs de retour à l’intérieur d’une fonction.

Dans les langages qui ont une valeur de retour, vous pouvez lier une fonction [liaison de sortie](./functions-triggers-bindings.md#binding-direction) à la valeur de retour :

- Dans une bibliothèque de classes C#, appliquez l’attribut de liaison de sortie à la valeur de retour de la méthode.
- Dans d’autres langages, définissez la propriété `name` dans *function.json* sur `$return`.

S’il existe plusieurs liaisons de sortie, utilisez la valeur de retour pour un seul d’entre eux.

En C# et dans les scripts C#, les autres méthodes pour envoyer des données à une liaison de sortie sont les paramètres `out` et les [objets collecteurs](functions-reference-csharp.md#writing-multiple-output-values).

Consultez l’exemple spécifique à un langage montrant l’utilisation de la valeur de retour :

- [C#](#c-example)
- [Script C# (.csx)](#c-script-example)
- [F#](#f-example)
- [JavaScript](#javascript-example)
- [Python](#python-example)

## <a name="c-example"></a>Exemple en code C#

Voici du code C# qui utilise la valeur de retour pour une liaison de sortie, suivie d’un exemple asynchrone :

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

## <a name="c-script-example"></a>Exemple de script C#

Voici la liaison de sortie dans le fichier *function.json* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Voici le code d’un script C#, suivi d’un exemple asynchrone :

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

## <a name="f-example"></a>Exemple F#

Voici la liaison de sortie dans le fichier *function.json* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Voici le code F# :

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="javascript-example"></a>Exemple JavaScript

Voici la liaison de sortie dans le fichier *function.json* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Dans JavaScript, la valeur de retour apparaît dans le second paramètre pour `context.done` :

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Exemple Python

Voici la liaison de sortie dans le fichier *function.json* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Voici le code Python :

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer les erreurs de liaison d’Azure Functions](./functions-bindings-errors.md)

