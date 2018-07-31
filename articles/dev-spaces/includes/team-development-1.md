---
title: Fichier Include
description: Fichier Include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: ab6fdbcd3d1a6a5e611809ccee2343fced05d1e0
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39189283"
---
Jusqu’à présent, vous avez exécuté le code de votre application comme si vous étiez le seul développeur à travailler sur l’application. Dans cette section, vous allez apprendre comment Azure Dev Spaces simplifie le développement en équipe :
* Permettre à une équipe de développeurs de travailler dans le même environnement, dans un espace de développement partagé ou dans des espaces de développement distincts en fonction des besoins...
* Prendre en charge chaque développeur qui fait des itérations sur son code de manière isolée, sans risque de compromettre le travail des autres.
* Tester le code de bout en bout, avant la validation du code, sans avoir à créer des versions fictives ou à simuler des dépendances.

### <a name="challenges-with-developing-microservices"></a>Les problèmes liés au développement des microservices
Votre exemple d’application n’est pas très complexe pour le moment. Mais lorsque vous créerez une véritable application, les problèmes ne tarderont pas à émerger à mesure que vous ajouterez des services et que l’équipe de développement se développera.

Imaginez que vous travaillez sur un service qui interagit avec des dizaines d’autres services.

- Difficile d’imaginer exécuter tous les éléments en local pour le développement. Votre ordinateur de développement risque de ne pas avoir suffisamment de ressources pour exécuter l’application complète. Ou peut-être que votre application possède des points de terminaison qui doivent être publiquement accessibles (par exemple, votre application répond au Webhook d’une application SaaS).

- Vous pouvez essayer de n’exécuter que les services dont vous avez besoin, mais pour cela vous devriez d’abord être sûr que toutes les dépendances sont closes (y compris les dépendances de dépendances). Vous pouvez aussi avoir des problèmes pour la création et l’exécution de vos dépendances simplement parce que vous n’avez pas travaillé sur celles-ci.
- Certains développeurs ont recours à la simulation ou à des objets fictifs, pour la plupart de leurs dépendances de service. Si cette approche peut parfois se révéler utile, la gestion de ces objets fictifs peut rapidement demander du temps. En outre, avec cette approche votre espace de développement sera très différent de l’environnement de production et des bogues risquent d’apparaître.
- Ensuite, il devient difficile d’effectuer des tests de bout en bout, quels qu’ils soient. Les tests d’intégration ne peuvent être effectués qu’après validation, ce qui signifie que vous verrez les problèmes plus tard dans le cycle de développement.

![](../media/common/microservices-challenges.png)


### <a name="work-in-a-shared-dev-space"></a>Travailler dans un espace de développement partagé
Avec Azure Dev Spaces, vous pouvez configurer un espace de développement *partagé* dans Azure. Chaque développeur peut se concentrer sur sa partie de l’application et peut développer de manière itérative du *code avant validation* dans un espace de développement qui contient déjà tous les autres services et ressources de cloud nécessaires à leurs scénarios. Les dépendances sont toujours à jour et la façon de travailler des développeurs reflète celle des équipes de production.

### <a name="work-in-your-own-space"></a>Travailler dans votre propre espace de travail
Lorsque vous développez le code de votre service, avant qu’il soit prêt à être archivé, il n’est pas en très bon état. Vous le remaniez de manière itérative, vous le testez et vous essayez plusieurs solutions. Azure Dev Spaces propose le concept **d’espace**, qui vous permet de travailler de manière isolée, sans risquer de compromettre le travail des autres membres de votre équipe.

> [!Note]
> Avant de poursuivre, fermez les fenêtres VS Code des deux services, puis exécutez `azds up -d` dans chacun des dossiers racine du service (il s’agit d’une limite de la version préliminaire).

Examinons de plus près les services qui s’exécutent. Exécutez la commande `azds list-up`. La sortie ressemble à ce qui suit :

```
Name                          DevSpace  Type     Updated      Status
----------------------------  --------  -------  -----------  ----------------
mywebapi                      default   Service  10m 1s ago   Running
mywebapi-54f9cf5b59-bjnkm     default   Pod      10m 4s ago   Running
webfrontend-5b697958d6-b6v96  default   Pod      26m 38s ago  Init:1/3:mindaro-build
```

La colonne DevSpace indique que les deux services s’exécutent dans un espace nommé `default`. Toute personne qui ouvre l’URL publique et accède à l’application web appelle le chemin d’accès du code précédemment écrit qui s’exécute via les deux services. Maintenant, supposons que vous souhaitez continuer à développer `mywebapi`. Comment apporter des modifications au code, puis les tester sans interrompre les autres développeurs qui utilisent l’environnement de développement ? Pour ce faire, vous allez configurer votre propre espace.

### <a name="create-a-dev-space"></a>Créer un espace de développement
Pour exécuter votre propre version de `mywebapi` dans un espace autre que `default`, vous pouvez créer votre propre espace à l’aide de la commande suivante :

``` 
azds space select --name scott
```

Quad vous y êtes invité, sélectionnez `default` comme **espace de développement parent**. Cela signifie que notre nouvel espace, `default/scott`, dérive de l’espace `default`. Nous verrons bientôt comment cela va nous aider lors des tests. 

Dans l’exemple ci-dessus, j’ai utilisé mon nom pour le nouvel espace afin qu’il soit identifié par mes homologues comme l’espace dans lequel je travaille, mais vous pouvez l’appeler comme vous le souhaitez du moment que sa signification reste claire, par exemple « sprint4 » ou « demo ».

Exécutez la commande `azds space list` pour afficher une liste de tous les espaces dans l’environnement de développement. Un astérisque (*) s’affiche en regard de l’espace actuellement sélectionné. Dans votre cas, l’espace nommé « default/scott » a été automatiquement sélectionné lors de sa création. Vous pouvez sélectionner un autre espace à tout moment avec la commande `azds space select`.
