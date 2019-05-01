---
title: Déboguer localement des fonctions d’Azure PowerShell
description: Découvrez comment développer des fonctions à l’aide de PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: 554b7b7f401ec7cdb1ae08839550b81d797764f2
ms.sourcegitcommit: 111a7b3e19d5515ce7036287cea00a7204ca8b56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64530330"
---
# <a name="debug-powershell-azure-functions-locally"></a>Déboguer localement des fonctions d’Azure PowerShell

Azure Functions vous permet de développer vos fonctions en tant que les scripts PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Vous pouvez déboguer localement vos fonctions PowerShell comme vous le feriez pour tout script PowerShell à l’aide des outils de développement standard suivants :

* [Visual Studio Code](https://code.visualstudio.com/) : Éditeur de texte gratuits, légers et open source de Microsoft avec l’extension PowerShell qui offre une expérience de développement PowerShell complète.
* Une console PowerShell : Débogage en utilisant les mêmes commandes que vous utiliseriez pour déboguer un autre processus PowerShell.

[Azure Functions Core Tools](functions-run-local.md) prend en charge le débogage local d’Azure Functions, dont les fonctions PowerShell.

## <a name="example-function-app"></a>Exemple d’application (fonction)

L’application de fonction utilisée dans cet article a une seule fonction déclenchée via HTTP et présente les fichiers suivants :

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Cette application de fonction est semblable à celle que vous obtenez lorsque vous terminez la [démarrage rapide de PowerShell](functions-create-first-function-powershell.md).

Le code de fonction dans `run.ps1` se présente comme le script suivant :

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Définir le point d’attache

Pour déboguer une fonction PowerShell, la fonction doit arrêter le débogueur doit être attaché. Le `Wait-Debugger` applet de commande arrête l’exécution et attend que le débogueur.

Il vous suffit de faire est d’ajouter un appel à la `Wait-Debugger` applet de commande juste au-dessus du `if` instruction, comme suit :

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Le débogage commence selon le `if` instruction. 

Avec `Wait-Debugger` en place, vous pouvez maintenant déboguer les fonctions à l’aide de Visual Studio Code ou une console PowerShell.

## <a name="debug-in-visual-studio-code"></a>Déboguer dans Visual Studio Code

Pour déboguer vos fonctions PowerShell dans Visual Studio Code, vous devez disposer des extensions suivantes pour Visual Studio Code :

* [PowerShell](/powershell/scripting/components/Visual Studio Code/using-Visual Studio Code)
* [Azure Functions](functions-create-first-function-vs-code.md)

Après avoir installé les extensions PowerShell et Azure Functions, charger un projet d’application de fonction existante. Vous pouvez également [créer un projet Functions](functions-create-first-function-vs-code.md).

>[!NOTE]
> Votre projet ne doit pas avoir les fichiers de configuration nécessaires, vous êtes invité à les ajouter.

### <a name="start-the-function-app"></a>Démarrer l’application de fonction

Vérifiez que `Wait-Debugger` est définie dans la fonction où vous souhaitez attacher le débogueur.  Avec `Wait-Debugger` ajouté, vous pouvez déboguer votre application de fonction à l’aide de Visual Studio Code.

Choisissez le **déboguer** volet, puis **attacher à une fonction PowerShell**.

![Débogueur](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Vous pouvez également appuyer sur la touche F5 pour démarrer le débogage.

Le début d’opération de débogage effectue les tâches suivantes :

* Exécutions `func extensions install` dans le terminal pour installer des extensions d’Azure Functions requises par votre application de fonction.
* Exécutions `func host start` dans le terminal pour démarrer l’application de fonction dans l’hôte Functions.
* Attacher le débogueur PowerShell à l’instance d’exécution de PowerShell dans le runtime Functions.

Avec votre application de fonction en cours d’exécution, vous avez besoin d’une console PowerShell distincte pour appeler la fonction déclenchée via HTTP.

Dans ce cas, la console PowerShell est le client. Le `Invoke-RestMethod` est utilisée pour déclencher la fonction.

Dans une console PowerShell, exécutez la commande suivante :

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Vous remarquerez qu’une réponse n’est pas immédiatement retournée. C’est parce que `Wait-Debugger` a attaché le débogueur et PowerShell l’exécution est passé en mode arrêt dès qu’il est impossible. Cela est dû au fait le [BreakAll concept](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), qui est expliqué plus loin. Une fois que vous appuyez sur la `continue` bouton, le débogueur maintenant s’arrête sur la ligne juste après `Wait-Debugger`.

À ce stade, le débogueur est attaché et vous pouvez effectuer les normales de débogage opérations. Pour plus d’informations sur l’utilisation du débogueur dans Visual Studio Code, consultez [la documentation officielle](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Une fois que vous continuez et entièrement appelez votre script, vous remarquerez que :

* La console PowerShell qui a effectué la `Invoke-RestMethod` a retourné un résultat
* La Console intégrée de PowerShell dans Visual Studio Code est en attente d’un script à exécuter

Les fois suivantes lorsque vous appelez la même fonction, le débogueur dans PowerShell extension s’arrête juste après le `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Débogage dans une Console PowerShell

>[!NOTE]
> Cette section suppose que vous avez lu la [docs d’Azure Functions Core Tools](functions-run-local.md) et savoir comment utiliser le `func host start` commande pour démarrer votre application de fonction.

Ouvrez une console, `cd` dans le répertoire de votre application de fonction, puis exécutez la commande suivante :

```sh
func host start
```

Avec l’application de fonction en cours d’exécution et le `Wait-Debugger` en place, vous pouvez attacher au processus. Vous avez besoin des deux consoles PowerShell plus.

Une des consoles agit comme client. À partir de cela, vous appelez `Invoke-RestMethod` pour déclencher la fonction. Par exemple, vous pouvez exécuter la commande suivante :

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Vous remarquerez qu’elle ne retourne pas une réponse, ce qui est le résultat de la `Wait-Debugger`. L’instance d’exécution PowerShell attend maintenant un débogueur à attacher. Nous allons obtenir lié.

Dans la console autres PowerShell, exécutez la commande suivante :

```powershell
Get-PSHostProcessInfo
```

Cette applet de commande retourne une table qui ressemble à la sortie suivante :

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Prenez note de la `ProcessId` pour l’élément dans la table avec la `ProcessName` comme `dotnet`. Ce processus est votre function app.

Ensuite, exécutez l’extrait de code suivant :

```powershell
# This enters into the the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Une fois démarré, le débogueur s’arrête et montre quelque chose comme la sortie suivante :

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

À ce stade, vous êtes arrêté à un point d’arrêt dans le [débogueur PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). À ce stade, vous pouvez effectuer toutes les opérations de débogage habituelles, pas à pas principal, pas à pas détaillé, continuer, quittez et d’autres. Pour afficher l’ensemble complet des commandes de débogage disponibles dans la console, exécutez le `h` ou `?` commandes.

Vous pouvez également définir des points d’arrêt à ce niveau avec le `Set-PSBreakpoint` applet de commande.

Une fois que vous continuez et entièrement appelez votre script, vous remarquerez que :

* La console PowerShell où vous avez exécuté `Invoke-RestMethod` a retourné maintenant un résultat.
* La console PowerShell où vous avez exécuté `Debug-Runspace` est en attente d’un script à exécuter.

Vous pouvez appeler à nouveau la même fonction (à l’aide de `Invoke-RestMethod` par exemple) et le débogueur s’arrête dans juste après le `Wait-Debugger` commande.

## <a name="considerations-for-debugging"></a>Considérations relatives au débogage

Gardez l’esprit les problèmes suivants lors du débogage de votre code de fonctions.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` peut entraîner le débogueur s’arrête dans un emplacement inattendu

Les utilisations d’extension PowerShell `Debug-Runspace`, qui à son tour dépend du PowerShell `BreakAll` fonctionnalité. Cette fonctionnalité indique à PowerShell pour arrêter à la première commande est exécutée. Ce comportement vous donne la possibilité de définir des points d’arrêt au sein de l’instance d’exécution débogué.

Le runtime Azure Functions s’exécute quelques commandes avant d’appeler réellement votre `run.ps1` de script, il est donc possible que le débogueur se termine avec rupture dans les `Microsoft.Azure.Functions.PowerShellWorker.psm1` ou `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Ce saut doit se produire, exécutez le `continue` ou `c` commande pour ignorer ce point d’arrêt. Vous arrêtez puis sur le point d’arrêt attendu.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le développement de fonctions à l’aide de PowerShell, consultez [guide du développeur Azure fonctions PowerShell](functions-reference-powershell.md).
