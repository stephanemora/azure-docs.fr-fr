---
title: Développement en équipe à l’aide de .NET Core et Visual Studio
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Ce tutoriel vous montre comment utiliser Azure Dev Spaces et Visual Studio pour développer en équipe une application .NET Core dans Azure Kubernetes Service.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s '
ms.openlocfilehash: 5d917dc71ef02b5197ed8d20ec31c538a1af4c14
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072974"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>Développement en équipe à l’aide de .NET Core et Visual Studio avec Azure Dev Spaces

Dans ce tutoriel, vous allez voir comment les développeurs d’une même équipe peuvent travailler simultanément sur un même cluster Kubernetes à l’aide de Dev Spaces.

## <a name="learn-about-team-development"></a>Découvrir le développement en équipe

Jusqu'à présent, vous avez exécuté le code de votre application comme si vous étiez le seul développeur à travailler sur l’application. Dans cette section, vous allez apprendre comment Azure Dev Spaces simplifie le développement en équipe :
* Permettre à une équipe de développeurs de travailler dans le même environnement, dans un espace de développement partagé ou dans des espaces de développement distincts en fonction des besoins.
* Prendre en charge chaque développeur qui fait des itérations sur son code de manière isolée, sans risque de compromettre le travail des autres.
* Tester le code de bout en bout, avant la validation du code, sans avoir à créer des versions fictives ou à simuler des dépendances.

### <a name="challenges-with-developing-microservices"></a>Les problèmes liés au développement des microservices
Votre exemple d’application n’est pas complexe pour le moment. Mais lorsque vous créerez une véritable application, les problèmes ne tarderont pas à émerger à mesure que vous ajouterez des services et que l’équipe de développement se développera.

