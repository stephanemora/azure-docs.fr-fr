---
title: Migrer une ressource de stockage Azure d’Azure Allemagne vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources de gestion du stockage Azure d’Azure Germany vers Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: ce152dc6948e7d77ace2defede7f1cb0c0b744a8
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "122523973"
---
# <a name="migrate-storage-resources-to-global-azure"></a>Migrer des ressources de stockage vers Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Cet article contient des informations qui peuvent vous aider à migrer des ressources de stockage Azure d’Azure Germany vers Azure global.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="blobs"></a>Objets blob

AzCopy est un outil gratuit que vous pouvez utiliser pour copier des objets blob, des fichiers et des tableaux. AzCopy fonctionne pour les migrations Azure à Azure, local à Azure et Azure à local. Utilisez AzCopy pour votre migration afin de copier des objets blob directement d’Azure Germany vers Azure global.

Si vous n'utilisez pas de disques managés pour votre machine virtuelle source, utilisez AzCopy pour copier les fichiers .vhd vers l’environnement cible. Sinon, vous devez effectuer quelques étapes au préalable. Pour plus d’informations, consultez [Recommandations pour les disques managés](#managed-disks).

L’exemple suivant explique le fonctionnement d’AzCopy. Pour accéder à une référence complète, consultez la [documentation AzCopy](../storage/common/storage-use-azcopy-v10.md).

AzCopy utilise les termes **Source** et **Dest**, exprimés comme URI. Les URI pour Azure Germany ont toujours ce format :

```http
https://<storageaccountname>.blob.core.cloudapi.de/<containername>/<blobname>
```

Les URI pour Azure global ont toujours ce format :

```http
https://<storageaccountname>.blob.core.windows.net/<containername>/<blobname>
```

Vous obtenez les trois parties de l’URI (*storageaccountname*, *containername*, *blobname*) à partir du portail, à l’aide de PowerShell ou de l’interface de ligne de commande Azure. Le nom de l’objet blob peut faire partie de l’URI, ou il peut être fourni comme modèle, par exemple *vm121314.vhd*.

Vous avez également besoin des clés du compte de stockage pour accéder au compte Stockage Azure. Obtenez-les à partir du portail, en utilisant PowerShell ou l’interface de ligne de commande. Par exemple :

```powershell
Get-AzStorageAccountKey -Name <saname> -ResourceGroupName <rgname>
```

Comme toujours, vous n'avez besoin que d'une seule des deux clés pour chaque compte de stockage.

Exemple :

Partie URI | exemple de valeur
-------- | --------------
storageAccount source | `migratetest`
Conteneur source | `vhds`
Objet blob source | `vm-121314.vhd`
storageAccount cible | `migratetarget`
Conteneur cible | `targetcontainer`

Cette commande copie un disque dur virtuel d’Azure Germany vers Azure global (les touches sont abrégées pour plus de lisibilité) :

```cmd
azcopy -v /source:https://migratetest.blob.core.cloudapi.de/vhds /sourcekey:"0LN...w==" /dest:https://migratetarget.blob.core.windows.net/targetcontainer /DestKey:"o//ucDi5TN...w==" /Pattern:vm-121314.vhd
```

