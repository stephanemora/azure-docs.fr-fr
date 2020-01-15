---
title: Présentation des groupes de conteneurs
description: En savoir plus sur les groupes de conteneurs dans Azure Container Instances, une collection d’instances qui partagent un cycle de vie et des ressources telles que le stockage et le réseau
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: ca160c62160bc5233139dccc650474811c4cd784
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442299"
---
# <a name="container-groups-in-azure-container-instances"></a>Groupes de conteneurs dans Azure Container Instances

La ressource de niveau supérieur dans Azure Container Instances est un *groupe de conteneurs*. Cet article décrit les groupes de conteneurs et les types de scénarios associés.

## <a name="what-is-a-container-group"></a>Qu’est-ce qu’un groupe de conteneurs ?

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

Voici deux méthodes courantes de déploiement d’un groupe à plusieurs conteneurs : utilisez un [modèle Resource Manager][resource-manager template] ou un [fichier YAML][yaml-file]. Un modèle Resource Manager est recommandé si vous avez besoin de déployer des ressources de service Azure supplémentaires (par exemple, un [partage Azure Files][azure-files]) lorsque vous déployez les instances de conteneur. En raison de la nature plus concise du format YAML, un fichier YAML est recommandé lorsque le déploiement comprend uniquement les instances de conteneur. Pour plus d'informations sur les propriétés que vous pouvez définir, consultez la documentation [Informations de référence sur les modèles Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) ou [Informations de référence sur YAML](container-instances-reference-yaml.md).

Pour conserver la configuration d’un groupe de conteneurs, vous pouvez exporter la configuration dans un fichier YAML avec la commande Azure CLI [az container export][az-container-export]. L’exportation permet de stocker ces configurations de groupe de conteneurs dans la gestion de version pour la « configuration en tant que code ». Vous pouvez également utiliser le fichier exporté comme point de départ pour développer une nouvelle configuration dans YAML.



## <a name="resource-allocation"></a>Allocation des ressources

Azure Container Instances alloue des ressources comme l’UC, la mémoire et éventuellement le [GPU][gpus] (préversion) à un groupe multi-conteneurs en ajoutant les [demandes de ressources][resource-requests] des instances du groupe. Par exemple, pour les ressources d’UC, si vous créez un groupe de conteneurs avec deux instances, chacune demandant 1 UC, le groupe de conteneurs se voit allouer 2 UC.

### <a name="resource-usage-by-instances"></a>Utilisation des ressources par les instances

Chaque instance de conteneur dans un groupe se voit allouer les ressources spécifiées dans sa demande de ressource. Toutefois, le nombre maximal de ressources utilisées par une instance dans un groupe peut être différent si vous configurez sa propriété facultative de [limite des ressources][resource-limits]. La limite des ressources d’une instance doit être supérieure ou égale à la propriété obligatoire de [demande de ressource][resource-requests].

* Si vous ne spécifiez pas de limite de ressources, l’utilisation maximale des ressources de l’instance est identique à celle de sa demande de ressource.

* Si vous spécifiez une limite pour une instance, l’utilisation maximale de l’instance peut être supérieure à la demande, jusqu’à la limite que vous définissez. En conséquence, l’utilisation des ressources par d’autres instances dans le groupe peut diminuer. La limite maximale de ressources que vous pouvez définir pour une instance est le nombre total de ressources allouées au groupe.
    
Par exemple, dans un groupe avec deux instances demandant chacune 1 processeur, un de vos conteneurs peut exécuter une charge de travail qui nécessite plus de processeurs que l’autre.

Dans ce scénario, vous pouvez définir une limite de ressource de 2 UC pour l’instance. Cette configuration permet au conteneur d’utiliser jusqu’à 2 UC complètes si elles sont disponibles.

### <a name="minimum-and-maximum-allocation"></a>Allocation minimale et maximale

* Allouez un **minimum** d’1 UC et de 1 Go de mémoire à un groupe de conteneurs. Il est possible d’approvisionner des instances de conteneur individuelles avec moins de 1 processeur virtuel et de 1 Go de mémoire. 

* Pour les ressources **maximales** d’un groupe de conteneurs, consultez la [disponibilité des ressources][region-availability] pour Azure Container Instances dans la région de déploiement.

## <a name="networking"></a>Mise en réseau

Les groupes de conteneurs peuvent partager une adresse IP externe et un espace de noms de port sur cette adresse IP. Pour que les clients externes puissent atteindre un conteneur au sein du groupe, vous devez exposer le port sur l’adresse IP et à partir du conteneur. Étant donné que les conteneurs au sein du groupe partagent un espace de noms de port, le mappage de port n’est pas pris en charge. 

Dans un groupe de conteneurs, les instances de conteneur peuvent s’atteindre les unes les autres via localhost sur n’importe quel port, même si ce port n’est pas exposé en externe sur l’adresse IP du groupe ou à partir du conteneur.

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