* Votre ordinateur de développement peut ne pas avoir suffisamment de ressources pour exécuter simultanément tous les services dont vous avez besoin.
* Certains services peuvent nécessiter un accès public. Par exemple, un service peut avoir besoin d’un point de terminaison répondant à un webhook.
* Si vous souhaitez exécuter une partie des services, vous devez connaître la hiérarchie complète des dépendances qui existent entre tous vos services. Il peut être difficile d’établir cette hiérarchie, en particulier lorsque le nombre de vos services augmente.
* Certains développeurs ont recours à la simulation ou à des objets fictifs, pour la plupart de leurs dépendances de service. Cette approche peut aider, mais la gestion de ces objets fictifs peut rapidement impacter les coûts de développement. En outre, cette approche entraîne des différences entre votre environnement de développement et votre environnement de production, ce qui peut provoquer des bogues subtils.
* Il s’ensuit que les tests d’intégration, quels qu’ils soient, s’avèrent alors difficiles. Les tests d’intégration ne peuvent être effectués qu’après validation, ce qui signifie que vous verrez les problèmes plus tard dans le cycle de développement.

    ![Image illustrant les relations entre un service d’application et ses dépendances pour montrer la complexité du test d’intégration.](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Travailler dans un espace de développement partagé
Avec Azure Dev Spaces, vous pouvez configurer un espace de développement *partagé* dans Azure. Chaque développeur peut se concentrer sur sa partie de l’application et peut développer de manière itérative du *code avant validation* dans un espace de développement qui contient déjà tous les autres services et ressources de cloud nécessaires à leurs scénarios. Les dépendances sont toujours à jour et la façon de travailler des développeurs reflète celle des équipes de production.

### <a name="work-in-your-own-space"></a>Travailler dans votre propre espace de travail
Lorsque vous développez le code de votre service, avant qu’il soit prêt à être archivé, il n’est pas en très bon état. Vous le remaniez de manière itérative, vous le testez et vous essayez plusieurs solutions. Azure Dev Spaces propose le concept **d’espace**, qui vous permet de travailler de manière isolée, sans risquer de compromettre le travail des autres membres de votre équipe.

## <a name="use-dev-spaces-for-team-development"></a>Utiliser Dev Spaces pour le développement en équipe
Nous allons illustrer ces idées avec un exemple concret, qui est celui de l’exemple d’application *webfrontend* -> *mywebapi*. Nous allons imaginer un scénario dans lequel un développeur, Scott, doit apporter une modification au service *mywebapi*, et *uniquement* à ce service. La mise à jour de Scott ne nécessite pas que le service *webfrontend* soit modifié.

_Sans_ Dev Spaces, Scott disposerait d’autres moyens de développer et de tester sa mise à jour, mais aucun ne serait idéal :
* Exécuter tous les composants localement, ce qui nécessite un ordinateur de développement plus puissant, sur lequel est installé Docker et éventuellement MiniKube
* Exécuter TOUS les composants dans un espace de noms isolé du cluster Kubernetes. Dans la mesure où *webfrontend* n’est pas modifié, l’utilisation d’un espace de noms isolé constitue un gaspillage des ressources de cluster.
* Exécuter UNIQUEMENT *mywebapi* et effectuer des appels REST manuellement à des fins de test. Ce type de test ne permet pas de tester le flux de bout en bout.
* Ajouter du code axé sur le développement à *webfrontend* qui permette au développeur d’envoyer des requêtes vers une autre instance de *mywebapi*. L’ajout de ce code complique le service *webfrontend*.

### <a name="set-up-your-baseline"></a>Configurer votre base de référence
Tout d’abord, nous allons devoir déployer une base de référence de nos services. Ce déploiement va représenter la « dernière configuration valide connue ». Vous pourrez donc comparer facilement le comportement de votre code local à celui de la version archivée. Nous allons ensuite créer un espace enfant basé sur cette base de référence, afin de tester les modifications apportées à *mywebapi* dans le contexte d’une application plus grande.

1. Clonez l’[exemple d’application Dev Spaces](https://github.com/Azure/dev-spaces) : `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Extrayez la branche distante *azds_updates* : `git checkout -b azds_updates origin/azds_updates`
1. Fermez toutes les sessions F5/de débogage des deux services, en conservant les projets ouverts dans leur fenêtre Visual Studio.
1. Basculez vers la fenêtre Visual Studio qui contient le projet _mywebapi_.
1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Propriétés**.
1. Sélectionnez l’onglet **Debug** (Déboguer) sur la gauche pour afficher les paramètres d’Azure Dev Spaces.
1. Sélectionnez **Modifier** pour créer l’espace qui doit être utilisé lorsque vous aurez appuyé sur les touches F5 ou Ctrl + F5 pour ce service.
1. Dans la liste déroulante Espace, sélectionnez **\<Create New Space…\>** .
1. Vérifiez que l’espace parent est défini sur **\<none\>** , puis entrez le nom **dev** pour l’espace. Cliquez sur OK.
1. Appuyez sur Ctrl + F5 pour exécuter _mywebapi_ sans le débogueur attaché.
1. Basculez vers la fenêtre Visual Studio contenant le projet _webfrontend_, puis appuyez sur Ctrl + F5 pour l’exécuter également.

> [!Note]
> Il est parfois nécessaire d’actualiser votre navigateur après l’affichage de la page web suite à l’action Ctrl + F5.

> [!TIP]
> Les étapes ci-dessus permettent de configurer manuellement une base de référence. Toutefois, il est recommandé aux équipes d’utiliser CI/CD pour maintenir leur base de référence à jour automatiquement à l’aide d’un code validé.
>
> Découvrez notre [guide de configuration de CI/CD avec Azure DevOps](how-to/setup-cicd.md) pour créer un workflow similaire à celui du diagramme suivant.
>
> ![Exemple de diagramme CI/CD](media/common/ci-cd-complex.png)

Toute personne qui ouvre l’URL publique et accède à l’application web appelle le chemin de code que vous avez écrit et qui s’exécute via les deux services à l’aide de l’espace par défaut _dev_. Supposons maintenant que vous souhaitiez continuer à développer *mywebapi*. Comment procéder sans interrompre les autres développeurs qui utilisent l’espace de développement ? Pour ce faire, vous allez configurer votre propre espace.

### <a name="create-a-new-dev-space"></a>Créer un espace de développement
Dans Visual Studio, vous pouvez créer des espaces supplémentaires qui seront utilisés lorsque vous appuierez sur F5 ou Ctrl + F5 dans votre service. Vous pouvez donner n’importe quel nom à votre espace et vous pouvez choisir sa signification (par exemple _sprint4_ ou _demo_).

Procédez comme suit pour créer un nouvel espace :
1. Basculez vers la fenêtre Visual Studio qui contient le projet *mywebapi*.
2. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet et sélectionnez **Propriétés**.
3. Sélectionnez l’onglet **Debug** (Déboguer) sur la gauche pour afficher les paramètres d’Azure Dev Spaces.
4. Ici, vous pouvez modifier ou créer le cluster et/ou l’espace qui sera utilisé lorsque vous appuyez sur F5 ou Ctrl + F5. *Assurez-vous que l’espace de développement Azure que vous avez créé précédemment est sélectionné*.
5. Dans la liste déroulante Espace, sélectionnez **\<Create New Space…\>** .

    ![Capture d’écran montrant la sélection de l’option « Créer un espace » dans la liste déroulante Espace de la section Déboguer de la page de propriétés du projet dans l’Explorateur de solutions Visual Studio](media/get-started-netcore-visualstudio/Settings.png)

6. Dans la boîte de dialogue **Ajouter un espace**, définissez l’espace parent sur **dev**, puis saisissez un nom pour le nouvel espace. Vous pouvez utiliser votre nom pour le nouvel espace (par exemple « Scott ») pour que vos collègues sachent que c’est le vôtre. Cliquez sur **OK**.

    ![Capture d’écran montrant la boîte de dialogue Ajouter un espace pour l’ajout d’un nouvel espace de développement en vue d’un développement en équipe](media/get-started-netcore-visualstudio/AddSpace.png)

7. Vous devriez maintenant voir votre cluster AKS et un nouvel espace sélectionné dans la page des propriétés du projet.

    ![Capture d’écran montrant le cluster AKS « MyAKS » et l’espace « scott » sélectionnés dans la section Déboguer de la page de propriétés du projet dans l’Explorateur de solutions Visual Studio](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Mettre à jour du code pour *mywebapi*

1. Dans le projet *mywebapi*, modifiez le code de la méthode `string Get(int id)` du fichier `Controllers/ValuesController.cs` de la façon :
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Définissez un point d’arrêt dans ce bloc de code mis à jour (vous en avez peut-être déjà défini un auparavant).
3. Appuyez sur F5 pour démarrer le service _mywebapi_, ce qui va démarrer le service dans votre cluster à l’aide de l’espace sélectionné. L’espace sélectionné dans ce cas est _scott_.

Voici un diagramme qui vous aidera à comprendre comment les différents espaces fonctionnent. Le chemin violet indique une requête via l’espace _dev_, qui est le chemin par défaut utilisé si aucun espace n’est ajouté à l’URL. Le chemin rose indique une requête via l’espace _dev/scott_.

![Diagramme montrant les différences de noms de chemins et le routage des requêtes dans le nouvel espace « dev/scott » et l’espace « dev » par défaut.](media/common/Space-Routing.png)

Cette fonctionnalité intégrée à Azure Dev Spaces vous permet de tester le code de bout en bout dans un environnement partagé sans que chaque développeur n’ait à recréer la pile de services complète dans son espace. Ce routage nécessite que les en-têtes de propagation soient transférés dans le code de votre application, comme illustré dans l’étape précédente de ce guide.

### <a name="test-code-running-in-the-_devscott_-space"></a>Tester le code qui s’exécute dans l’espace _dev/scott_
Pour tester votre nouvelle version de *mywebapi* avec *webfrontend*, ouvrez votre navigateur et accédez à l’URL du point d’accès public de *webfrontend* (par exemple, `http://dev.webfrontend.123456abcdef.eus.azds.io`), puis accédez à la page À propos de. Vous devez y voir le message d’origine « Hello from webfrontend and Hello from mywebap ».

Maintenant, ajoutez « scott.s » de l’URL de sorte qu’elle indique quelque chose comme http\://scott.s.dev.webfrontend.123456abcdef.eus.azds.io et actualisez le navigateur. Vous devriez atteindre le point d’arrêt que vous avez défini dans votre projet *mywebapi*. Appuyez sur F5 pour continuer et dans votre navigateur, vous devriez maintenant voir le nouveau message « Hello from webfrontend » et mywebapi affiche maintenant autre chose. Cela est dû au fait que le chemin de votre code mis à jour dans *mywebapi* est exécuté dans l’espace _dev/scott_.

Une fois que vous disposerez d’un espace _dev_ contenant en permanence les dernières modifications, et si votre application est conçue pour tirer parti d’un routage DevSpace basé sur un espace, comme décrit dans cette section du tutoriel, vous vous rendrez compte à quel point Dev Spaces aide à tester les nouvelles fonctionnalités dans le contexte d’une application de taille plus importante. Au lieu de déployer _tous_ les services dans votre espace privé, vous pouvez créer un espace privé dérivé de _dev_, puis « activer » uniquement les services que vous utilisez réellement. L’infrastructure de routage Dev Spaces gère le reste en utilisant tous les services issus de votre espace privé qu’elle peut trouver, tout en utilisant par défaut la dernière version actuellement exécutée dans l’espace _dev_. Mieux encore, _plusieurs_ développeurs peuvent développer différents services simultanément dans leur propre espace, sans s’interrompre mutuellement.

### <a name="well-done"></a>C’est terminé !
Vous êtes arrivé au terme du guide de démarrage ! Vous avez appris à :

> [!div class="checklist"]
> * Configurer Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
> * Développer du code de façon itérative dans des conteneurs.
> * Développer indépendamment deux services distincts et utiliser la découverte des services DNS de Kubernetes pour appeler un autre service.
> * Développer et tester votre code de façon productive dans un environnement d’équipe.
> * Établir une base de référence de fonctionnalités à l’aide de Dev Spaces pour tester facilement les modifications isolées dans le contexte d’une application de microservice plus grande

Maintenant que vous avez exploré Azure Dev Spaces, [partagez votre espace de développement avec les membres de l’équipe](how-to/share-dev-spaces.md) et montrez-leur combien il est facile de collaborer.

## <a name="clean-up"></a>Nettoyer
Pour supprimer complètement une instance d’Azure Dev Spaces sur un cluster, dont tous les espaces de développement et les services en cours d’exécution qu’elle contient, utilisez la commande `az aks remove-dev-spaces`. N’oubliez pas que cette action est irréversible. Vous pouvez ajouter à nouveau la prise en charge d’Azure Dev Spaces sur le cluster, mais il sera comme si vous recommenciez. Vos anciens services et espaces ne sont pas restaurés.

L’exemple suivant répertorie les contrôleurs Azure Dev Spaces de votre abonnement actif, puis supprime le contrôleur Azure Dev Spaces qui est associé au cluster AKS « myaks » dans le groupe de ressources « myaks-rg ».

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
