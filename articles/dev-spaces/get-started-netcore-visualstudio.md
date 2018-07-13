---
title: Créer un espace de développement Kubernetes dans le cloud à l’aide de .NET Core et de Visual Studio | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
manager: douge
ms.openlocfilehash: c2d92f26bec2045e7f1e8afff189d58d8c29f25a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099474"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core-and-visual-studio"></a>Prise en main sur Azure Dev Spaces avec .NET Core et Visual Studio

Dans ce guide, vous allez apprendre à :

- Configurer Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
- Développer du code de façon itérative dans des conteneurs à l’aide de Visual Studio.
- Développer indépendamment deux services distincts et utiliser la découverte des services DNS de Kubernetes pour appeler un autre service.
- Développer et tester votre code de façon productive dans un environnement d’équipe.

[!INCLUDE[](includes/see-troubleshooting.md)]

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="get-the-visual-studio-tools"></a>Obtenir les outils Visual Studio
1. Installez la dernière version de [Visual Studio 2017](https://www.visualstudio.com/vs/).
1. Dans le programme d’installation de Visual Studio, vérifiez que la charge de travail suivante est sélectionnée :
    * Développement web et ASP.NET
1. Installer [Visual Studio Tools pour Kubernetes](https://aka.ms/get-azds-visualstudio)

## <a name="create-a-web-app-running-in-a-container"></a>Créer une application web s’exécutant dans un conteneur

Dans cette section, vous allez apprendre à créer une application web ASP.NET Core et à l’exécuter dans un conteneur dans Kubernetes.

### <a name="create-an-aspnet-web-app"></a>Créez une application web ASP.NET

Dans Visual Studio 2017, créez un projet. Actuellement, le projet doit correspondre à **Application web ASP.NET Core**. Nommez le projet « **webfrontend** ».

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Sélectionnez le modèle **Application Web (Model-View-Controller)** et assurez-vous que vous ciblez **.NET Core** et **ASP.NET Core 2.0** dans les deux menus déroulants au sommet de la boîte de dialogue. Cliquez sur **OK** pour créer le projet.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)


### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Activer Dev Spaces pour un cluster AKS

Ouvrez le projet que vous venez de créer, puis sélectionnez **Azure Dev Spaces** dans la liste déroulante des paramètres de lancement, comme indiqué ci-dessous.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Dans la boîte de dialogue qui s’affiche ensuite, assurez-vous que vous êtes connecté avec le compte approprié, puis sélectionnez un cluster Kubernetes existant.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

Laissez l’option de la liste déroulante **Space** (Espace) sur `default` pour l’instant. Vous en apprendrez davantage sur cette option un peu plus tard. Cochez la case **Publicly Accessible** (Accessible publiquement) pour que l’application web soit accessible par le biais d’un point de terminaison public. Ce paramètre n’est pas obligatoire, mais il servira à illustrer certains concepts dans la suite de cette procédure pas à pas. Dans les deux cas, vous serez en mesure de déboguer votre site web avec Visual Studio.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Cliquez sur **OK** pour sélectionner ou créer le cluster.

Si vous choisissez un cluster qui n’a pas été activé pour travailler avec Azure Dev Spaces, vous verrez un message qui vous demande si vous souhaitez le configurer.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Choisissez **OK**.

 Une tâche en arrière-plan démarre alors pour effectuer cette opération. L’exécution de cette tâche nécessite quelques minutes. Pour voir si la création est toujours en cours, placez votre pointeur sur l’icône **Tâches en arrière-plan** dans le coin inférieur gauche de la barre d’état, comme illustré ci-après.

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> Vous ne pouvez pas déboguer votre application avant la fin de la création de l’espace de développement.

### <a name="look-at-the-files-added-to-project"></a>Examiner les fichiers ajoutés au projet
En attendant la création de l’espace de développement, examinez les fichiers qui ont été ajoutés à votre projet quand vous avez choisi d’utiliser un espace de développement.

Tout d’abord, vous pouvez voir qu’un dossier nommé `charts` a été ajouté et que dans ce dossier, un [graphique Helm](https://docs.helm.sh) a été structuré pour votre application. Ces fichiers sont utilisés pour déployer votre application dans l’environnement de développement.

Vous verrez qu’un fichier nommé `Dockerfile` a été ajouté. Ce fichier contient les informations nécessaires pour empaqueter votre application au format Docker standard.

Enfin, vous verrez un fichier nommé `azds.yaml` qui contient la configuration au moment du développement nécessaire à l’espace de développement.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Déboguer un conteneur dans Kubernetes
Une fois l’espace de développement créé, vous pouvez déboguer votre application. Définissez un point d’arrêt dans le code, par exemple à la ligne 20 du fichier `HomeController.cs` où la variable `Message` est définie. Cliquez sur **F5** pour démarrer le débogage. 

Visual Studio communique avec l’espace de développement pour générer et déployer l’application, puis ouvre un navigateur avec l’application web en cours d’exécution. Le conteneur a l’air de s’exécuter en local, mais en réalité, il s’exécute dans l’espace de développement dans Azure. La présence de l’adresse localhost est due à Azure Dev Spaces qui crée un tunnel SSH temporaire jusqu’au conteneur en cours d’exécution dans AKS.

Cliquez sur le lien **À propos de** en haut de la page pour déclencher le point d’arrêt. Vous avez un accès complet aux informations pour le débogage, comme si le code s’exécutait en local, y compris à la pile des appels, aux variables locales, aux informations sur les exceptions, etc.

## <a name="iteratively-develop-code"></a>Développer du code de façon itérative

Azure Dev Spaces vous permet non seulement d’obtenir un code s’exécutant dans Kubernetes, mais également de visualiser rapidement et de façon itérative la prise en compte des modifications de votre code dans un environnement Kubernetes dans le cloud.

### <a name="update-a-content-file"></a>Mettre à jour un fichier de contenu
1. Recherchez le fichier `./Views/Home/Index.cshtml` et procédez à une modification du code HTML. Par exemple, remplacez la ligne 70 indiquant `<h2>Application uses</h2>` par une chaîne telle que : `<h2>Hello k8s in Azure!</h2>`
1. Enregistrez le fichier .
1. Accédez à votre navigateur et actualisez la page. La page web doit afficher le code HTML mis à jour.

Que s’est-il passé ? Les modifications des fichiers de contenu, comme HTML et CSS, ne nécessitent aucune recompilation dans une application web .NET Core. Une session F5 active synchronise donc automatiquement tous les fichiers de contenu modifiés dans le conteneur en cours d’exécution dans AKS, ce qui vous permet de visualiser immédiatement les modifications du contenu.

### <a name="update-a-code-file"></a>Mettre à jour un fichier de code
La mise à jour des fichiers de code nécessite un peu plus de travail, car une application .NET Core doit régénérer et produire les fichiers binaires d’application mis à jour.

1. Arrêtez le débogueur dans Visual Studio.
1. Ouvrez le fichier de code nommé `Controllers/HomeController.cs`, puis modifiez le message qui doit s’afficher sur la page À propos de : `ViewData["Message"] = "Your application description page.";`
1. Enregistrez le fichier .
1. Appuyez sur **F5** pour redémarrer le débogage. 

Plutôt que de régénérer et redéployer une image conteneur chaque fois que des modifications de code sont effectuées, cette opération nécessitant généralement un temps considérable, Azure Dev Spaces recompile le code de manière incrémentielle au sein du conteneur existant afin d’accélérer la boucle de modification/débogage.

Actualisez l’application web dans le navigateur, puis accédez à la page À propos de. Votre message personnalisé doit apparaître dans l’interface utilisateur.


## <a name="call-another-container"></a>Appeler un autre conteneur
Dans cette section, vous allez créer un deuxième service `mywebapi` et demander à `webfrontend` de l’appeler. Chaque service s’exécutera dans un conteneur distinct. Ensuite, vous allez effectuer le débogage dans les deux conteneurs.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Télécharger l’exemple de code pour *mywebapi*
Pour des questions de temps, nous allons télécharger un exemple de code à partir d’un référentiel GitHub. Accédez à https://github.com/Azure/dev-spaces et sélectionnez **Clone or Download** (Cloner ou Télécharger) pour télécharger depuis le référentiel GitHub. Le code de cette section se trouve dans `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Exécuter *mywebapi*
1. Ouvrez le projet `mywebapi` dans une *fenêtre distincte de Visual Studio*.
1. Sélectionnez **Azure Dev Spaces** dans la liste déroulante des paramètres de lancement comme vous l’avez déjà fait pour le projet `webfrontend`. Au lieu de créer un nouveau cluster ASK, sélectionnez celui que vous avez déjà créé. Comme avant, laissez le champ Space (Espace) sur `default` et cliquez sur **OK**. Dans la fenêtre Sortie, vous pouvez voir que Visual Studio commence à « préparer » ce nouveau service dans votre espace de développement afin d’accélérer les choses quand vous démarrer à déboguer.
1. Appuyez sur F5, et attendez la création et le déploiement du service. Lorsque la barre d’état de Visual Studio devient orange, le service est prêt.
1. Notez l’URL du point de terminaison qui s’affiche dans le volet **Azure Dev Spaces pour AKS** de la fenêtre **Output** (Résultat). Elle ressemble à ceci : http://localhost:\<portnumber\> Le conteneur a l’air de s’exécuter en local, mais en réalité, il s’exécute dans l’espace de développement dans Azure.
2. Lorsque `mywebapi` est prêt, ouvrez votre navigateur à l’adresse localhost et ajoutez `/api/values` à l’URL pour appeler l’API GET par défaut pour le `ValuesController`. 
3. Si toutes les étapes se sont déroulées correctement, vous pouvez voir une réponse du service `mywebapi` qui ressemble à ceci.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Effectuer une requête à partir de *webfrontend* sur *mywebapi*
Nous allons maintenant écrire du code dans `webfrontend` qui envoie une requête à `mywebapi`. Basculez vers la fenêtre Visual Studio qui contient le projet `webfrontend`. Dans le fichier `HomeController.cs`, *remplacez* le code de la méthode À propos de par le code suivant :

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

L’exemple de code précédent transfère l’en-tête `azds-route-as` de la requête entrante à la requête sortante. Vous verrez plus tard comment cela permet d’augmenter la productivité dans les scénarios d’équipe.

### <a name="debug-across-multiple-services"></a>Déboguer dans plusieurs services
1. À ce stade, `mywebapi` doit toujours être en cours d’exécution avec le débogueur joint. Si ce n’est pas le cas, appuyez sur F5 dans le projet `mywebapi`.
1. Définissez un point d’arrêt dans la méthode `Get(int id)` du fichier `Controllers/ValuesController.cs` qui gère les demandes GET `api/values/{id}`.
1. Dans le projet `webfrontend` où vous avez collé le code ci-dessus, définissez un point d’arrêt juste avant l’envoi d’une requête GET à `mywebapi/api/values`.
1. Appuyez sur F5 dans le projet `webfrontend`. Visual Studio ouvre à nouveau un navigateur sur le port localhost approprié et l’application web s’affiche.
1. Cliquez sur le lien « **About** » (À propos de) en haut de la page pour déclencher le point d’arrêt dans le projet `webfrontend`. 
1. Appuyez sur F10 pour continuer. Le point d’arrêt dans le projet `mywebapi` est maintenant déclenché.
1. Appuyez sur F5 pour continuer et vous êtes redirigé dans le code du projet `webfrontend`.
1. En appuyant encore une fois sur F5, vous terminez la requête et renvoyez une page dans le navigateur. Dans l’application web, la page À propos de affiche un message concaténé provenant des deux services : « Hello from webfrontend and Hello from mywebap ».

C’est terminé ! Vous disposez maintenant d’une application à plusieurs conteneurs où chaque conteneur peut être développé et déployé séparément.

## <a name="learn-about-team-development"></a>Découvrir le développement en équipe

Jusqu'à présent, vous avez exécuté le code de votre application comme si vous étiez le seul développeur à travailler sur l’application. Dans cette section, vous allez apprendre comment Azure Dev Spaces simplifie le développement en équipe :
* Permettre à une équipe de développeurs de travailler dans le même environnement, dans un espace de développement partagé ou dans des espaces de développement distincts en fonction des besoins...
* Prendre en charge chaque développeur qui fait des itérations sur son code de manière isolée, sans risque de compromettre le travail des autres.
* Tester le code de bout en bout, avant la validation du code, sans avoir à créer des versions fictives ou à simuler des dépendances.

### <a name="challenges-with-developing-microservices"></a>Les problèmes liés au développement des microservices
Votre exemple d’application n’est pas très complexe pour le moment. Mais lorsque vous créerez une véritable application, les problèmes ne tarderont pas à émerger à mesure que vous ajouterez des services et que l’équipe de développement se développera.

Imaginez que vous travaillez sur un service qui interagit avec des dizaines d’autres services.

- Difficile d’imaginer exécuter tous les éléments en local pour le développement. Votre ordinateur de développement risque de ne pas avoir suffisamment de ressources pour exécuter l’application complète. Ou peut-être que votre application possède des points de terminaison qui doivent être publiquement accessibles (par exemple, votre application répond au Webhook d’une application SaaS).
- Vous pouvez essayer de n’exécuter que les services dont vous avez besoin, mais pour cela vous devriez d’abord être sûr que toutes les dépendances sont closes (y compris les dépendances de dépendances). Vous pouvez aussi avoir des problèmes pour la création et l’exécution de vos dépendances simplement parce que vous n’avez pas travaillé sur celles-ci.
- Certains développeurs ont recours à la simulation ou à des objets fictifs, pour la plupart de leurs dépendances de service. Si cette technique peut parfois se révéler utile, la gestion de ces objets fictifs peut rapidement demander du temps. En outre, avec cette technique, votre environnement de développement sera différent de l’environnement de production et des bogues risquent d’apparaître.
- Ensuite, il devient difficile d’effectuer des tests de bout en bout, quels qu’ils soient. Les tests d’intégration ne peuvent être effectués qu’après validation, ce qui signifie que vous verrez les problèmes plus tard dans le cycle de développement.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Travailler dans un espace de développement partagé
Avec Azure Dev Spaces, vous pouvez configurer un espace de développement *partagé* dans Azure. Chaque développeur peut se concentrer sur sa partie de l’application et peut développer de manière itérative du *code avant validation* dans un espace de développement qui contient déjà tous les autres services et ressources de cloud nécessaires à leurs scénarios. Les dépendances sont toujours à jour et la façon de travailler des développeurs reflète celle des équipes de production.

### <a name="work-in-your-own-space"></a>Travailler dans votre propre espace de travail
Lorsque vous développez le code de votre service, avant qu’il soit prêt à être archivé, il n’est pas en très bon état. Vous le remaniez de manière itérative, vous le testez et vous essayez plusieurs solutions. Azure Dev Spaces propose le concept **d’espace**, qui vous permet de travailler de manière isolée, sans risquer de compromettre le travail des autres membres de votre équipe.

Effectuez les étapes suivantes pour vous assurer que vos services `webfrontend` et `mywebapi` s’exécutent tous les deux **dans l’espace de développement `default`**.
1. Fermez toutes les sessions F5/de débogage des deux services, en conservant les projets ouverts dans leur fenêtre Visual Studio.
2. Basculez vers la fenêtre Visual Studio contenant le projet `mywebapi` et appuyez sur Ctrl + F5 pour exécuter le service sans le débogueur joint.
3. Basculez vers la fenêtre Visual Studio contenant le projet `webfrontend` et appuyez sur Ctrl + F5 pour l’exécuter également.

> [!Note]
> Il est parfois nécessaire d’actualiser votre navigateur après l’affichage de la page web suite à l’action Ctrl + F5.

Toute personne qui ouvre l’URL publique et accède à l’application web appelle le chemin d’accès du code que vous avez écrit et qui s’exécute via les deux services à l’aide de l’espace par défaut `default`. Supposons maintenant que vous souhaitiez continuer à développer `mywebapi`. Comment procéder sans interrompre les autres développeurs qui utilisent l’espace de développement ? Pour ce faire, vous allez configurer votre propre espace.

### <a name="create-a-new-dev-space"></a>Créer un espace de développement
Dans Visual Studio, vous pouvez créer des espaces supplémentaires qui seront utilisés lorsque vous appuierez sur F5 ou Ctrl + F5 dans votre service. Vous pouvez donner n’importe quel nom à votre espace et vous pouvez choisir sa signification (par exemple `sprint4` ou `demo`).

Procédez comme suit pour créer un nouvel espace :
1. Basculez vers la fenêtre Visual Studio qui contient le projet `mywebapi`.
2. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Propriétés**.
3. Sélectionnez l’onglet **Debug** (Déboguer) sur la gauche pour afficher les paramètres d’Azure Dev Spaces.
4. Ici, vous pouvez modifier ou créer le cluster et/ou l’espace qui sera utilisé lorsque vous appuyez sur F5 ou Ctrl + F5. *Assurez-vous que l’espace de développement Azure que vous avez créé précédemment est sélectionné*.
5. Dans la liste déroulante des espaces, sélectionnez **<Create New Space…>** (<Créer nouvel espace...>).

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. Dans la boîte de dialogue **Add Space** (Ajouter un espace), entrez le nom de l’espace et cliquez sur **OK**. Vous pouvez utiliser votre nom pour le nouvel espace (par exemple « Scott ») pour que vos collègues sachent que c’est le vôtre.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Vous devriez maintenant voir votre cluster AKS et un nouvel espace sélectionné dans la page des propriétés du projet.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Mettre à jour du code pour *mywebapi*

1. Dans le projet `mywebapi`, modifiez le code dans la méthode `string Get(int id)` du fichier `Controllers/ValuesController.cs` comme suit :
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Définissez un point d’arrêt dans ce bloc de code mis à jour (vous en avez peut-être déjà défini un auparavant).
3. Appuyez sur F5 pour démarrer le service `mywebapi`. Cette action démarre le service dans votre cluster en utilisant l’espace sélectionné, `scott` dans le cas présent.

Voici un diagramme qui vous aidera à comprendre comment les différents espaces fonctionnent. Le chemin violet indique une requête via l’espace `default`, qui est le chemin par défaut utilisé si aucun espace n’est ajouté à l’URL. Le chemin rose indique une requête via l’espace `default/scott`.

![](media/common/Space-Routing.png)

Cette fonctionnalité intégrée à Azure Dev Spaces vous permet de tester le code de bout en bout dans un environnement partagé sans que chaque développeur n’ait à recréer la pile de services complète dans son espace. Ce routage nécessite que les en-têtes de propagation soient transférés dans le code de votre application, comme illustré dans l’étape précédente de ce guide.

### <a name="test-code-running-in-the-defaultscott-space"></a>Tester le code qui s’exécute dans l’espace `default/scott`
Pour tester votre nouvelle version de `mywebapi` avec `webfrontend`, ouvrez votre navigateur et accédez à l’URL du point d’accès public de `webfrontend` (par exemple http://webfrontend.123456abcdef.eastus.aksapp.io)), puis accédez à la page À propos de. Vous devez y voir le message d’origine « Hello from webfrontend and Hello from mywebap ».

Maintenant, ajoutez « scott.s » à l’URL pour qu’elle ressemble à quelque chose comme ceci http://scott.s.webfrontend.123456abcdef.eastus.aksapp.io, puis actualisez le navigateur. Vous devriez atteindre le point d’arrêt que vous avez défini dans votre projet `mywebapi`. Appuyez sur F5 pour continuer et dans votre navigateur, vous devriez maintenant voir le nouveau message « Hello from webfrontend » et mywebapi affiche maintenant autre chose. C’est parce que le chemin d’accès de votre code mis à jour dans `mywebapi` est en cours d’exécution l’espace `default/scott`.

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
