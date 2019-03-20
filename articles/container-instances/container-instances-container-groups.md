---
title: Groupes de conteneurs Azure Container Instances
description: Comprendre comment les conteneurs multiples groupes Professionnel dans Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 8724bd7e13b0d8607ad5a6814b27c8c06681f331
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202008"
---
# <a name="container-groups-in-azure-container-instances"></a>Groupes de conteneurs dans Azure Container Instances

La ressource de niveau supérieur dans Azure Container Instances est un *groupe de conteneurs*. Cet article décrit les groupes de conteneurs et les types de scénarios associés.

## <a name="how-a-container-group-works"></a>Fonctionnement d’un groupe de conteneurs

Un groupe de conteneurs est une collection de conteneurs qui sont planifiés sur le même ordinateur hôte. Les conteneurs dans un groupe de conteneurs partagent un cycle de vie, de ressources, de réseau local et de volumes de stockage. Il est semblable dans son concept à un *pod* dans [Kubernetes][kubernetes-pod].

Le diagramme suivant montre un exemple de groupe de conteneurs incluant plusieurs conteneurs :

![Diagramme de groupes de conteneurs][container-groups-example]

Le groupe de conteneurs donné en exemple :

* Est planifié sur un ordinateur hôte unique.
* Est affecté à une étiquette de nom DNS.
* Expose une adresse IP publique unique, avec exposition d’un seul port.
* Comprend deux conteneurs. Un conteneur écoute le port 80, l’autre le port 5000.
* Inclut deux partages de fichiers Azure en tant que montages de volume, et chaque conteneur monte l’un des partages localement.

> [!NOTE]
> Groupes de plusieurs conteneurs actuellement en charge uniquement les conteneurs Linux. Pour les conteneurs Windows, Azure Container Instances prend uniquement en charge le déploiement d’une instance unique. Bien que nous nous efforçons de proposer toutes ces fonctionnalités pour les conteneurs Windows, vous pouvez trouver les différences actuelles de la plateforme dans le service [vue d’ensemble](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Déploiement

Voici deux méthodes courantes pour déployer un groupe de conteneurs : utiliser un [modèle Resource Manager] [ resource-manager template] ou un [fichier YAML][yaml-file]. Utiliser un modèle Resource Manager lorsque vous avez besoin déployer des ressources de service Azure supplémentaires (par exemple, un [partage Azure Files][azure-files]) au moment où vous déployez les instances de conteneur. En raison de nature plus concis au format YAML, un fichier YAML est recommandé lorsque votre déploiement inclut uniquement les instances de conteneur.

## <a name="resource-allocation"></a>Allocation des ressources

Azure Container Instances alloue des ressources telles que les processeurs, mémoire et éventuellement [GPU] [ gpus] (version préliminaire) et un groupe de conteneurs en ajoutant le [demandes de ressources] [ resource-requests] des instances dans le groupe. En prenant des ressources processeur par exemple, si vous créez un groupe de conteneurs avec deux instances, chaque demande 1 processeur, puis le groupe de conteneurs est allouée à 2 processeurs.

Les ressources maximales disponibles pour un groupe de conteneurs varient selon le [région Azure] [ region-availability] utilisé pour le déploiement.

### <a name="container-resource-requests-and-limits"></a>Les limites et les demandes de ressources de conteneur

* Par défaut, les instances de conteneur dans un groupe partagent les ressources demandées du groupe. Dans un groupe avec deux instances de chaque demande 1 processeur, le groupe dans son ensemble a accès à 2 processeurs. Chaque instance peut utiliser jusqu'à 2 processeurs et les instances peuvent entrer en concurrence pour les ressources de processeur sont en cours.

* Pour limiter l’utilisation des ressources par une instance d’un groupe, vous pouvez également définir un [limite de ressource] [ resource-limits] pour l’instance. Dans un groupe avec deux instances demandant 1 processeur, un de vos conteneurs peut nécessiter plus de processeurs à exécuter à l’autre.

  Dans ce scénario, vous pouvez définir une limite de ressource de 0,5 processeur pour une instance et une limite de 2 processeurs pour la deuxième. Cette configuration limite l’utilisation des ressources du conteneur premier 0,5 processeur, ce qui permet le second conteneur à utiliser les 2 UC s’il est disponible.

Pour plus d’informations, consultez le [ResourceRequirements] [ resource-requirements] propriété dans le conteneur de groupes de l’API REST.

### <a name="minimum-and-maximum-allocation"></a>Minimale et maximale d’allocation

* Allouer un **minimale** 1 processeur et 1 Go de mémoire à un groupe de conteneurs. Instances de conteneur individuels au sein d’un groupe peuvent être configurées avec moins de 1 processeur et 1 Go de mémoire. 

* Pour le **maximale** des ressources dans un groupe de conteneurs, consultez la [disponibilité des ressources] [aci-région-disponibilité] pour Azure Container Instances dans la région de déploiement.

## <a name="networking"></a>Mise en réseau

Les groupes du conteneur partagent une adresse IP et un espace de noms de port sur cette adresse IP. Pour que les clients externes puissent atteindre un conteneur au sein du groupe, vous devez exposer le port sur l’adresse IP et à partir du conteneur. Étant donné que les conteneurs au sein du groupe partagent un espace de noms de port, le mappage de port n’est pas pris en charge. Conteneurs au sein d’un groupe peuvent atteindre mutuellement via localhost sur les ports qu’ils ont exposés, même si ces ports ne sont pas exposés en externe sur l’adresse IP du groupe.

Si vous le souhaitez déployer des groupes de conteneurs dans un [réseau virtuel Azure] [ virtual-network] (version préliminaire) pour permettre aux conteneurs de communiquer en toute sécurité avec d’autres ressources dans le réseau virtuel.

## <a name="storage"></a>Stockage

Vous pouvez spécifier des volumes externes à monter dans un groupe de conteneurs. Vous pouvez mapper ces volumes à des chemins spécifiques dans les conteneurs individuels d’un groupe.

## <a name="common-scenarios"></a>Scénarios courants

Avec les groupes de plusieurs conteneurs, vous pouvez répartir une seule tâche fonctionnelle sur un petit nombre d’images conteneur. Ces images peuvent être fournies par différentes équipes et présenter des exigences spécifiques aux ressources.

Exemples d’utilisation :

* Un conteneur servant une application web et un conteneur extrayant le contenu le plus récent du contrôle de code source.
* Un conteneur d’applications et un conteneur de journalisation. Le conteneur de journalisation collecte les journaux et les métriques générés par l’application principale, puis les écrit dans le stockage à long terme.
* Un conteneur d’applications et un conteneur d’analyse. Le conteneur de surveillance émet régulièrement une demande à destination de l’application pour vérifier qu’elle s’exécute et répond correctement, et déclenche une alerte si ce n’est pas le cas.
* Un conteneur frontal et un conteneur de serveur principal. Le serveur frontal sont susceptibles de servir une application web, avec le serveur principal exécutant un service pour récupérer des données. 

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
