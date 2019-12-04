---
title: Mettre à jour un groupe de conteneurs
description: Découvrez comment mettre à jour les conteneurs en cours d’exécution dans vos groupes de conteneurs Azure Container Instances.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533299"
---
# <a name="update-containers-in-azure-container-instances"></a>Mettre à jour les conteneurs dans Azure Container Instances

Lors d’une utilisation normale de vos instances de conteneurs, vous pouvez avoir besoin de mettre à jour les conteneurs en cours d’exécution d’un [groupe de conteneurs](container-instances-container-groups.md). Par exemple, vous pouvez mettre à jour la version de l’image, modifier un nom DNS, mettre à jour les variables d’environnement ou actualiser l’état d’un conteneur dont l’application a planté.

> [!NOTE]
> Les groupes de conteneurs terminés ou supprimés ne peuvent pas être mis à jour. Une fois qu’un groupe de conteneurs s’est arrêté (dans un état de réussite ou d’échec) ou a été supprimé, le groupe doit être déployé en tant que nouveau groupe.

## <a name="update-a-container-group"></a>Mettre à jour un groupe de conteneurs

Pour mettre à jour les conteneurs d’un groupe de conteneurs en cours d’exécution, redéployez un groupe existant en ayant modifié au moins une propriété. Lorsque vous mettez à jour un groupe de conteneurs, tous les conteneurs en cours d’exécution sont redémarrés sur place, en général sur le même hôte conteneur sous-jacent.

Redéployez un groupe de conteneurs existant à l’aide de la commande create (ou du portail Azure), puis spécifiez le nom d’un groupe existant. Modifiez au moins une propriété valide du groupe lorsque vous émettez la commande Create pour déclencher le redéploiement, et laissez les propriétés restantes inchangées (ou continuez à utiliser les valeurs par défaut). Certaines propriétés de groupe de conteneurs ne sont pas valides pour le redéploiement. Pour obtenir la liste des propriétés non prises en charge, consultez [Propriétés qui nécessitent la suppression du conteneur](#properties-that-require-container-delete).

L’exemple Azure CLI suivant met à jour un groupe de conteneurs avec une nouvelle étiquette de nom DNS. Étant donné que la propriété d’étiquette de nom DNS du groupe est celui qui peut être mis à jour, le groupe de conteneurs est redéployé et ses conteneurs sont redémarrés.

Déploiement initial avec l’étiquette de nom DNS *myapplication-staging* :

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Mettez à jour le groupe de conteneurs avec une nouvelle étiquette de nom DNS, *myapplication*, et laissez les propriétés restantes inchangées :

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
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

[Arrêter ou démarrer manuellement des conteneurs dans Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
