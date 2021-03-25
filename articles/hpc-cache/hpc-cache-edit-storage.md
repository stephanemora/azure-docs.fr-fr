---
title: Mettre à jour les cibles de stockage Azure HPC Cache
description: Comment modifier les cibles de stockage Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/10/2021
ms.author: v-erkel
ms.openlocfilehash: 0c505937d4adbe2596e91ed7269676e60ada8253
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772577"
---
# <a name="edit-storage-targets"></a>Modifier les cibles de stockage

Vous pouvez supprimer ou modifier les cibles de stockage avec le portail Azure ou Azure CLI.

Selon le type de stockage, vous pouvez modifier ces valeurs de cible de stockage :

* Pour les cibles de stockage d’objets blob, vous pouvez modifier le chemin d’accès à l’espace de noms et la stratégie d’accès.

* Pour les cibles de stockage NFS, vous pouvez modifier les valeurs suivantes :

  * Chemins d’accès de l’espace de noms
  * Stratégie d’accès
  * Sous-répertoire d’exportation ou d’exportation de stockage associé à un chemin d’accès d’espace de noms
  * Modèle d’utilisation

* Pour les cibles de stockage ADLS-NFS, vous pouvez modifier le chemin d’accès de l’espace de noms, la stratégie d’accès et le modèle d’utilisation.

Vous ne pouvez pas modifier le nom, le type ou le système de stockage back-end d’une cible de stockage (conteneur d’objets blob ou nom d’hôte/adresse IP NFS). Si vous avez besoin de modifier ces propriétés, supprimez la cible de stockage et créez-en une autre, avec la nouvelle valeur.

> [!TIP]
> La [vidéo Gestion d’Azure HPC Cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) montre comment modifier une cible de stockage dans le portail Azure.

## <a name="remove-a-storage-target"></a>Supprimer une cible de stockage

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour supprimer une cible de stockage, ouvrez la page **Cibles de stockage**. Sélectionnez la cible de stockage dans la liste et cliquez sur le bouton **Supprimer**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Utilisez [az hpc-cache storage-target remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) pour supprimer une cible de stockage du cache.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

Le fait de supprimer une cible de stockage supprime l’association du système de stockage avec ce système Azure HPC Cache, mais ne modifie pas le système de stockage back-end. Par exemple, si vous avez utilisé un conteneur Stockage Blob Azure, le conteneur et son contenu existent toujours une fois que vous l’avez supprimé du cache. Vous pouvez ajouter le conteneur à un autre Azure HPC Cache, le rajouter à ce cache ou le supprimer avec le portail Azure.

Toutes les modifications de fichiers stockées dans le cache sont écrites dans le système de stockage back-end avant la suppression de la cible de stockage. Ce processus peut prendre une heure ou plus si un grand nombre de données modifiées se trouvent dans le cache.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Modifier le chemin d’accès de l’espace de noms d’une cible de stockage blob

Les chemins d’espace de noms sont les chemins utilisés par les clients pour monter cette cible de stockage. (Pour en savoir plus, consultez [Planifier l’espace de noms agrégé](hpc-cache-namespace.md) et [Configurer l’espace de noms agrégé](add-namespace-paths.md)).

Le chemin de l’espace de noms est la seule mise à jour que vous pouvez effectuer sur une cible de stockage blob Azure. Utilisez le portail Azure ou Azure CLI pour le modifier.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Utilisez la page **Espace de noms** pour votre Azure HPC Cache. La page Espace de noms est décrite plus en détail dans l’article [Configurer l’espace de noms agrégé](add-namespace-paths.md).

Cliquez sur le nom du chemin d’accès que vous souhaitez modifier, puis créez le nouveau chemin dans la fenêtre d’édition qui s’affiche.

![Capture d’écran de la page Espace de noms après sélection d’un chemin d’accès d’espace de noms Blob. Les champs d’édition s’affichent dans un volet à droite.](media/update-namespace-blob.png)

Après avoir apporté les modifications souhaitées, cliquez sur **OK** pour mettre à jour la cible de stockage ou cliquez sur **Annuler** pour abandonner les modifications.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Pour modifier l’espace de noms d’une cible de stockage blob avec Azure CLI, utilisez la commande [az hpc-cache blob-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update). Seule la valeur `--virtual-namespace-path` peut être modifiée.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Mettre à jour cible de stockage NFS

Pour les cibles de stockage NFS, vous pouvez modifier ou ajouter des chemins d’accès d’espace de noms virtuels, modifier les valeurs d’exportation NFS ou de sous-répertoires vers lesquelles pointe un chemin d’accès et modifier le modèle d’utilisation.

Les cibles de stockage dans les caches avec certains types de paramètres DNS personnalisés disposent également d’un contrôle pour actualiser leurs adresses IP. (Ce type de configuration est rare.)

La procédure est indiquée ci-dessous :

