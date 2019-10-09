---
title: Ajouter du stockage à Azure HPC Cache (préversion)
description: Comment définir des cibles de stockage pour qu’Azure HPC Cache puisse utiliser votre système NFS local ou des conteneurs d’objets blob Azure dans le but de stocker des fichiers à long terme
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: rohogue
ms.openlocfilehash: 302d727ede9604d11972eaa8f46a3e27f204858f
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710036"
---
# <a name="add-storage-targets"></a>Ajouter des cibles de stockage

Les *cibles de stockage* sont des stockages back-end destinés aux fichiers qui sont accessibles via une instance d’Azure HPC Cache. Vous pouvez ajouter un stockage NFS (comme un système local), ou stocker des données dans un objet blob Azure.

Vous pouvez définir jusqu’à dix cibles de stockage différentes pour un même cache. Le cache présente toutes les cibles de stockage d’un espace de noms agrégé.

N’oubliez pas que les exportations de stockage doivent être accessibles à partir du réseau virtuel de votre cache. Pour le stockage matériel local, vous devrez peut-être configurer un serveur DNS capable de résoudre les noms d’hôtes pour l’accès au stockage NFS. Pour plus d’informations, lisez [Accès DNS](hpc-cache-prereqs.md#dns-access).

Ajoutez des cibles de stockage après avoir créé votre cache. La procédure est légèrement différente selon que vous ajoutez du stockage Blob Azure ou une exportation NFS. Vous trouverez ci-dessous des informations détaillées pour chacune de ces options.

## <a name="open-the-storage-targets-page"></a>Ouvrir la page des cibles de stockage

À partir du portail Azure, ouvrez votre instance de cache, puis cliquez sur **Cibles de stockage** dans la barre latérale gauche. La page des cibles de stockage répertorie toutes les cibles existantes et fournit un lien permettant d’en ajouter une nouvelle.

![capture d’écran du lien Cibles de stockage dans la barre latérale, sous l’en-tête Configurer, qui se trouve entre les en-têtes Paramètres et Surveillance](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Ajouter une cible de stockage Blob Azure

Les nouvelles cibles de stockage Blob nécessitent un conteneur d’objets blob vide ou un conteneur comprenant des données au format du système de fichiers cloud Azure HPC Cache. Pour plus d’informations sur le préchargement d’un conteneur d’objets blob, consultez [Déplacer les données dans le stockage d’objets blob Azure](hpc-cache-ingest.md).

Pour définir un conteneur d’objets blob Azure, entrez les informations ci-dessous.

![capture d’écran de la page Ajouter une cible de stockage, où ont été renseignées les informations relatives à la nouvelle cible de stockage d’objets blob Azure](media/hpc-cache-add-blob.png)

<!-- need to replace screenshot after note text is updated with both required RBAC roles -->

* **Nom de la cible de stockage** : définissez un nom permettant d’identifier cette cible de stockage dans Azure HPC Cache.
* **Type de cible** : choisissez **Objet blob**.
* **Compte de stockage** : sélectionnez le compte qui comprend le conteneur à référencer.

  Vous devez autoriser l’instance de cache à accéder au compte de stockage, en suivant la procédure décrite dans [Ajouter les rôles d’accès](#add-the-access-control-roles-to-your-account).
* **Conteneur de stockage** : sélectionnez le conteneur d’objets blob pour cette cible.

* **Chemin de l’espace de noms virtuels** - Définissez le chemin côté client de cette cible de stockage. Pour plus d’informations sur la fonctionnalité Espace de noms virtuels, consultez [Configurer un espace de noms agrégé](hpc-cache-namespace.md).

Lorsque vous avez terminé, cliquez sur **OK** pour ajouter la cible de stockage.

### <a name="add-the-access-control-roles-to-your-account"></a>Ajouter les rôles de contrôle d’accès à votre compte

Azure HPC Cache utilise le [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) pour autoriser l’application de cache à accéder aux cibles de stockage Blob Azure de votre compte de stockage.

Le propriétaire du compte de stockage doit ajouter explicitement les rôles [Contributeur de comptes de stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) et [Contributeur aux données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) pour l’utilisateur « StorageCache Resource Provider ».

Vous pouvez le faire à l’avance, ou en cliquant sur le lien de la page à partir de laquelle vous ajoutez une cible de stockage Blob.

Étapes nécessaires pour ajouter des rôles RBAC :

1. Ouvrez la page **Contrôle d’accès (IAM)** du compte de stockage (le lien de la page **Ajouter une cible de stockage** ouvre automatiquement cette page pour le compte sélectionné).

1. Cliquez sur le **+** en haut de la page, puis choisissez **Ajouter une attribution de rôle**.

1. Sélectionnez le rôle « Contributeur de comptes de stockage » dans la liste.

1. Dans le champ **Attribuer l’accès à**, conservez la valeur par défaut (Utilisateur, groupe ou principal de service Azure AD).  

1. Dans le champ **Sélectionner**, recherchez « storagecache ».  Cette chaîne doit correspondre au principal de sécurité nommé « HPC Cache Resource Provider ». Cliquez sur ce principal pour le sélectionner.

1. Cliquez sur le bouton **Enregistrer** pour ajouter l’attribution de rôle au compte de stockage.

1. Répétez ce processus pour attribuer le rôle « Contributeur aux données Blob du stockage ».  

![capture d’écran de l’interface graphique utilisateur Ajouter une attribution de rôle](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Ajouter une cible de stockage NFS

Une cible de stockage NFS contient des champs supplémentaires permettant de spécifier comment atteindre l’exportation de stockage et comment mettre en cache efficacement ses données. Vous pouvez aussi créer plusieurs chemins d’espace de noms à partir d’un hôte NFS si plusieurs exportations sont disponibles.

![Capture d’écran de la page Ajouter la cible de stockage avec le type de cible défini sur NFS](media/hpc-cache-add-nfs-target.png)

Fournissez les informations suivantes pour une cible de stockage NFS :

* **Nom de la cible de stockage** - Définissez un nom permettant d’identifier cette cible de stockage dans Azure HPC Cache.

* **Type de cible** - Choisissez **NFS**.

* **Nom d’hôte** - Entrez l’adresse IP ou le nom de domaine complet de votre système de stockage NFS (utilisez un nom de domaine uniquement si votre cache a accès à un serveur DNS capable de résoudre ce nom).

* **Modèle d’utilisation** - Choisissez l’un des profils de mise en cache des données en fonction de votre workflow, comme décrit dans [Choisir un modèle d’utilisation](#choose-a-usage-model).

### <a name="nfs-namespace-paths"></a>Chemin d'espaces de noms NFS

Une cible de stockage NFS peut avoir plusieurs chemins d’accès virtuels, à condition que chaque chemin d’accès représente une exportation ou un sous-répertoire différent sur le même système de stockage.

Créez tous les chemins d’accès à partir d’une cible de stockage.
<!-- You can create multiple namespace paths to represent different exports on the same NFS storage system, but you must create them all from one storage target. -->

Renseignez ces valeurs pour chaque chemin d’espace de noms :

* **Chemin de l’espace de noms virtuels** - Définissez le chemin côté client de cette cible de stockage. Pour plus d’informations sur la fonctionnalité Espace de noms virtuels, consultez [Configurer un espace de noms agrégé](hpc-cache-namespace.md).

<!--  The virtual path should start with a slash ``/``. -->

* **Chemin d’exportation NFS** - Entrez le chemin de l’exportation NFS.

* **Chemin du sous-répertoire** - Si vous souhaitez monter un sous-répertoire de l’exportation, entrez-le ici. Sinon, laissez ce champ vide.

Lorsque vous avez terminé, cliquez sur **OK** pour ajouter la cible de stockage.

### <a name="choose-a-usage-model"></a>Choisir un modèle d’utilisation
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Lorsque vous créez une cible de stockage qui pointe vers un système de stockage NFS, vous devez choisir le *modèle d’utilisation* de cette cible. Ce modèle détermine la façon dont vos données sont mises en cache.

* Read heavy (Écritures lourdes) : si vous utilisez principalement le cache pour accélérer l’accès en lecture aux données, choisissez cette option.

* Lecture/écriture : si les clients utilisent le cache pour la lecture et l’écriture, choisissez cette option.

* Clients bypass the cache (Les clients contournent le cache) : choisissez cette option si vos clients écrivent des données directement dans le système de stockage sans écrire au préalable dans le cache.

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé les cibles de stockage, vous pouvez effectuer l’une des tâches suivantes :

* [Monter le cache Azure HPC Cache](hpc-cache-mount.md)
* [Déplacer les données dans le stockage d’objets blob Azure](hpc-cache-ingest.md)
