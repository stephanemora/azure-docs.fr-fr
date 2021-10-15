---
title: Mettre à jour les cibles de stockage Azure HPC Cache
description: Comment modifier les cibles de stockage Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/30/2021
ms.author: v-erkel
ms.openlocfilehash: 151186d3d42ad799707f4a352e76cd154d3443bb
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276186"
---
# <a name="edit-storage-targets"></a>Modifier les cibles de stockage

Vous pouvez modifier les cibles de stockage avec le portail Azure ou Azure CLI. Par exemple, vous pouvez modifier les stratégies d’accès, les modèles d’utilisation et les chemins d’accès d’espace de noms pour une cible de stockage existante.

> [!TIP]
> Consultez [Afficher et gérer les cibles de stockage](manage-storage-targets.md) pour savoir comment supprimer ou suspendre des cibles de stockage, ou leur faire écrire des données en mémoire cache dans le stockage principal.

Selon le type de stockage, vous pouvez modifier ces valeurs de cible de stockage :

* Pour les cibles de stockage d’objets blob, vous pouvez modifier le chemin d’accès à l’espace de noms et la stratégie d’accès.

* Pour les cibles de stockage NFS, vous pouvez modifier les valeurs suivantes :

  * Chemins d’accès de l’espace de noms
  * Stratégie d’accès
  * Sous-répertoire d’exportation ou d’exportation de stockage associé à un chemin d’accès d’espace de noms
  * Modèle d’utilisation

* Pour les cibles de stockage ADLS-NFS, vous pouvez modifier le chemin d’accès de l’espace de noms, la stratégie d’accès et le modèle d’utilisation.

Vous ne pouvez pas modifier le nom, le type ou le système de stockage principal d’une cible de stockage. Si vous avez besoin de modifier ces propriétés, supprimez la cible de stockage et créez-en une autre, avec la nouvelle valeur.

La [vidéo Gestion d’Azure HPC Cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) montre comment modifier une cible de stockage dans le portail Azure.

## <a name="change-a-blob-storage-targets-namespace-path-or-access-policy"></a>Modifier le chemin d’accès ou la stratégie d’accès de l’espace de noms d’une cible de stockage blob

Les chemins d’espace de noms sont les chemins utilisés par les clients pour monter cette cible de stockage. (Pour en savoir plus, consultez [Planifier l’espace de noms agrégé](hpc-cache-namespace.md) et [Configurer l’espace de noms agrégé](add-namespace-paths.md)).

Utilisez le portail Azure ou Azure CLI pour modifier le chemin d’accès de l’espace de noms ou la stratégie d’accès.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Utilisez la page **Espace de noms** de votre cache Azure HPC pour mettre à jour le chemin d’accès d’espace de noms ou la stratégie d’accès du client. La page Espace de noms est décrite plus en détail dans l’article [Configurer l’espace de noms agrégé](add-namespace-paths.md).

1. Cliquez sur le chemin d’accès que vous souhaitez modifier.
   ![Capture d’écran de la page Espace de noms avec le curseur sur un élément dans la colonne Chemin d’accès de l’espace de noms (première colonne à gauche). Le nom est mis en forme en tant que lien hypertexte et le curseur indique qu’il est possible de cliquer dessus.](media/edit-select-namespace.png)

1. Utilisez la fenêtre d’édition pour saisir un nouveau chemin virtuel ou mettre à jour la stratégie d’accès.

   ![Capture d’écran de la page Espace de noms après sélection d’un chemin d’accès d’espace de noms Blob. Les champs d’édition s’affichent dans un volet à droite.](media/update-namespace-blob.png)

Après avoir apporté les modifications souhaitées, cliquez sur **OK** pour mettre à jour la cible de stockage ou cliquez sur **Annuler** pour abandonner les modifications.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Pour modifier l’espace de noms d’une cible de stockage blob avec Azure CLI, utilisez la commande [az hpc-cache blob-storage-target update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update). Seule la valeur `--virtual-namespace-path` peut être modifiée.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Mettre à jour cible de stockage NFS

Pour les cibles de stockage NFS, vous pouvez modifier ou ajouter des chemins d’accès d’espace de noms virtuels, modifier les valeurs d’exportation NFS ou de sous-répertoires vers lesquelles pointe un chemin d’accès et modifier le modèle d’utilisation.

