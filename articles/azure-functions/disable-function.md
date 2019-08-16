---
title: Guide pratique pour désactiver des fonctions dans Azure Functions
description: Découvrez comment désactiver et activer des fonctions dans Azure Functions 1.x et 2.x.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: 183056d01146194b2854a70df790802e1a0bb839
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782232"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Guide pratique pour désactiver des fonctions dans Azure Functions

Cet article explique comment désactiver une fonction dans Azure Functions. Quand vous *désactivez* une fonction, le runtime ignore le déclencheur automatique défini pour la fonction. La procédure à suivre pour y parvenir dépend de la version du runtime et du langage de programmation :

* Functions 2.x :
  * Méthode pour tous les langages
  * Méthode facultative pour les bibliothèques de classes C#
* Functions 1.x :
  * Langages de script
  * Bibliothèque de classes C#

## <a name="functions-2x---all-languages"></a>Functions 2.x - Tous les langages

Dans Functions 2.x, vous désactivez une fonction à l'aide d'un paramètre d'application au format `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Vous pouvez créer et modifier ce paramètre par programmation à l'aide de l'interface de ligne de commande Azure. Vous pouvez également le faire à partir de l'onglet **Gérer** de votre fonction sur le [Portail Azure](https://portal.azure.com). 

### <a name="azure-cli"></a>D’Azure CLI

Dans l'interface de ligne de commande Azure, vous utilisez la commande [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) pour créer et modifier le paramètre d'application. La commande suivante désactive une fonction nommée `QueueTrigger` en créant un paramètre d'application nommé `AzureWebJobs.QueueTrigger.Disabled` et en le définissant sur `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Pour réactiver la fonction, réexécutez la même commande avec la valeur `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portail

Vous pouvez également utiliser le commutateur **État de la fonction** sous l’onglet **Gérer** de la fonction. Le commutateur crée et supprime le paramètre d’application `AzureWebJobs.<FUNCTION_NAME>.Disabled`.

![Commutateur d’état de la fonction](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functions 2.x - Bibliothèques de classes C#

Dans une bibliothèque de classes Functions 2.x, nous vous recommandons d’utiliser la méthode qui fonctionne pour tous les langages. Toutefois, si vous préférez, vous pouvez [utiliser l’attribut Disable comme dans Functions 1.x](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Functions 1.x - Langages de script

Pour les langages de script tels que JavaScript et C#, vous utilisez la propriété `disabled` du fichier *function.json* pour indiquer au runtime de ne pas déclencher une fonction. Vous pouvez affecter à cette propriété la valeur `true` ou le nom d’un paramètre d’application :

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
or 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

Dans le deuxième exemple, la fonction est désactivée quand un paramètre d’application nommé IS_DISABLED existe et a la valeur `true` ou 1.

Vous pouvez modifier le fichier dans le portail Azure ou utiliser le commutateur **État de la fonction** sous l’onglet **Gérer** de la fonction. Le commutateur du portail change le fichier *function.json*.

![Commutateur d’état de la fonction](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Functions 1.x - Bibliothèques de classes C#

Dans une bibliothèque de classes Functions 1.x, vous utilisez un attribut `Disable` pour empêcher une fonction de se déclencher. Vous pouvez utiliser l’attribut sans paramètre de constructeur, comme dans l’exemple suivant :

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

L’attribut sans paramètre de constructeur vous oblige à recompiler et à redéployer le projet pour changer l’état désactivé de la fonction. Un moyen plus souple d’utiliser l’attribut consiste à inclure un paramètre de constructeur faisant référence à un paramètre d’application booléen, comme dans l’exemple suivant :

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Cette méthode vous permet d’activer et de désactiver la fonction en changeant le paramètre d’application, sans recompiler ou redéployer quoi que soit. Le fait de changer un paramètre d’application entraîne le redémarrage de l’application de fonction. Le changement de l’état désactivé est donc reconnu immédiatement.

> [!IMPORTANT]
> L’attribut `Disabled` est la seule façon de désactiver une fonction de bibliothèque de classes. Le fichier *function.json* généré pour une fonction de bibliothèque de classes n’est pas destiné à être modifié directement. Si vous modifiez ce fichier, toute modification apportée à la propriété `disabled` n’a aucun effet.
>
> Il en va de même pour le commutateur **État de fonction**, sous l’onglet **Gérer**, dans la mesure où il change le fichier *function.json*.
>
> Notez également que le portail peut indiquer que la fonction est désactivée alors qu’elle ne l’est pas.

## <a name="next-steps"></a>Étapes suivantes

Cet article traite de la désactivation des déclencheurs automatiques. Pour plus d’informations sur les déclencheurs, consultez [Déclencheurs et liaisons](functions-triggers-bindings.md).
