---
title: Groupes de conteneurs Azure Container Instances
description: Découvrez comment fonctionnent les groupes à plusieurs conteneurs dans Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b17004e7821bcac61ca98afdbeaf87644da2a441
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68326049"
---
# <a name="container-groups-in-azure-container-instances"></a>Groupes de conteneurs dans Azure Container Instances

La ressource de niveau supérieur dans Azure Container Instances est un *groupe de conteneurs*. Cet article décrit les groupes de conteneurs et les types de scénarios associés.

## <a name="how-a-container-group-works"></a>Fonctionnement d’un groupe de conteneurs

Un groupe de conteneurs est une collection de conteneurs qui sont planifiés sur le même ordinateur hôte. Les conteneurs d’un groupe de conteneurs partagent un cycle de vie, des ressources, un réseau local et les volumes de stockage. Il s’apparente conceptuellement à un *pod* dans [Kubernetes][kubernetes-pod].

Le diagramme suivant montre un exemple de groupe de conteneurs incluant plusieurs conteneurs :

![Diagramme de groupes de conteneurs][container-groups-example]

Le groupe de conteneurs donné en exemple :

* Est planifié sur un ordinateur hôte unique.
* Est affecté à une étiquette de nom DNS.
* Expose une adresse IP publique unique, avec exposition d’un seul port.
* Comprend deux conteneurs. Un conteneur écoute le port 80, l’autre le port 5000.
* Inclut deux partages de fichiers Azure en tant que montages de volume, et chaque conteneur monte l’un des partages localement.

> [!NOTE]
> Les groupes à plusieurs conteneurs ne prennent actuellement en charge que les conteneurs Linux. Pour les conteneurs Windows, Azure Container Instances prend uniquement en charge le déploiement d’une instance unique. Nous travaillons actuellement à proposer toutes ces fonctionnalités dans des conteneurs Windows. En attendant, nous vous invitons à découvrir les différences actuelles de la plateforme dans le service [Vue d’ensemble](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Déploiement

Voici deux méthodes courantes de déploiement d’un groupe à plusieurs conteneurs : utilisez un [modèle Resource Manager][resource-manager template] ou un [fichier YAML][yaml-file]. Un modèle Resource Manager est recommandé si vous avez besoin de déployer des ressources de service Azure supplémentaires (par exemple, un [partage Azure Files][azure-files]) lorsque vous déployez les instances de conteneur. En raison de la nature plus concise du format YAML, un fichier YAML est recommandé lorsque le déploiement comprend uniquement les instances de conteneur.

Pour conserver la configuration d’un groupe de conteneurs, vous pouvez exporter la configuration dans un fichier YAML avec la commande Azure CLI [az container export][az-container-export]. L’exportation permet de stocker ces configurations de groupe de conteneurs dans la gestion de version pour la « configuration en tant que code ». Vous pouvez également utiliser le fichier exporté comme point de départ pour développer une nouvelle configuration dans YAML.

## <a name="resource-allocation"></a>Allocation des ressources

Azure Container Instances alloue des ressources telles que UC, mémoire et éventuellement [GPU][gpus] (préversion) à un groupe de conteneurs en ajoutant les [demandes de ressources][resource-requests] des instances du groupe. Par exemple, pour les ressources d’UC, si vous créez un groupe de conteneurs avec deux instances, chacune demandant 1 UC, le groupe de conteneurs se voit allouer 2 UC.

Le nombre maximum de ressources disponibles pour un groupe de conteneurs varie selon la [région Azure][region-availability] utilisée pour le déploiement.

### <a name="container-resource-requests-and-limits"></a>Demandes et limites de ressources de conteneur

* Par défaut, les instances de conteneur d’un groupe partagent les ressources demandées du groupe. Dans un groupe avec deux instances dont chacune demande 1 UC, le groupe dans son ensemble a accès à 2 UC. Chaque instance peut utiliser jusqu’à 2 UC et les instances en cours d’exécution peuvent entrer en concurrence pour les ressources d’UC.

* Pour limiter l’utilisation des ressources par une instance d’un groupe, vous pouvez également définir une [limite de ressource][resource-limits]pour l’instance. Dans un groupe avec deux instances demandant 1 UC, un de vos conteneurs peut nécessiter plus d’UC que l’autre.

  Dans ce scénario, vous pouvez définir une limite de ressource de 0,5 UC pour une instance et une limite de 2 UC pour la deuxième. Cette configuration limite l’utilisation des ressources du premier conteneur à 0,5 UC, permettant ainsi au deuxième d’utiliser complètement, si elles sont disponibles, les 2 UC.

Pour plus d’informations, consultez la propriété [ResourceRequirements][resource-requirements] dans l’API REST des groupes de conteneurs.

### <a name="minimum-and-maximum-allocation"></a>Allocation minimale et maximale

* Allouez un **minimum** d’1 UC et de 1 Go de mémoire à un groupe de conteneurs. Il est possible d’approvisionner des instances de conteneur individuelles avec moins de 1 processeur virtuel et de 1 Go de mémoire. 

* Pour les ressources **maximales** d’un groupe de conteneurs, consultez la [disponibilité des ressources][region-availability] pour Azure Container Instances dans la région de déploiement.

## <a name="networking"></a>Mise en réseau

Les groupes du conteneur partagent une adresse IP et un espace de noms de port sur cette adresse IP. Pour que les clients externes puissent atteindre un conteneur au sein du groupe, vous devez exposer le port sur l’adresse IP et à partir du conteneur. Étant donné que les conteneurs au sein du groupe partagent un espace de noms de port, le mappage de port n’est pas pris en charge. Les conteneurs au sein d’un groupe peuvent s’atteindre les uns les autres via localhost sur les ports qu’ils ont exposés, même si ces ports ne sont pas exposés en externe sur l’adresse IP du groupe.

Déployez éventuellement des groupes de conteneurs dans un [réseau virtuel Azure][virtual-network] (préversion) pour permettre à vos conteneurs de communiquer en toute sécurité avec d’autres ressources dans le réseau virtuel.

## <a name="storage"></a>Stockage

Vous pouvez spécifier des volumes externes à monter dans un groupe de conteneurs. Vous pouvez mapper ces volumes à des chemins spécifiques dans les conteneurs individuels d’un groupe.

## <a name="common-scenarios"></a>Scénarios courants

Avec les groupes de plusieurs conteneurs, vous pouvez répartir une seule tâche fonctionnelle sur un petit nombre d’images conteneur. Ces images peuvent être fournies par différentes équipes et présenter des exigences spécifiques aux ressources.

Exemples d’utilisation :

* Un conteneur servant une application web et un conteneur extrayant le contenu le plus récent du contrôle de code source.
* Un conteneur d’applications et un conteneur de journalisation. Le conteneur de journalisation collecte les journaux d’activité et les métriques générés par l’application principale, puis les écrit dans le stockage à long terme.
* Un conteneur d’applications et un conteneur d’analyse. Le conteneur de surveillance émet régulièrement une demande à destination de l’application pour vérifier qu’elle s’exécute et répond correctement, et déclenche une alerte si ce n’est pas le cas.
* Un conteneur frontal et un conteneur principal. Le conteneur frontal peut servir d’application web, le conteneur principal exécutant un service pour récupérer des données. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment déployer un groupe de plusieurs conteneurs avec un modèle Azure Resource Manager :

> [!div class="nextstepaction"]
> [Déployer un groupe de conteneurs][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
