---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190845"
---
## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Exécutez votre fonction en démarrant l’hôte du runtime d’Azure Functions local à partir du dossier *LocalFunctionProj* :

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

Vers la fin de la sortie, les lignes suivantes doivent s’afficher : 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Si HttpExample n’apparaît pas comme indiqué ci-dessous, cela signifie probablement que vous avez démarré l’hôte à partir du dossier *HttpExample*. Dans ce cas, utilisez **Ctrl**+**C** pour arrêter l’hôte, accédez au dossier *LocalFunctionProj* parent, puis réexécutez la commande précédente.

Copiez dans un navigateur l’URL de votre fonction `HttpExample` à partir de cette sortie, puis ajoutez la chaîne de requête `?name=<your-name>` pour obtenir une URL complète semblable à `http://localhost:7071/api/HttpExample?name=Functions`. Le navigateur doit afficher un message similaire à `Hello Functions` :

![Résultat de la fonction exécutée localement dans le navigateur](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Le terminal dans lequel vous avez exécuté `func start` affiche également une sortie de journal quand vous effectuez des requêtes.

Quand vous êtes prêt, utilisez **Ctrl**+**C**, puis choisissez `y` pour arrêter l’hôte de fonctions.