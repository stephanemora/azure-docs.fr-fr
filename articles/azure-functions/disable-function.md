---
title: Guide pratique pour désactiver des fonctions dans Azure Functions
description: Découvrez comment désactiver et activer des fonctions dans Azure Functions.
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cbb84308507ea15f1c44c00122a9a59472f12a88
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551041"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Guide pratique pour désactiver des fonctions dans Azure Functions

Cet article explique comment désactiver une fonction dans Azure Functions. Quand vous *désactivez* une fonction, le runtime ignore le déclencheur automatique défini pour la fonction. Ceci vous permet d’empêcher une fonction spécifique de s’exécuter sans arrêter toute l’application de fonction.

La façon recommandée de désactiver une fonction est d’utiliser un paramètre d’application au format `AzureWebJobs.<FUNCTION_NAME>.Disabled` défini sur `true`. Vous pouvez créer et modifier ce paramètre d’application de plusieurs façons, notamment à l’aide de l’interface [Azure CLI](/cli/azure/) et à partir de l’onglet **Vue d’ensemble** de votre fonction sur le [portail Azure](https://portal.azure.com). 

> [!NOTE]  
> Lorsque vous désactivez une fonction déclenchée par HTTP à l’aide des méthodes décrites dans cet article, le point de terminaison peut toujours être accessible en cas d’exécution sur votre ordinateur local.  

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

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

## <a name="use-the-portal"></a>Utiliser le portail

Vous pouvez également utiliser les boutons **Activer** et **Désactiver** sur la page **Vue d’ensemble** de la fonction. Ces boutons fonctionnent en modifiant la valeur du paramètre d’application `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Ce paramètre propre à la fonction est créé la première fois qu’il est désactivé. 

![Commutateur d’état de la fonction](media/disable-function/function-state-switch.png)

Même lorsque vous publiez dans votre application de fonction à partir d’un projet local, vous pouvez toujours utiliser le portail pour désactiver des fonctions dans l’application de fonction. 

> [!NOTE]  
> La fonctionnalité de test intégré au portail ignore le paramètre `Disabled`. Cela signifie qu’une fonction désactivée continue à s’exécuter quand elle est démarrée à partir de la fenêtre **Test** dans le portail. 

## <a name="localsettingsjson"></a>local.settings.json

Les fonctions peuvent être désactivées de la même façon lors de l’exécution locale. Pour désactiver une fonction nommée `HttpExample`, ajoutez une entrée à la collection Values dans le fichier local.settings.json, comme suit :

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>Autres méthodes

Bien que la méthode via un paramètre d’application soit recommandée pour tous les langages et toutes les versions du runtime, il existe plusieurs autres méthodes pour désactiver des fonctions. Ces méthodes, qui varient selon le langage et la version du runtime, sont conservées à des fins de compatibilité descendante. 

### <a name="c-class-libraries"></a>Bibliothèque de classes C#

Dans une fonction de bibliothèque de classes, vous pouvez aussi utiliser l’attribut `Disable` pour empêcher le déclenchement de la fonction. Cet attribut vous permet de personnaliser le nom du paramètre utilisé pour désactiver la fonction. Utilisez la version de l’attribut qui vous permet de définir un paramètre de constructeur faisant référence à un paramètre d’application booléen, comme indiqué dans l’exemple suivant :

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

Il existe également un constructeur pour le paramètre qui n’accepte pas de chaîne pour le nom du paramètre. Cette version de l’attribut n’est pas recommandée. Si vous utilisez cette version, vous devez recompiler et redéployer le projet pour modifier l’état désactivé de la fonction.

### <a name="functions-1x---scripting-languages"></a>Functions 1.x - Langages de script

Dans la version 1.x, vous pouvez aussi utiliser la propriété `disabled` du fichier *function.json* pour indiquer au runtime de ne pas déclencher une fonction. Cette méthode fonctionne seulement pour les langages de script, comme un script C# et JavaScript. La propriété `disabled` peut être définie sur `true` ou sur le nom d’un paramètre de l’application :

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

>[!IMPORTANT]  
>Le portail utilise les paramètres d’application pour désactiver les fonctions v1.x. Lorsqu’un paramètre d’application est en conflit avec le fichier function.json, une erreur peut se produire. Vous devez supprimer la propriété `disabled` du fichier function.json pour éviter les erreurs. 


## <a name="next-steps"></a>Étapes suivantes

Cet article traite de la désactivation des déclencheurs automatiques. Pour plus d’informations sur les déclencheurs, consultez [Déclencheurs et liaisons](functions-triggers-bindings.md).
