---
title: Créer un environnement de développement Kubernetes dans le cloud à l’aide de .NET Core et VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Développement Kubernetes rapide avec les conteneurs et les microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
manager: douge
ms.openlocfilehash: a57118feb85a010e38d73b758ebfb84d1cc463fa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361248"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core"></a>Prise en main d’Azure Dev Spaces avec .NET Core

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Vous voici prêt à créer un environnement de développement Kubernetes dans Azure.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure
Azure Dev Spaces requiert une configuration d’ordinateur local minimale. La majeure partie de la configuration de votre environnement de développement est stockée dans le cloud et peut être partagée avec d’autres utilisateurs. Commencez par télécharger et exécuter [l’interface de ligne de commande Azure (Azure CLI)](/cli/azure/install-azure-cli?view=azure-cli-latest). 

> [!IMPORTANT]
> Si vous avez déjà installé Azure CLI, vérifiez que vous utilisez la version 2.0.32 ou une version ultérieure.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Pendant que vous attendez que le cluster soit créé, vous pouvez commencer à développer du code.

## <a name="create-an-aspnet-core-web-app"></a>Créez une application web ASP.NET Core
Si vous avez installé [.NET Core](https://www.microsoft.com/net), vous pouvez rapidement créer une application web ASP.NET Core dans un dossier nommé `webfrontend`.
    
```cmd
dotnet new mvc --name webfrontend
```

Une autre possibilité consiste à **télécharger un exemple de code à partir de GitHub** en accédant à https://github.com/Azure/dev-spaces, puis à sélectionner **Clone or Download** pour télécharger le référentiel GitHub dans votre environnement local. Le code de ce guide est disponible dans `samples/dotnetcore/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

## <a name="update-a-content-file"></a>Mettre à jour un fichier de contenu
Azure Dev Spaces vous permet non seulement d’obtenir un code s’exécutant dans Kubernetes, mais également de visualiser rapidement et de façon itérative la prise en compte des modifications de votre code dans un environnement Kubernetes dans le cloud.

1. Recherchez le fichier `./Views/Home/Index.cshtml` et procédez à une modification du code HTML. Par exemple, remplacez la ligne 70 indiquant `<h2>Application uses</h2>` par une chaîne telle que : `<h2>Hello k8s in Azure!</h2>`
1. Enregistrez le fichier . Quelques instants plus tard, la fenêtre de terminal affiche un message indiquant qu’un fichier du conteneur en cours d’exécution a été mis à jour.
1. Accédez à votre navigateur et actualisez la page. La page web doit afficher le code HTML mis à jour.

Que s’est-il passé ? Les modifications des fichiers de contenu, comme HTML et CSS, ne nécessitent aucune recompilation dans une application web .NET Core. Par conséquent, une commande `azds up` active synchronise automatiquement tous les fichiers de contenu modifiés dans le conteneur en cours d’exécution dans Azure, ce qui vous permet de visualiser immédiatement les modifications du contenu.

## <a name="update-a-code-file"></a>Mettre à jour un fichier de code
La mise à jour des fichiers de code nécessite un peu plus de travail, car une application .NET Core doit régénérer et produire les fichiers binaires d’application mis à jour.

1. Dans la fenêtre de terminal, appuyez sur `Ctrl+C` (pour arrêter `azds up`).
1. Ouvrez le fichier de code nommé `Controllers/HomeController.cs`, puis modifiez le message qui doit s’afficher sur la page À propos de : `ViewData["Message"] = "Your application description page.";`
1. Enregistrez le fichier .
1. Exécutez `azds up` dans la fenêtre de terminal. 

Cette commande régénère l’image conteneur et redéploie le graphique Helm. Pour visualiser la prise en compte de vos modifications de code dans l’application en cours d’exécution, accédez au menu À propos de figurant dans l’application web.


Toutefois, vous allez découvrir à la section suivante une *méthode encore plus rapide* pour développer du code. 

## <a name="debug-a-container-in-kubernetes"></a>Déboguer un conteneur dans Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]


### <a name="select-the-azds-debug-configuration"></a>Sélectionner la configuration de débogage AZDS
1. Pour ouvrir l’affichage de débogage, cliquez sur l’icône Débogage dans la **barre d’activités** située sur le côté de VS Code.
1. Sélectionnez **Lancer .NET Core (AZDS)** comme configuration de débogage active.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Si la palette de commandes ne présente aucune commande Azure Dev Spaces, vérifiez que vous avez installé l’extension VS Code pour Azure Dev Spaces. Assurez-vous que l’espace de travail que vous avez ouvert dans VS Code correspond au dossier contenant azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Déboguer le conteneur dans Kubernetes
Pour déboguer votre code dans Kubernetes, appuyez sur **F5**.

Comme avec la commande `up`, le code est synchronisé avec l’environnement de développement, et un conteneur est créé et déployé dans Kubernetes. Cette fois-ci, le débogueur est bien entendu joint au conteneur distant.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

Définissez un point d’arrêt dans un fichier de code côté serveur, par exemple dans la fonction `Index()` du fichier source `Controllers/HomeController.cs`. L’actualisation de la page du navigateur entraîne l’atteinte du point d’arrêt.

Vous disposez d’un accès complet aux informations de débogage, exactement comme si le code s’exécutait en local, notamment à la pile des appels, aux variables locales, aux informations sur les exceptions, etc.

### <a name="edit-code-and-refresh"></a>Modifier du code et actualiser la page
Lorsque le débogueur est actif, apportez une modification au code. Par exemple, modifiez le message de la page À propos de dans `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Enregistrez le fichier, puis dans le **volet Actions de débogage**, cliquez sur le bouton **Actualiser**. 

![](media/get-started-netcore/debug-action-refresh.png)

Plutôt que de régénérer et redéployer une image conteneur chaque fois que des modifications de code sont effectuées, cette opération nécessitant généralement un temps considérable, Azure Dev Spaces recompile le code de manière incrémentielle au sein du conteneur existant afin d’accélérer la boucle de modification/débogage.

Actualisez l’application web dans le navigateur, puis accédez à la page À propos de. Votre message personnalisé doit apparaître dans l’interface utilisateur.

**Vous disposez désormais d’une méthode d’itération rapide sur le code et de débogage directement dans Kubernetes.** Ensuite, vous verrez comment créer et appeler un second conteneur.

## <a name="call-a-service-running-in-a-separate-container"></a>Appeler un service en cours d’exécution dans un conteneur distinct

Dans cette section, vous créez un deuxième service `mywebapi` et demander à `webfrontend` de l’appeler. Chaque service s’exécutera dans un conteneur distinct. Ensuite, vous effectuerez le débogage dans les deux conteneurs.

![Plusieurs conteneurs](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Télécharger l’exemple de code pour *mywebapi*
Pour des questions de temps, nous allons télécharger un exemple de code à partir d’un référentiel GitHub. Accédez à https://github.com/Azure/dev-spaces et sélectionnez **Clone or Download** (Cloner ou Télécharger) pour télécharger depuis le référentiel GitHub. Le code de cette section se trouve dans `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Exécuter *mywebapi*
1. Ouvrez le dossier `mywebapi` dans une *fenêtre VS Code distincte*.
1. Appuyez sur F5, et attendez la création et le déploiement du service. Ce dernier sera prêt lorsque vous verrez la barre de débogage VS Code s’afficher.
1. Notez l’URL du point de terminaison qui doit ressembler à http://localhost:\<portnumber\>. **Conseil : la barre d’état VS Code affichera une URL interactive.** Le conteneur a l’air de s’exécuter en local, mais en réalité, il s’exécute dans notre environnement de développement dans Azure. L’adresse localhost est utilisée car `mywebapi` n’a pas défini de points de terminaison publics et est accessible uniquement à partir de l’instance Kubernetes. Pour des raisons pratiques et pour faciliter l’interaction avec le service privé à partir de votre ordinateur local, Azure Dev Spaces crée un tunnel SSH temporaire vers le conteneur en cours d’exécution dans Azure.
1. Lorsque `mywebapi` est prêt, ouvrez votre navigateur à l’adresse localhost. Ajoutez `/api/values` à l’URL pour appeler l’API GET par défaut pour le `ValuesController`. 
1. Si toutes les étapes se sont déroulées correctement, vous pouvez voir une réponse du service `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Effectuer une requête de *webfrontend* à *mywebapi*
Nous allons maintenant écrire du code dans `webfrontend` qui envoie une requête à `mywebapi`.
1. Basculez vers la fenêtre VS Code pour `webfrontend`.
1. *Remplacez* le code de la méthode About :

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        // Use HeaderPropagatingHttpClient instead of HttpClient so we can propagate
        // headers in the incoming request to any outgoing requests
        using (var client = new HeaderPropagatingHttpClient(this.Request))
        {
            // Call *mywebapi*, and display its response in the page
            var response = await client.GetAsync("http://mywebapi/api/values/1");
            ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
        }

        return View();
    }
    ```

La découverte des services DNS de Kubernetes est utilisée pour faire référence au service en tant que `http://mywebapi`. **Dans votre environnement de développement, le code s’exécute de la même façon qu’en production**.

L’exemple de code ci-dessus utilise aussi une classe `HeaderPropagatingHttpClient`. Cette classe du programme d’assistance a été ajoutée à votre dossier de code au moment où vous avez exécuté `azds prep`. `HeaderPropagatingHttpClient` est dérivé de la classe `HttpClient` bien connue et ajoute des fonctionnalités pour propager des en-têtes spécifiques à partir d’un objet ASP .NET HttpRequest existant dans un objet HttpRequestMessage sortant. Nous verrons plus tard comment l’utilisation de cette classe dérivée permet d’augmenter la productivité dans les scénarios d’équipe.


### <a name="debug-across-multiple-services"></a>Déboguer dans plusieurs services
1. À ce stade, `mywebapi` doit toujours être en cours d’exécution avec le débogueur joint. Si ce n’est pas le cas, appuyez sur F5 dans le projet `mywebapi`.
1. Définissez un point d’arrêt dans la méthode `Get(int id)` qui gère les requêtes GET `api/values/{id}`.
1. Dans le projet `webfrontend`, définissez un point d’arrêt juste avant l’envoi d’une requête GET à `mywebapi/api/values`.
1. Appuyez sur F5 dans le projet `webfrontend`.
1. Appelez l’application web et parcourez le code dans les deux services.
1. Dans l’application web, la page À propos de affiche un message concaténé provenant des deux services : « Hello from webfrontend and Hello from mywebap ».


C’est terminé ! Vous disposez maintenant d’une application à plusieurs conteneurs où chaque conteneur peut être développé et déployé séparément.

## <a name="learn-about-team-development"></a>En savoir plus sur le développement en équipe

[!INCLUDE[](includes/team-development-1.md)]

Découvrons-le en action. Accédez à la fenêtre VS Code pour `mywebapi` et modifiez le code de la méthode `string Get(int id)`, par exemple :

    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