Pour obtenir une copie cohérente du disque dur virtuel, éteignez la machine virtuelle avant de copier le disque dur virtuel. Prévoyez un temps d’arrêt pour l’activité de copie. Une fois le disque dur virtuel copié, [régénérez votre machine virtuelle dans l'environnement cible](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

Pour plus d’informations :

- Consultez la [documentation AzCopy](../storage/common/storage-use-azcopy-v10.md).
- Découvrez comment [créer une machine virtuelle à partir de disques restaurés](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="managed-disks"></a>Disques managés

Disques managés Azure simplifie la gestion des disques pour les machines virtuelles Azure infrastructure as a service (IaaS) en gérant les comptes de stockage associés au disque de la machine virtuelle. 

Comme vous n'avez pas d'accès direct au fichier .vhd, vous ne pouvez pas utiliser directement des outils comme AzCopy pour copier vos fichiers (voir [Objets blob](#blobs)). La solution de contournement consiste à exporter d’abord le disque managé en obtenant un URI de signature d'accès partagé temporaire, puis à télécharger ou à copier le fichier en utilisant ces informations. Les sections suivantes montrent un exemple permettant d'obtenir l’URI de signature d’accès partagé et ce qu'il faut en faire.

### <a name="step-1-get-the-shared-access-signature-uri"></a>Étape 1 : Obtenir l’URI de signature d’accès partagé

1. Dans le portail, recherchez votre disque managé. (Il figure dans le même groupe de ressources que votre machine virtuelle. Le type de ressource est **Disque**.)
1. Sur la page **Vue d’ensemble**, sélectionnez le bouton **Exporter** dans le menu supérieur (vous devez d'abord fermer et libérer votre machine virtuelle, ou la détacher).
1. Définissez le délai d'expiration de l'URI (3 600 secondes par défaut).
1. Générez une URL (cette étape ne devrait prendre que quelques secondes).
1. Copiez l'URL (elle n’apparaît qu’une seule fois).

### <a name="step-2-azcopy"></a>Étape 2 : AzCopy

Pour obtenir des exemples d'utilisation d'AzCopy, consultez [Objets blob](#blobs). Utilisez AzCopy (ou un outil similaire) pour copier le disque directement de votre environnement source vers l'environnement cible. Dans AzCopy, vous devez diviser l'URI en deux parties : l'URI de base et la signature d’accès partagé. La partie signature d'accès partagé de l'URI commence par le caractère «  **?**  ». Le portail fournit cet URI comme URI de signature d'accès partagé :

```http
https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D>
```

Les commandes suivantes montrent les paramètres source pour AzCopy :

```cmd
/source:"https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd"
```

```cmd
/sourceSAS:" ?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D"
```

Voici la condition complète :

```cmd
azcopy -v /source:"https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd" /sourceSAS:"?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D" /dest:"https://migratetarget.blob.core.windows.net/targetcontainer/newdisk.vhd" /DestKey:"o//ucD... Kdpw=="
```

### <a name="step-3-create-a-new-managed-disk-in-the-target-environment"></a>Étape 3 : Créer un nouveau disque managé dans l’environnement cible

Plusieurs choix s’offrent à vous pour créer un disque managé. Voici comment procéder dans le portail Azure :

1. Dans le portail, sélectionnez **Nouveau** > **Disque managé** > **Créer**.
1. Entrez un nom pour le nouveau disque.
1. Sélectionnez un groupe de ressources.
1. Sous **Type de source**, sélectionnez **Objet blob de stockage**. Copiez ensuite l'URI de destination à partir de la commande AzCopy, ou naviguez pour sélectionner l'URI de destination.
1. Si vous avez copié un disque de système d’exploitation, sélectionnez le type **Système d’exploitation**. Pour les autres types de disque, sélectionnez **Créer**.

### <a name="step-4-create-the-vm"></a>Étape 4 : Créer la machine virtuelle

Comme indiqué précédemment, il existe de multiples façons de créer une machine virtuelle en utilisant ce nouveau disque managé. Voici deux options :

- Dans le portail, sélectionnez le disque, puis choisissez **Créer une machine virtuelle**. Définissez les autres paramètres de votre machine virtuelle, comme d'habitude.
- Pour PowerShell, consultez [Créer une machine virtuelle à partir de disques restaurés](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

Pour plus d’informations :

- Découvrez comment exporter vers un disque [via une API](/rest/api/compute/disks/grantaccess) en obtenant un URI de signature d'accès partagé. 
- Découvrez comment créer un disque managé [via une API](/rest/api/compute/disks/createorupdate#create-a-managed-disk-by-importing-an-unmanaged-blob-from-a-different-subscription.) à partir d'un objet blob non géré.

## <a name="tables"></a>Tables

Vous pouvez migrer des tables dans Azure à l’aide d’Explorateur Stockage. Explorateur Stockage est un outil permettant de gérer vos ressources de stockage cloud Azure. À l’aide d’Explorateur Stockage, vous pouvez vous connecter au compte de stockage Azure Allemagne source et copier des tables vers le compte de stockage Azure global cible.

Pour commencer, installez [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

### <a name="connect-to-source"></a>Se connecter à la source

Vous utilisez Explorateur Stockage pour copier des tables à partir du compte de stockage Azure source. 

Connectez Explorateur Stockage à vos ressources de tables sources dans Microsoft Azure Allemagne. Vous pouvez [vous connecter pour accéder aux ressources de votre abonnement](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) ou vous pouvez [attacher à des ressources Stockage spécifiques](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource). 

### <a name="connect-to-target"></a>Se connecter à la cible

Vous utilisez Explorateur Stockage pour coller des tables dans le compte de stockage Azure cible.

Connectez Explorateur Stockage à votre abonnement Microsoft Azure cible ou à Stockage Azure. Vous pouvez [vous connecter pour accéder aux ressources de votre abonnement](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) ou vous pouvez [attacher à des ressources Stockage spécifiques](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource). 


### <a name="migrate-tables"></a>migrez les tables

Copiez des tableaux d’Azure Allemagne vers Azure global à l’aide d’Explorateur Stockage. Vous pouvez copier des tables en cliquant avec le bouton droit sur la table que vous souhaitez copier et en choisissant **Copier la table** dans le menu contextuel. L’exemple suivant montre la copie de la table *testmigrationtable* depuis un *abonnement Azure Allemagne*.

![Menu Copier la table sélectionné dans l’abonnement Azure Allemagne](./media/germany-migration-storage/copy-table.png)

Collez la table dans le compte de stockage Azure cible à l’aide d’Explorateur Stockage. Vous pouvez coller des tables en cliquant avec le bouton droit sur le nœud *Tables* dans le compte de stockage Azure cible. L’exemple suivant montre le collage de la table *testmigrationtable* dans un compte de stockage Azure connecté.
 
![Menu Coller la table sélectionné dans le compte de stockage Azure cible](./media/germany-migration-storage/paste-table.png)

Répétez les étapes de copier-coller pour chaque table à migrer.

## <a name="file-shares"></a>Partages de fichiers

Utilisez AzCopy pour votre migration afin de copier des partages de fichiers directement d’Azure Allemagne vers Azure global. AzCopy est un outil gratuit que vous pouvez utiliser pour copier des objets blob, des fichiers et des tableaux.

Pour commencer, téléchargez [AzCopy](https://aka.ms/downloadazcopy) et installez-le.

AzCopy utilise les termes **Source** et **Dest**, exprimés comme URI. Les URI pour Azure Germany ont toujours ce format :

```http
https://<storageaccountname>.blob.core.cloudapi.de/<filesharename>
```

Les URI pour Azure global ont toujours ce format :

```http
https://<storageaccountname>.blob.core.windows.net/<filesharename>
```
Vous avez besoin d’un jeton SAP de compte de stockage pour accéder au compte de stockage Azure. 

L’exemple de commande suivant copie tous les partages de fichiers, répertoires et fichiers d’un compte de stockage Azure Allemagne dans un compte de stockage Azure global. Pour accéder à une référence complète, consultez la [documentation AzCopy](../storage/common/storage-use-azcopy-v10.md).

Partie URI | Valeur d'exemple
-------- | --------------
storageAccount source | `migratetest`
Partage de fichiers source | `sourcefileshare`
storageAccount cible | `migratetarget`
Partage de fichiers cible | `targetfileshare`

```cmd
azcopy copy "https://migratetest.blob.core.cloudapi.de/sourcefileshare?<SAS-token>" "https://migratetarget.blob.core.windows.net/targetfileshare?<SAS-token>" --recursive=true
```

Pour plus d’informations sur AzCopy, consultez la [documentation AzCopy](../storage/common/storage-use-azcopy-v10.md) et [Transférer des données avec AzCopy et le stockage de fichiers](../storage/common/storage-use-azcopy-files.md#copy-files-between-storage-accounts).

## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur les outils, techniques et suggestions pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Réseau](./germany-migration-networking.md)
- [Web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils d'administration](./germany-migration-management-tools.md)
- [Média](./germany-migration-media.md)
