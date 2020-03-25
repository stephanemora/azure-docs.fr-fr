---
title: Développement en équipe à l’aide de Node.js et Visual Studio Code
services: azure-dev-spaces
ms.date: 07/09/2018
ms.topic: tutorial
description: Ce tutoriel vous montre comment utiliser Azure Dev Spaces et Visual Studio Code pour développer en équipe une application Node.js dans Azure Kubernetes Service.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s '
ms.openlocfilehash: abcf4934af056d508ac136f80758597294d40b1a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251934"
---
# <a name="team-development-using-nodejs-and-visual-studio-code-with-azure-dev-spaces"></a>Développement en équipe à l’aide de Node.js et Visual Studio Code avec Azure Dev Spaces

Dans ce tutoriel, vous allez voir comment les développeurs d’une même équipe peuvent travailler simultanément sur un même cluster Kubernetes à l’aide de Dev Spaces.

## <a name="learn-about-team-development"></a>Découvrir le développement en équipe
Jusqu’à présent, vous avez exécuté le code de votre application comme si vous étiez le seul développeur à travailler sur l’application. Dans cette section, vous allez apprendre comment Azure Dev Spaces simplifie le développement en équipe :
* Permettre à une équipe de développeurs de travailler dans le même environnement, dans un espace de développement partagé ou dans des espaces de développement distincts en fonction des besoins.
* Prendre en charge chaque développeur qui fait des itérations sur son code de manière isolée, sans risque de compromettre le travail des autres.
* Tester le code de bout en bout, avant la validation du code, sans avoir à créer des versions fictives ou à simuler des dépendances.

### <a name="challenges-with-developing-microservices"></a>Les problèmes liés au développement des microservices
Votre exemple d’application n’est pas très complexe pour le moment. Mais lorsque vous créerez une véritable application, les problèmes ne tarderont pas à émerger à mesure que vous ajouterez des services et que l’équipe de développement se développera. Difficile d’imaginer exécuter tous les éléments en local pour le développement.

* Votre ordinateur de développement peut ne pas avoir suffisamment de ressources pour exécuter simultanément tous les services dont vous avez besoin.
* Certains services peuvent nécessiter un accès public. Par exemple, un service peut avoir besoin d’un point de terminaison répondant à un webhook.
* Si vous souhaitez exécuter une partie des services, vous devez connaître la hiérarchie complète des dépendances qui existent entre tous vos services. Il peut être difficile d’établir cette hiérarchie, en particulier lorsque votre nombre de services augmente.
* Certains développeurs ont recours à la simulation ou à des objets fictifs, pour la plupart de leurs dépendances de service. Cette approche peut aider, mais la gestion de ces objets fictifs peut rapidement impacter les coûts de développement. De plus, cette approche amène votre environnement de développement à se différencier considérablement de celui de production, permettant ainsi à des bogues subtils de se glisser.
* Il s’ensuit que les tests d’intégration, quels qu’ils soient, s’avèrent alors difficiles. Les tests d’intégration ne peuvent être effectués qu’après validation, ce qui signifie que vous verrez les problèmes plus tard dans le cycle de développement.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Travailler dans un espace de développement partagé
Avec Azure Dev Spaces, vous pouvez configurer un espace de développement *partagé* dans Azure. Chaque développeur peut se concentrer sur sa partie de l’application et peut développer de manière itérative du *code avant validation* dans un espace de développement qui contient déjà tous les autres services et ressources de cloud nécessaires à leurs scénarios. Les dépendances sont toujours à jour et la façon de travailler des développeurs reflète celle des équipes de production.

### <a name="work-in-your-own-space"></a>Travailler dans votre propre espace de travail
Lorsque vous développez le code de votre service, avant qu’il soit prêt à être archivé, il n’est pas en très bon état. Vous le remaniez de manière itérative, vous le testez et vous essayez plusieurs solutions. Azure Dev Spaces propose le concept **d’espace**, qui vous permet de travailler de manière isolée, sans risquer de compromettre le travail des autres membres de votre équipe.

## <a name="use-dev-spaces-for-team-development"></a>Utiliser Dev Spaces pour le développement en équipe
Nous allons illustrer ces idées avec un exemple concret, qui est celui de l’exemple d’application *webfrontend* -> *mywebapi*. Nous allons imaginer un scénario dans lequel un développeur, Scott, doit apporter une modification au service *mywebapi*, et *uniquement* à ce service. La mise à jour de Scott ne nécessite pas que le service *webfrontend* soit modifié.

