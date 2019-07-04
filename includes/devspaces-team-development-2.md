---
title: Fichier Include
description: Fichier Include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: e0f768b876b49ec006ce98decf121d73d334b6d8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177502"
---
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

Notez que l’URL de point d’accès public de *webfrontend* comporte le préfixe *scott.s*. Cette URL est unique dans l’espace _dev/scott_. Ce préfixe d’URL indique au contrôleur d’entrée qu’il doit router les requêtes vers la version _dev/scott_ d’un service. Lorsqu’une requête avec cette URL est gérée par Dev Spaces, le contrôleur d’entrée tente d’abord de router la requête vers le service *webfrontend* dans l’espace _dev/scott_. Si cette tentative échoue, la requête sera routée vers le service *webfrontend* dans l’espace _dev_ comme solution de secours. Notez également qu’une URL localhost permet d’accéder au service via le localhost à l’aide de la fonctionnalité *port-forward* de Kubernetes. Pour plus d’informations sur les URL et le routage dans Azure Dev Spaces, consultez [Fonctionnement et configuration d’Azure Dev Spaces](../articles/dev-spaces/how-dev-spaces-works.md).



![Routage de l’espace](../articles/dev-spaces/media/common/Space-Routing.png)

Cette fonctionnalité intégrée d’Azure Dev Spaces permet de tester du code dans un environnement partagé, sans que chaque développeur ait à recréer toute la pile de services dans son espace. Ce routage nécessite que le code de votre application transmette les en-têtes de propagation, comme indiqué à l’étape précédente de ce guide.

### <a name="test-code-in-a-space"></a>Tester le code dans un espace
Pour tester votre nouvelle version de *mywebapi* avec *webfrontend*, ouvrez votre navigateur et accédez à l’URL du point d’accès public de *webfronted*, puis à la page À propos de. Votre nouveau message doit être affiché.

Maintenant, supprimez la partie « scott.s » de l’URL, puis actualisez le navigateur. Vous devriez revenir à l’ancien comportement (la version *mywebapi* s’exécute dans _dev_).

Une fois que vous disposerez d’un espace _dev_ contenant en permanence les dernières modifications, et si votre application est conçue pour tirer parti d’un routage DevSpace basé sur un espace, comme décrit dans cette section du tutoriel, vous vous rendrez compte à quel point Dev Spaces facilite les tests des nouvelles fonctionnalités dans le contexte d’une application plus grande. Au lieu de déployer _tous_ les services dans votre espace privé, vous pouvez créer un espace privé dérivé de _dev_, puis « activer » uniquement les services que vous utilisez réellement. L’infrastructure de routage Dev Spaces gère le reste en utilisant tous les services issus de votre espace privé qu’elle peut trouver, tout en utilisant par défaut la dernière version actuellement exécutée dans l’espace _dev_. Mieux encore, _plusieurs_ développeurs peuvent développer différents services simultanément dans leur propre espace, sans s’interrompre mutuellement.

### <a name="well-done"></a>C’est terminé !
Vous êtes arrivé au terme du guide de démarrage ! Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Configurer Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
> * Développer du code de façon itérative dans des conteneurs.
> * Développer indépendamment deux services distincts et utiliser la découverte des services DNS de Kubernetes pour appeler un autre service.
> * Développer et tester votre code de façon productive dans un environnement d’équipe.
> * Établir une base de référence de fonctionnalités à l’aide de Dev Spaces pour tester facilement les modifications isolées dans le contexte d’une application de microservice plus grande

Maintenant que vous avez exploré Azure Dev Spaces, [partagez votre espace de développement avec les membres de l’équipe](../articles/dev-spaces/how-to/share-dev-spaces.md) commencez à collaborer.

## <a name="clean-up"></a>Nettoyer
Pour supprimer complètement une instance d’Azure Dev Spaces sur un cluster, dont tous les espaces de développement et les services en cours d’exécution qu’elle contient, utilisez la commande `az aks remove-dev-spaces`. N’oubliez pas que cette action est irréversible. Vous pouvez ajouter à nouveau la prise en charge d’Azure Dev Spaces sur le cluster, mais il sera comme si vous recommenciez. Vos anciens services et espaces ne sont pas restaurés.

L’exemple suivant répertorie les contrôleurs Azure Dev Spaces de votre abonnement actif, puis supprime le contrôleur Azure Dev Spaces qui est associé au cluster AKS « myaks » dans le groupe de ressources « myaks-rg ».

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```