Les cibles de stockage dans les caches avec certains types de paramètres DNS personnalisés disposent également d’un contrôle pour actualiser leurs adresses IP. (Ce type de configuration est rare.) Découvrez comment actualiser les paramètres DNS dans [Afficher et gérer les cibles de stockage](manage-storage-targets.md#update-ip-address-custom-dns-configurations-only).

La procédure est indiquée ci-dessous :

* [Modifier les valeurs d’espaces de noms agrégés](#change-aggregated-namespace-values) (chemin d’espace de noms virtuel, stratégie d’accès, exportation et sous-répertoire d’exportation)
* [Modifier le modèle d’utilisation](#change-the-usage-model)

### <a name="change-aggregated-namespace-values"></a>Modifier les valeurs d’espaces de noms agrégés

Vous pouvez utiliser le portail Azure ou Azure CLI pour modifier le chemin de l’espace de noms côté client, l’exportation de stockage et le sous-répertoire d’exportation (si utilisé). Si vous avez besoin de modifier la stratégie d’accès, utilisez le portail Azure.

Lisez les instructions de la page [Ajouter des chemins d’accès aux espaces de noms NFS](add-namespace-paths.md#nfs-namespace-paths) si vous avez besoin d’un rappel sur la création de plusieurs chemins d’accès valides sur une cible de stockage.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Utilisez la page **Espace de noms** de votre cache Azure HPC pour mettre à jour les valeurs d’espace de noms, y compris la stratégie d’accès du client. Cette page est décrite plus en détail dans l’article [Configurer l’espace de noms agrégé](add-namespace-paths.md).

![Capture d’écran de la page Espace de noms du portail avec la page de mise à jour NFS ouverte à droite](media/update-namespace-nfs.png)

1. Cliquez sur le nom du chemin d’accès que vous souhaitez modifier.
1. Utilisez la fenêtre Modifier pour entrer de nouvelles valeurs de chemin d’accès virtuel, d’exportation ou de sous-répertoire, ou pour sélectionner une autre stratégie d’accès.
1. Une fois que vous avez apporté les modifications souhaitées, cliquez sur **OK** pour mettre à jour la cible de stockage ou cliquez sur **Annuler** pour abandonner les modifications.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Utilisez l’option ``--junction`` de la commande [az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target) pour modifier le chemin d’accès de l’espace de noms, l’exportation NFS ou le sous-répertoire d’exportation.

Le paramètre ``--junction`` utilise les valeurs suivantes :

* ``namespace-path`` : chemin d’accès au fichier virtuel orienté client
* ``nfs-export`` : exportation du système de stockage à associer au chemin d’accès orienté client
* ``target-path`` (facultatif) : sous-répertoire de l’exportation, si nécessaire

Exemple : ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Vous devez fournir les trois valeurs pour chaque chemin d’accès dans l’instruction ``--junction``. Conservez les valeurs existantes pour toutes les valeurs que vous ne souhaitez pas modifier.

Le nom du cache, le nom de la cible de stockage et le groupe de ressources sont également requis dans toutes les commandes de mise à jour.

Exemple de commande :

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Modifier le modèle d’utilisation

Le modèle d’utilisation influe sur le mode de conservation des données par le cache. Pour en savoir plus, consultez [Comprendre les modèles d’utilisation du cache](cache-usage-models.md).

> [!NOTE]
> Vous ne pouvez pas changer entre **Lire les écritures volumineuses rares** et d’autres modèles d’utilisation. Pour en savoir plus, consultez [Comprendre les modèles d’utilisation du cache](cache-usage-models.md#change-usage-models).

Pour modifier le modèle d’utilisation d’une cible de stockage NFS, utilisez l’une des méthodes suivantes.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Ouvrez la page **Cibles de stockage** sur le portail Azure. Cliquez sur le nom d’une cible de stockage dans la liste pour ouvrir sa page d’édition.

![Capture d’écran de la page de modification d’une cible de stockage NFS](media/edit-storage-nfs.png)

Utilisez le sélecteur de liste déroulante pour choisir un nouveau modèle d’utilisation. Cliquez sur **OK** pour mettre à jour la cible de stockage ou cliquez sur **Annuler** pour abandonner les modifications.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Utilisez la commande [az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update).

La commande de mise à jour est quasiment identique à la commande que vous utilisez pour ajouter une cible de stockage NFS. Pour plus d’informations et pour voir des exemples, reportez-vous à [Créer une cible de stockage NFS](hpc-cache-add-storage.md#create-an-nfs-storage-target).

Pour modifier le modèle d’utilisation, mettez à jour l’option ``--nfs3-usage-model``. Exemple : ``--nfs3-usage-model WRITE_WORKLOAD_15``

Le nom du cache, le nom de la cible de stockage et les valeurs du groupe de ressources sont également requis.

Si vous souhaitez vérifier les noms des modèles d’utilisation, utilisez la commande [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage-model-list).

Si le cache est arrêté ou n’est pas sain, la mise à jour s’applique une fois que le cache est sain.

---

## <a name="update-an-adls-nfs-storage-target"></a>Mettre à jour une cible de stockage ADLS-NFS

Comme pour les cibles NFS, vous pouvez modifier le chemin d’accès de l’espace de noms et le modèle d’utilisation pour les cibles de stockage ADLS-NFS.

### <a name="change-an-adls-nfs-namespace-path"></a>Modifier le chemin d’accès d’un espace de noms ADLS-NFS

Utilisez la page **Espace de noms** de votre cache Azure HPC Cache pour mettre à jour les valeurs d’espace de noms. Cette page est décrite plus en détail dans l’article [Configurer l’espace de noms agrégé](add-namespace-paths.md).

![Capture d’écran de la page Espace de noms du portail avec la page de mise à jour ADS-NFS ouverte à droite](media/update-namespace-adls.png)

1. Cliquez sur le nom du chemin d’accès que vous souhaitez modifier.
1. Utilisez la fenêtre d’édition pour saisir un nouveau chemin virtuel ou mettre à jour la stratégie d’accès.
1. Une fois que vous avez apporté les modifications souhaitées, cliquez sur **OK** pour mettre à jour la cible de stockage ou cliquez sur **Annuler** pour abandonner les modifications.

### <a name="change-adls-nfs-usage-models"></a>Modifier les modèles d’utilisation ADLS-NFS

La configuration pour les modèles d’utilisation ADLS-NFS est identique à la sélection du modèle d’utilisation NFS. Lisez les instructions du portail dans [Modifier le modèle d’utilisation](#change-the-usage-model) dans la section NFS ci-dessus. Des outils supplémentaires pour la mise à jour des cibles de stockage ADLS-NFS sont en cours de développement.

## <a name="next-steps"></a>Étapes suivantes

* Lisez [Afficher et gérer les cibles de stockage](manage-storage-targets.md) pour obtenir des informations sur l’arrêt, la suppression et la purge de cibles de stockage spécifiques.
* Pour en savoir plus sur les options de cible de stockage, voir [Ajouter des cibles de stockage](hpc-cache-add-storage.md).
* Pour plus d’informations sur l’utilisation des chemins d’accès virtuels, voir [Planifier l’espace de noms agrégé](hpc-cache-namespace.md).
