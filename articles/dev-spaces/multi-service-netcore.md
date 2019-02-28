---
title: Exécution de plusieurs services dépendants à l’aide de .NET Core et de VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
ms.openlocfilehash: 39c9d2f938dd3e06a328626756ddcc80d0aad47b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821232"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Développement multiservice avec Azure Dev Spaces

Dans ce tutoriel, vous allez apprendre à développer des applications multiservices à l’aide d’Azure Dev Spaces, et découvrir certains des avantages de Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Appeler un service en cours d’exécution dans un conteneur distinct

Dans cette section, vous allez créer un deuxième service (`mywebapi`) et demander à `webfrontend` de l’appeler. Chaque service s’exécutera dans un conteneur distinct. Ensuite, vous effectuerez le débogage dans les deux conteneurs.

![Plusieurs conteneurs](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Télécharger l’exemple de code pour *mywebapi*
Pour des questions de temps, nous allons télécharger un exemple de code à partir d’un référentiel GitHub. Accédez à https://github.com/Azure/dev-spaces et sélectionnez **Clone or Download** (Cloner ou Télécharger) pour télécharger depuis le référentiel GitHub. Le code de cette section se trouve dans `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Exécuter *mywebapi*
1. Ouvrez le dossier `mywebapi` dans une *fenêtre VS Code distincte*.
1. Ouvrez la **Palette de commandes** (à partir du menu **Affichage | Palette de commandes**) et utilisez la saisie semi-automatique pour taper et sélectionnez cette commande : `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Cette commande ne doit pas être confondue avec la commande `azds prep`, qui configure le projet pour le déploiement.
1. Appuyez sur F5, et attendez la création et le déploiement du service. Ce dernier sera prêt lorsque vous verrez la barre de débogage VS Code s’afficher.
1. L’URL du point de terminaison doit ressembler à http://localhost:\<portnumber\>. **Conseil : La barre d’état de VS Code affichera une URL sur laquelle vous pouvez cliquer.** Le conteneur a l’air de s’exécuter en local, mais en réalité, il s’exécute dans notre espace de développement dans Azure. L’adresse localhost est utilisée car `mywebapi` n’a pas défini de points de terminaison publics et est accessible uniquement à partir de l’instance Kubernetes. Pour des raisons pratiques et pour faciliter l’interaction avec le service privé à partir de votre ordinateur local, Azure Dev Spaces crée un tunnel SSH temporaire vers le conteneur en cours d’exécution dans Azure.
1. Lorsque `mywebapi` est prêt, ouvrez votre navigateur à l’adresse localhost. Ajoutez `/api/values` à l’URL pour appeler l’API GET par défaut pour le `ValuesController`.
1. Si toutes les étapes se sont déroulées correctement, vous pouvez voir une réponse du service `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Effectuer une requête de *webfrontend* à *mywebapi*
Nous allons maintenant écrire du code dans `webfrontend` qui envoie une requête à `mywebapi`.
1. Basculez vers la fenêtre VS Code pour `webfrontend`.
1. *Remplacez* le code de la méthode About dans `HomeController.cs` :

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

L’exemple de code précédent transfère l’en-tête `azds-route-as` de la requête entrante à la requête sortante. Vous verrez ultérieurement de quelle façon cette fonction aide les équipes au niveau du [développement collaboratif](team-development-netcore.md).

### <a name="debug-across-multiple-services"></a>Déboguer dans plusieurs services
1. À ce stade, `mywebapi` doit toujours être en cours d’exécution avec le débogueur joint. Si ce n’est pas le cas, appuyez sur F5 dans le projet `mywebapi`.
1. Définissez un point d’arrêt dans la méthode `Get(int id)` qui gère les requêtes GET `api/values/{id}`.
1. Dans le projet `webfrontend`, définissez un point d’arrêt juste avant l’envoi d’une requête GET à `mywebapi/api/values`.
1. Appuyez sur F5 dans le projet `webfrontend`.
1. Appelez l’application web et parcourez le code dans les deux services.
1. Dans l’application web, la page About affichera un message concaténé par les deux services : « Hello from webfrontend and Hello from mywebapi. »

### <a name="automatic-tracing-for-http-messages"></a>Traçage automatique des messages HTTP
Vous avez peut-être remarqué que, même si *webfrontend* ne contient pas de code permettant d’imprimer l’appel HTTP qu’il émet vers *mywebapi*, des messages de trace HTTP s’affichent dans la fenêtre de sortie :
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --gyk-> webfrontend:
   GET /Home/About HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend-668b7ddb9f-n5rhj --pu5-> mywebapi:
   GET /api/values/1 HTTP/1.1

// Response from *mywebapi*
webfrontend-668b7ddb9f-n5rhj <-pu5-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-gyk-- webfrontend:
   HTTP/1.1 200 OK
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="utf-8" />
       <meta name="viewport" content="width=device-width, initial-sc...<[TRUNCATED]>
```
Cela fait partie des avantages « gratuits » qui sont offerts par l’instrumentation Dev Spaces. Nous insérons des composants qui effectuent le suivi des requêtes HTTP qui traversent le système afin de faciliter le suivi des appels multiservices complexes pendant le développement.


### <a name="well-done"></a>C’est terminé !
Vous disposez maintenant d’une application à plusieurs conteneurs où chaque conteneur peut être développé et déployé séparément.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur le développement en équipe dans Dev Spaces](team-development-netcore.md)