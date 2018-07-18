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
ms.openlocfilehash: d44f33b0e9f71c1d8d6e2c9878b08f9fa0e1f8a1
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938170"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Préparation de code pour le développement Docker et Kubernetes
Pour l’heure, vous disposez d’une application web de base qui peut s’exécuter localement. Vous allez maintenant la mettre en conteneur en créant des ressources qui définissent le conteneur de l’application et la façon dont il sera déployé dans Kubernetes. Azure Dev Spaces permet d’effectuer facilement cette tâche : 

1. Lancez VS Code et ouvrez le dossier `webfrontend`. (Vous pouvez ignorer les invites par défaut qui vous demandent d’ajouter des ressources de débogage ou de restaurer le projet.)
1. Ouvrez le terminal intégré dans VS Code (à partir du menu **Affichage > Terminal intégré**).
1. Exécutez cette commande (vérifiez que **webfrontend** est bien votre dossier actuel) :

    ```cmd
    azds prep --public
    ```

La commande `azds prep` d’Azure CLI génère des ressources Docker et Kubernetes avec les paramètres par défaut :
* `./Dockerfile` décrit l’image du conteneur de l’application et la façon dont le code source est généré et s’exécute dans le conteneur.
* Un [graphique Helm](https://docs.helm.sh) sous `./charts/webfrontend` décrit la façon dont le conteneur est déployé dans Kubernetes.

Pour l’instant, il n’est pas nécessaire de comprendre l’ensemble du contenu de ces fichiers. Il est cependant important de noter que **les mêmes ressources de configuration en tant que code Kubernetes et Docker peuvent être utilisées du développement jusqu’à la production, ce qui assure une meilleure cohérence dans les différents environnements.**
 
Un fichier nommé `./azds.yaml` est aussi généré par la commande `prep` ; il s’agit du fichier de configuration pour Azure Dev Spaces. Il complète les artefacts Docker et Kubernetes avec une configuration supplémentaire qui offre une expérience de développement itératif dans Azure.