* [Modifier les valeurs d’espaces de noms agrégés](#change-aggregated-namespace-values) (chemin d’espace de noms virtuel, stratégie d’accès, exportation et sous-répertoire d’exportation)
* [Modifier le modèle d’utilisation](#change-the-usage-model)
* [Actualiser le DNS](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>Modifier les valeurs d’espaces de noms agrégés

Vous pouvez utiliser le portail Azure ou Azure CLI pour modifier le chemin de l’espace de noms côté client, l’exportation de stockage et le sous-répertoire d’exportation (si utilisé).

Lisez les instructions de la page [Ajouter des chemins d’accès aux espaces de noms NFS](add-namespace-paths.md#nfs-namespace-paths) si vous avez besoin d’un rappel sur la création de plusieurs chemins d’accès valides sur une cible de stockage.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Utilisez la page **Espace de noms** de votre cache Azure HPC Cache pour mettre à jour les valeurs d’espace de noms. Cette page est décrite plus en détail dans l’article [Configurer l’espace de noms agrégé](add-namespace-paths.md).

![Capture d’écran de la page Espace de noms du portail avec la page de mise à jour NFS ouverte à droite](media/update-namespace-nfs.png)

1. Cliquez sur le nom du chemin d’accès que vous souhaitez modifier.
1. Utilisez la fenêtre Modifier pour entrer de nouvelles valeurs de chemin d’accès virtuel, d’exportation ou de sous-répertoire, ou pour sélectionner une autre stratégie d’accès.
1. Une fois que vous avez apporté les modifications souhaitées, cliquez sur **OK** pour mettre à jour la cible de stockage ou cliquez sur **Annuler** pour abandonner les modifications.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Utilisez l’option ``--junction`` de la commande [az hpc-cache nfs-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) pour modifier le chemin d’accès de l’espace de noms, l’exportation NFS ou le sous-répertoire d’exportation.

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

Le modèle d’utilisation influe sur le mode de conservation des données par le cache. Pour en savoir plus, voir [Choisir un modèle d’utilisation](hpc-cache-add-storage.md#choose-a-usage-model).

Pour modifier le modèle d’utilisation d’une cible de stockage NFS, utilisez l’une des méthodes suivantes.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Modifiez le modèle d’utilisation à partir de la page **Cibles de stockage** dans le portail Azure. Cliquez sur le nom de la cible de stockage à modifier.

![Capture d’écran de la page de modification d’une cible de stockage NFS](media/edit-storage-nfs.png)

Utilisez le sélecteur de liste déroulante pour choisir un nouveau modèle d’utilisation. Cliquez sur **OK** pour mettre à jour la cible de stockage ou cliquez sur **Annuler** pour abandonner les modifications.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Utilisez la commande [az hpc-cache nfs-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update).

La commande de mise à jour est quasiment identique à la commande que vous utilisez pour ajouter une cible de stockage NFS. Pour plus d’informations et pour voir des exemples, reportez-vous à [Créer une cible de stockage NFS](hpc-cache-add-storage.md#create-an-nfs-storage-target).

Pour modifier le modèle d’utilisation, mettez à jour l’option ``--nfs3-usage-model``. Exemple : ``--nfs3-usage-model WRITE_WORKLOAD_15``

Le nom du cache, le nom de la cible de stockage et les valeurs du groupe de ressources sont également requis.

Si vous souhaitez vérifier les noms des modèles d’utilisation, utilisez la commande [az hpc-cache usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

Si le cache est arrêté ou n’est pas sain, la mise à jour s’applique une fois que le cache est sain.

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>Mettre à jour l’adresse IP (configurations DNS personnalisées uniquement)

Si votre cache utilise une configuration DNS non définie par défaut, il est possible que l’adresse IP de votre cible de stockage NFS soit modifiée en raison des modifications DNS principales. Si votre serveur DNS modifie l’adresse IP du système de stockage principal, HPC Azure Cache peut perdre l’accès au système de stockage.

Dans l’idéal, vous devez utiliser le gestionnaire du système DNS personnalisé de votre cache pour planifier les mises à jour, car ces modifications rendent le stockage indisponible.

Si vous devez mettre à jour l’adresse IP fournie par le DNS d’une cible de stockage, un bouton apparaît dans la liste Cibles de stockage. Cliquez sur **Actualiser le DNS** pour interroger le serveur DNS personnalisé afin d’obtenir une nouvelle adresse IP.

![Capture d’écran de la liste Cibles de stockage. Pour une cible de stockage, le menu « ... » dans la colonne la plus à droite est ouvert et deux options s’affichent : Supprimer et Actualiser le DNS.](media/refresh-dns.png)

En cas de réussite, la mise à jour doit prendre moins de deux minutes. Vous ne pouvez actualiser qu’une cible de stockage à la fois. Attendez la fin de l’opération précédente avant d’en essayer une autre.

## <a name="update-an-adls-nfs-storage-target-preview"></a>Mettre à jour une cible de stockage ADLS-NFS (préversion)

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

* Pour en savoir plus sur ces options, voir [Ajouter des cibles de stockage](hpc-cache-add-storage.md).
* Pour plus d’informations sur l’utilisation des chemins d’accès virtuels, voir [Planifier l’espace de noms agrégé](hpc-cache-namespace.md).
