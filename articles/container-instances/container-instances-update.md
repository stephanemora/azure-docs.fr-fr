---
title: Mettre à jour les conteneurs dans Azure Container Instances
description: Découvrez comment mettre à jour les conteneurs en cours d’exécution dans vos groupes de conteneurs Azure Container Instances.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: marsma
ms.openlocfilehash: cce7befeb7f0f770bf42fcd7926979da97a6a44a
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392756"
---
# <a name="update-containers-in-azure-container-instances"></a>Mettre à jour les conteneurs dans Azure Container Instances

Lors d’une utilisation normale de vos instances de conteneurs, vous pouvez avoir besoin de mettre à jour les conteneurs d’un groupe de conteneurs. Par exemple, vous pouvez mettre à jour la version de l’image, modifier un nom DNS, mettre à jour les variables d’environnement ou actualiser l’état d’un conteneur dont l’application a planté.

## <a name="update-a-container-group"></a>Mettre à jour un groupe de conteneurs

Pour mettre à jour les conteneurs d’un groupe de conteneurs, redéployez un groupe existant en ayant modifié au moins une propriété. Lorsque vous mettez à jour un groupe de conteneurs, tous les conteneurs en cours d’exécution sont redémarrés sur place.

Redéployez un groupe de conteneurs existant à l’aide de la commande create (ou du portail Azure), puis spécifiez le nom d’un groupe existant. Modifiez au moins une propriété valide du groupe lorsque vous utilisez la commande create pour déclencher le redéploiement. Certaines propriétés de groupe de conteneurs ne sont pas valides pour le redéploiement. Pour obtenir la liste des propriétés non prises en charge, consultez [Propriétés qui nécessitent la suppression du conteneur](#properties-that-require-delete).

L’exemple Azure CLI suivant met à jour un groupe de conteneurs avec une nouvelle étiquette de nom DNS. Étant donné que la propriété d’étiquette de nom DNS du groupe est modifiée, le groupe de conteneurs est redéployé et ses conteneurs sont redémarrés.

Déploiement initial avec l’étiquette de nom DNS *myapplication-staging* :

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Attribuez au groupe de conteneurs la nouvelle étiquette de nom DNS *myapplication* :

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Avantages de la mise à jour

Lorsque vous mettez à jour un groupe de conteneurs existant, le déploiement est plus rapide. Lorsque vous redéployez un groupe de conteneurs existant, ses calques d’image de conteneur sont tirés (pull) de ceux mis en cache lors du déploiement précédent. Au lieu de tirer tous les calques d’image récents provenant du Registre, comme c’est le cas avec les nouveaux déploiements, seuls les calques modifiés (s’il en existe) sont tirés.

Lorsque vous mettez à jour un groupe existant au lieu d’en déployer un nouveau, les applications basées sur des images conteneur volumineuses (par exemple, Windows Server Core) peuvent être déployées beaucoup plus rapidement.

## <a name="limitations"></a>Limites

Certaines propriétés de groupe de conteneurs ne peuvent pas être mises à jour. Pour modifier certaines propriétés d’un groupe de conteneurs, vous devez d’abord supprimer, puis redéployer le groupe. Pour plus d’informations, consultez [Propriétés qui nécessitent la suppression du conteneur](#properties-that-require-container-delete).

Tous les conteneurs d’un groupe de conteneurs sont redémarrés lorsque vous mettez à jour ce groupe. Vous ne pouvez pas effectuer une mise à jour ou un redémarrage sur place pour un seul conteneur du groupe.

En général, l’adresse IP d’un conteneur ne change pas après une mise à jour. Toutefois, il n’est pas non plus garanti qu’elle reste la même. Tant que le groupe de conteneurs est déployé sur le même hôte sous-jacent, le groupe de conteneurs conserve son adresse IP. Même si Azure Container Instances s’efforce de redéployer le groupe sur le même hôte, il peut arriver que certains événements internes à Azure entraînent un redéploiement sur un hôte différent, bien que cela soit plutôt rare. Pour résoudre ce problème, vous devez toujours utiliser une étiquette de nom DNS pour vos instances de conteneur.

Les groupes de conteneurs terminés ou supprimés ne peuvent pas être mis à jour. Une fois qu’un groupe de conteneurs a été arrêté (à l’état *Terminé*) ou supprimé, le groupe est déployé comme un nouveau groupe.

## <a name="properties-that-require-container-delete"></a>Propriétés qui nécessitent la suppression du conteneur

Comme mentionné précédemment, certaines propriétés de groupe de conteneurs ne peuvent pas être mises à jour. Par exemple, pour modifier les ports ou la stratégie de redémarrage d’un conteneur, vous devez d’abord supprimer le groupe de conteneurs, puis le recréer.

Ces propriétés nécessitent la suppression du groupe de conteneurs avant le redéploiement :

* Type de système d’exploitation
* UC
* Mémoire
* Stratégie de redémarrage
* Ports

Lorsque vous supprimez, puis recréez un groupe de conteneurs, celui-ci n’est pas redéployé, mais recréé, comme un nouveau groupe. Tous les calques d’image sont fraîchement tirés (pull) du Registre. Il ne s’agit pas des calques mis en cache par un déploiement précédent. L’adresse IP du conteneur peut également changer si celui-ci est déployé sur un autre hôte sous-jacent.

## <a name="next-steps"></a>Étapes suivantes

Le **groupe de conteneurs** est mentionné plusieurs fois dans cet article. Chaque conteneur d’Azure Container Instances est déployé dans un groupe de conteneurs, et chaque groupe de conteneurs peut contenir plusieurs conteneurs.

[Groupes de conteneurs dans Azure Container Instances](container-instances-container-groups.md)

[Déployer un groupe multiconteneur](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