_Sans_ Dev Spaces, Scott disposerait d’autres moyens de développer et de tester sa mise à jour, mais aucun ne serait idéal :
* Exécuter TOUS les composants localement. Un ordinateur de développement plus puissant avec Docker et éventuellement MiniKube installés est donc nécessaire.
* Exécuter TOUS les composants dans un espace de noms isolé du cluster Kubernetes. Dans la mesure où *webfrontend* ne change pas, les ressources du cluster sont gaspillées.
* Exécuter UNIQUEMENT *mywebapi* et effectuer des appels REST manuellement à des fins de test. Le flux de bout en bout complet n’est alors pas testé.
* Ajouter du code axé sur le développement à *webfrontend* qui permette au développeur d’envoyer des requêtes vers une autre instance de *mywebapi*. Le service *webfrontend* est alors plus compliqué.

### <a name="set-up-your-baseline"></a>Configurer votre base de référence
Tout d’abord, nous allons devoir déployer une base de référence de nos services. Ce déploiement va représenter la « dernière configuration valide connue ». Vous pourrez donc comparer facilement le comportement de votre code local à celui de la version archivée. Nous allons ensuite créer un espace enfant basé sur cette base de référence, afin de tester les modifications apportées à *mywebapi* dans le contexte d’une application plus grande.

