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
ms.openlocfilehash: 749120446619bf682d02be0f9290a6d47540c16a
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55664367"
---
### <a name="run-the-service"></a>Exécuter le service

1. Appuyez sur F5 (ou tapez `azds up` dans la fenêtre du terminal) pour exécuter le service. Le service s’exécute automatiquement dans le nouvel espace sélectionné _dev/scott_. 
1. Vous pouvez vérifier que votre service s’exécute dans son propre espace en réexécutant `azds list-up`. Vous remarquez qu’une instance de *mywebapi* est en cours d’exécution dans l’espace _dev/scott_ (la version exécutée dans l’espace _dev_ reste active mais n’est pas listée).

    ```
    Name                      DevSpace  Type     Updated  Status
    mywebapi                  scott     Service  3m ago   Running
    mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
    webfrontend               dev       Service  26m ago  Running
    ```

1. Exécutez `azds list-uris` et remarquez l’URL du point d’accès de *webfrontend*.

    ```
    Uri                                                                        Status
    -------------------------------------------------------------------------  ---------
    http://localhost:53831 => mywebapi.scott:80                                Tunneled
    http://dev.s.dev.webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
    ```

1. Remplacez le préfixe d’URL « dev.s » par « scott.s » dans votre navigateur web. Remarquez que cette URL mise à jour se résout quand même. Cette URL est unique dans l’espace _dev/scott_. Cette URL spéciale signifie que les requêtes envoyées à l’« URL de Scott » vont d’abord tenter d’accéder aux services de l’espace _dev/scott_ ; en cas d’échec, elles reviendront aux services de l’espace _dev_.

<!--
TODO: replace 2 & 3 with below once bug#753164 and PR#158827 get pushed to production.

You can confirm that your service is running in its own space by running `azds list-up` again. First, you'll notice an instance of *mywebapi* is now running in the _dev/scott_ space (the version running in _dev_ is still running but it is not listed). If you run `azds list-uris`, you will notice that the access point URL for *webfrontend* is prefixed with the text "scott.s.". This URL is unique to the _dev/scott_ space. The special URL signifies that requests sent to the "Scott URL" will try to first route to services in the _dev/scott_ space, but if that fails, they will fall back to services in the _dev_ space.

```
Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

```
Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```
-->

![](../articles/dev-spaces/media/common/space-routing.png)

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

Maintenant que vous avez exploré Azure Dev Spaces, [partagez votre espace de développement avec les membres de l’équipe](../articles/dev-spaces/how-to/share-dev-spaces.md) et montrez-leur combien il est facile de collaborer.

## <a name="clean-up"></a>Nettoyer
Pour supprimer complètement une instance d’Azure Dev Spaces sur un cluster, dont tous les espaces de développement et les services en cours d’exécution qu’elle contient, utilisez la commande `az aks remove-dev-spaces`. N’oubliez pas que cette action est irréversible. Vous pouvez ajouter à nouveau la prise en charge d’Azure Dev Spaces sur le cluster, mais il sera comme si vous recommenciez. Vos anciens services et espaces ne sont pas restaurés.

L’exemple suivant répertorie les contrôleurs Azure Dev Spaces de votre abonnement actif, puis supprime le contrôleur Azure Dev Spaces qui est associé au cluster AKS « myaks » dans le groupe de ressources « myaks-rg ».

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```