---
title: 'Exécution de plusieurs services dépendants : .NET Core et Visual Studio'
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: Ce tutoriel vous montre comment utiliser Azure Dev Spaces et Visual Studio pour déboguer une application .NET Core multiservice sur Azure Kubernetes Service.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s
ms.openlocfilehash: 073019a75f78263e9d300a82469b36268d032679
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072939"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-with-azure-dev-spaces"></a>Exécution de plusieurs services dépendants : .NET Core et Visual Studio avec Azure Dev Spaces

Dans ce tutoriel, vous allez apprendre à développer des applications multiservices à l’aide d’Azure Dev Spaces, et découvrir certains des avantages de Dev Spaces.

## <a name="call-another-container"></a>Appeler un autre conteneur
Dans cette section, vous allez créer un deuxième service, `mywebapi`, et demander à `webfrontend` de l’appeler. Chaque service s’exécutera dans un conteneur distinct. Ensuite, vous allez effectuer le débogage dans les deux conteneurs.

![Le schéma illustre le service webfrontend appelant le service mywebapi (comme indiqué par la flèche).](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Télécharger l’exemple de code pour *mywebapi*
Pour des questions de temps, nous allons télécharger un exemple de code à partir d’un référentiel GitHub. Accédez à https://github.com/Azure/dev-spaces et sélectionnez **Clone or Download** (Cloner ou Télécharger) pour télécharger depuis le référentiel GitHub. Le code de cette section se trouve dans `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Exécuter *mywebapi*
1. Ouvrez le projet `mywebapi` dans une *fenêtre distincte de Visual Studio*.
1. Sélectionnez **Azure Dev Spaces** dans la liste déroulante des paramètres de lancement comme vous l’avez déjà fait pour le projet `webfrontend`. Au lieu de créer un nouveau cluster ASK, sélectionnez celui que vous avez déjà créé. Comme avant, laissez le champ Space (Espace) sur `default` et cliquez sur **OK**. Dans la fenêtre Sortie, vous pouvez voir que Visual Studio commence à « préparer » ce nouveau service dans votre espace de développement afin d’accélérer les choses quand vous commencez à déboguer.
1. Appuyez sur F5, et attendez la création et le déploiement du service. Lorsque la barre d’état de Visual Studio devient orange, le service est prêt.
1. Notez l’URL du point de terminaison qui s’affiche dans le volet **Azure Dev Spaces pour AKS** de la fenêtre **Sortie**. Elle ressemble à ceci : `http://localhost:<portnumber>` Le conteneur a l’air de s’exécuter en local, mais en réalité, il s’exécute dans l’espace de développement dans Azure.
2. Lorsque `mywebapi` est prêt, ouvrez votre navigateur à l’adresse localhost et ajoutez `/api/values` à l’URL pour appeler l’API GET par défaut pour le `ValuesController`. 
3. Si toutes les étapes se sont déroulées correctement, vous pouvez voir une réponse du service `mywebapi` qui ressemble à ceci.

    ![La page web présente un tableau JSON à deux chaînes : « value1 » et « value2 ».](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Effectuer une requête à partir de *webfrontend* sur *mywebapi*
Nous allons maintenant écrire du code dans `webfrontend` qui envoie une requête à `mywebapi`. Basculez vers la fenêtre Visual Studio qui contient le projet `webfrontend`. Dans le fichier `HomeController.cs`, *remplacez* le code de la méthode About par le code suivant :

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

L’exemple de code précédent transfère l’en-tête `azds-route-as` de la requête entrante à la requête sortante. Vous verrez plus tard comment cela permet d’augmenter la productivité dans les [scénarios d’équipe](team-development-netcore-visualstudio.md).

### <a name="debug-across-multiple-services"></a>Déboguer dans plusieurs services
1. À ce stade, `mywebapi` doit toujours être en cours d’exécution avec le débogueur joint. Si ce n’est pas le cas, appuyez sur F5 dans le projet `mywebapi`.
1. Définissez un point d’arrêt dans la méthode `Get(int id)` du fichier `Controllers/ValuesController.cs` qui gère les demandes GET `api/values/{id}`.
1. Dans le projet `webfrontend` où vous avez collé le code ci-dessus, définissez un point d’arrêt juste avant l’envoi d’une requête GET à `mywebapi/api/values`.
1. Appuyez sur F5 dans le projet `webfrontend`. Visual Studio ouvre à nouveau un navigateur sur le port localhost approprié et l’application web s’affiche.
1. Cliquez sur le lien « **About** » (À propos de) en haut de la page pour déclencher le point d’arrêt dans le projet `webfrontend`. 
1. Appuyez sur F10 pour continuer. Le point d’arrêt dans le projet `mywebapi` est maintenant déclenché.
1. Appuyez sur F5 pour continuer et vous êtes redirigé dans le code du projet `webfrontend`.
1. En appuyant encore une fois sur F5, vous terminez la requête et renvoyez une page dans le navigateur. Dans l’application web, la page About affichera un message concaténé par les deux services : « Hello from webfrontend and Hello from mywebapi. »

### <a name="well-done"></a>C’est terminé !
Vous disposez maintenant d’une application à plusieurs conteneurs où chaque conteneur peut être développé et déployé séparément.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur le développement en équipe dans Dev Spaces](team-development-netcore-visualstudio.md)