1. Clonez l’[exemple d’application Dev Spaces](https://github.com/Azure/dev-spaces) : `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Extrayez la branche distante *azds_updates* : `git checkout -b azds_updates origin/azds_updates`
1. Sélectionnez l’espace _dev_ : `azds space select --name dev`. Quand vous êtes invité à sélectionner un espace dev parent, sélectionnez _\<aucun\>_ .
1. Accédez au répertoire _mywebapi_ et exécutez : `azds up -d`
1. Accédez au répertoire _webfrontend_ et exécutez : `azds up -d`
1. Exécutez `azds list-uris` pour voir le point de terminaison public de _webfrontend_

> [!TIP]
> Les étapes ci-dessus permettent de configurer manuellement une base de référence. Toutefois, il est recommandé aux équipes d’utiliser CI/CD pour maintenir leur base de référence à jour automatiquement à l’aide d’un code validé.
>
> Découvrez notre [guide de configuration de CI/CD avec Azure DevOps](how-to/setup-cicd.md) pour créer un workflow similaire à celui du diagramme suivant.
>
> ![Exemple de diagramme CI/CD](media/common/ci-cd-complex.png)

À ce stade, votre base de référence doit être en cours d’exécution. Exécutez la commande `azds list-up --all`. La sortie ressemble à ce qui suit :

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

La colonne DevSpace indique que les deux services s’exécutent dans un espace nommé _dev_. Toute personne qui ouvre l’URL publique et accède à l’application web appelle le chemin du code archivé qui s’exécute par le biais des deux services. Maintenant, supposons que vous souhaitez continuer à développer _mywebapi_. Comment apporter des modifications au code, puis les tester sans interrompre les autres développeurs qui utilisent l’environnement de développement ? Pour ce faire, vous allez configurer votre propre espace.

### <a name="create-a-dev-space"></a>Créer un espace de développement
Pour exécuter votre propre version de _mywebapi_ dans un espace autre que _dev_, vous pouvez créer votre propre espace à l’aide de la commande suivante :

```cmd
azds space select --name scott
```

Quand vous y êtes invité, sélectionnez _dev_ comme **espace de développement parent**. Cela signifie que notre nouvel espace, _dev/scott_, va dériver de l’espace _dev_. Nous verrons bientôt comment cela va nous aider lors des tests.

Conformément à notre hypothèse de départ, nous avons utilisé le nom _scott_ pour le nouvel espace afin que ses homologues puissent identifier qui travaille dans celui-ci. Mais vous pouvez lui donner n’importe quel nom et faire montre de souplesse dans sa signification, comme _sprint4_ ou _démo_. Quoi qu’il en soit, _dev_ sert de base de référence à tous les développeurs qui travaillent sur une partie de cette application :

![](media/common/ci-cd-space-setup.png)

Exécutez la commande `azds space list` pour afficher une liste de tous les espaces dans l’environnement de développement. La colonne _Sélectionné_ indique l’espace que vous avez actuellement sélectionné (true/false). Dans votre cas, l’espace nommé _dev/scott_ a été automatiquement sélectionné lors de sa création. Vous pouvez sélectionner un autre espace à tout moment avec la commande `azds space select`.

Découvrons-le en action.

### <a name="make-a-code-change"></a>Modifier le code
Accédez à la fenêtre VS Code pour `mywebapi` et modifiez le code du gestionnaire GET `/` par défaut dans `server.js`, par exemple :

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

### <a name="run-the-service"></a>Exécuter le service

Pour exécuter le service, appuyez sur F5 (ou tapez `azds up` dans la fenêtre du terminal). Le service s’exécute automatiquement dans le nouvel espace sélectionné _dev/scott_. Vérifiez que votre service s’exécute dans son propre espace en réexécutant `azds list-up` :

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Remarquez qu’une instance de *mywebapi* est maintenant en cours d’exécution dans l’espace _dev/scott_. La version en cours d’exécution dans _dev_ est toujours exécutée, mais elle n’est pas listée.

Listez les URL de l’espace actuel en exécutant `azds list-uris`.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Notez que l’URL de point d’accès public de *webfrontend* comporte le préfixe *scott.s*. Cette URL est unique dans l’espace _dev/scott_. Ce préfixe d’URL indique au contrôleur d’entrée qu’il doit router les requêtes vers la version _dev/scott_ d’un service. Lorsqu’une requête avec cette URL est gérée par Dev Spaces, le contrôleur d’entrée tente d’abord de router la requête vers le service *webfrontend* dans l’espace _dev/scott_. Si cette tentative échoue, la requête sera routée vers le service *webfrontend* dans l’espace _dev_ comme solution de secours. Notez également qu’une URL localhost permet d’accéder au service via le localhost à l’aide de la fonctionnalité *port-forward* de Kubernetes. Pour plus d’informations sur les URL et le routage dans Azure Dev Spaces, consultez [Fonctionnement et configuration d’Azure Dev Spaces](how-dev-spaces-works.md).

![Routage de l’espace](media/common/Space-Routing.png)

Cette fonctionnalité intégrée d’Azure Dev Spaces permet de tester du code dans un environnement partagé, sans que chaque développeur ait à recréer toute la pile de services dans son espace. Ce routage nécessite que le code de votre application transmette les en-têtes de propagation, comme indiqué à l’étape précédente de ce guide.

### <a name="test-code-in-a-space"></a>Tester le code dans un espace
Pour tester votre nouvelle version de *mywebapi* avec *webfrontend*, ouvrez votre navigateur et accédez à l’URL du point d’accès public de *webfronted*, puis à la page À propos de. Votre nouveau message doit être affiché.

Maintenant, supprimez la partie « scott.s » de l’URL, puis actualisez le navigateur. Vous devriez revenir à l’ancien comportement (la version *mywebapi* s’exécute dans _dev_).

Une fois que vous disposerez d’un espace _dev_ contenant en permanence les dernières modifications, et si votre application est conçue pour tirer parti d’un routage DevSpace basé sur un espace, comme décrit dans cette section du tutoriel, vous vous rendrez compte à quel point Dev Spaces facilite les tests des nouvelles fonctionnalités dans le contexte d’une application plus grande. Au lieu de déployer _tous_ les services dans votre espace privé, vous pouvez créer un espace privé dérivé de _dev_, puis « activer » uniquement les services que vous utilisez réellement. L’infrastructure de routage Dev Spaces gère le reste en utilisant tous les services issus de votre espace privé qu’elle peut trouver, tout en utilisant par défaut la dernière version actuellement exécutée dans l’espace _dev_. Mieux encore, _plusieurs_ développeurs peuvent développer différents services simultanément dans leur propre espace, sans s’interrompre mutuellement.

### <a name="well-done"></a>C’est terminé !
Vous êtes arrivé au terme du guide de démarrage ! Vous avez appris à :

> [!div class="checklist"]
> * Configurer Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
> * Développer du code de façon itérative dans des conteneurs.
> * Développer indépendamment deux services distincts et utiliser la découverte des services DNS de Kubernetes pour appeler un autre service.
> * Développer et tester votre code de façon productive dans un environnement d’équipe.
> * Établir une base de référence de fonctionnalités à l’aide de Dev Spaces pour tester facilement les modifications isolées dans le contexte d’une application de microservice plus grande

Maintenant que vous avez exploré Azure Dev Spaces, [partagez votre espace de développement avec les membres de l’équipe](how-to/share-dev-spaces.md) commencez à collaborer.

## <a name="clean-up"></a>Nettoyer
Pour supprimer complètement une instance d’Azure Dev Spaces sur un cluster, dont tous les espaces de développement et les services en cours d’exécution qu’elle contient, utilisez la commande `az aks remove-dev-spaces`. N’oubliez pas que cette action est irréversible. Vous pouvez ajouter à nouveau la prise en charge d’Azure Dev Spaces sur le cluster, mais il sera comme si vous recommenciez. Vos anciens services et espaces ne sont pas restaurés.

L’exemple suivant répertorie les contrôleurs Azure Dev Spaces de votre abonnement actif, puis supprime le contrôleur Azure Dev Spaces qui est associé au cluster AKS « myaks » dans le groupe de ressources « myaks-rg ».

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
