---
title: Ajouter du stockage à Azure HPC Cache
description: Comment définir des cibles de stockage pour qu’Azure HPC Cache puisse utiliser votre système NFS local ou des conteneurs d’objets blob Azure dans le but de stocker des fichiers à long terme
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/23/2020
ms.author: v-erkel
ms.openlocfilehash: 4c3ef79806d29b188eb2738919bf912cfedc8ef1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85513878"
---
# <a name="add-storage-targets"></a>Ajouter des cibles de stockage

Les *cibles de stockage* constituent un stockage back-end pour des fichiers accessibles via un Azure HPC Cache. Vous pouvez ajouter un stockage NFS (comme un système local), ou stocker des données dans un objet blob Azure.

Vous pouvez définir jusqu’à dix cibles de stockage différentes pour un même cache. Le cache présente toutes les cibles de stockage d’un espace de noms agrégé.

N’oubliez pas que les exportations de stockage doivent être accessibles à partir du réseau virtuel de votre cache. Pour le stockage matériel local, vous devrez peut-être configurer un serveur DNS capable de résoudre les noms d’hôtes pour l’accès au stockage NFS. Pour plus d’informations, lisez [Accès DNS](hpc-cache-prereqs.md#dns-access).

Ajoutez des cibles de stockage après avoir créé votre cache. La procédure est légèrement différente selon que vous ajoutez du stockage Blob Azure ou une exportation NFS. Vous trouverez ci-dessous des informations détaillées pour chacune de ces options.

Cliquez sur l’image ci-dessous pour regarder une [démonstration vidéo](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) de la création d’un cache et de l’ajout d’une cible de stockage.

[![Miniature de vidéo : Azure HPC Cache : Configuration (cliquez pour visiter la page vidéo)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="open-the-storage-targets-page"></a>Ouvrir la page des cibles de stockage

À partir du portail Azure, ouvrez votre instance de cache, puis cliquez sur **Cibles de stockage** dans la barre latérale gauche. La page des cibles de stockage répertorie toutes les cibles existantes et fournit un lien permettant d’en ajouter une nouvelle.

![capture d’écran du lien Cibles de stockage dans la barre latérale, sous l’en-tête Configurer, qui se trouve entre les en-têtes Paramètres et Surveillance](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Ajouter une cible de stockage Blob Azure

Les nouvelles cibles de stockage Blob nécessitent un conteneur d’objets blob vide ou un conteneur comprenant des données au format du système de fichiers cloud Azure HPC Cache. Pour plus d’informations sur le préchargement d’un conteneur d’objets blob, consultez [Déplacer les données dans le stockage d’objets blob Azure](hpc-cache-ingest.md).

Vous pouvez créer un conteneur à partir de cette page avant de l’ajouter.

![capture d’écran de la page Ajouter une cible de stockage, où ont été renseignées les informations relatives à la nouvelle cible de stockage d’objets blob Azure](media/hpc-cache-add-blob.png)

Pour définir un conteneur d’objets blob Azure, entrez les informations ci-dessous.

* **Nom de la cible de stockage** - Définissez un nom permettant d’identifier cette cible de stockage dans Azure HPC Cache.
* **Type de cible** : choisissez **Objet blob**.
* **Compte de stockage** : sélectionnez le compte que vous souhaitez utiliser.

  Vous devez autoriser l’instance de cache à accéder au compte de stockage, en suivant la procédure décrite dans [Ajouter les rôles d’accès](#add-the-access-control-roles-to-your-account).

  Pour plus d’informations sur le type de compte de stockage que vous pouvez utiliser, lisez [Exigences relatives au stockage Blob](hpc-cache-prereqs.md#blob-storage-requirements).

* **Conteneur de stockage** : sélectionnez le conteneur d’objets blob pour cette cible ou cliquez sur **Créer nouveau**.

  ![capture d’écran de la boîte de dialogue permettant de spécifier le nom et le niveau d’accès (privé) du nouveau conteneur](media/add-blob-new-container.png)

* **Chemin de l’espace de noms virtuels** - Définissez le chemin côté client de cette cible de stockage. Pour plus d’informations sur la fonctionnalité Espace de noms virtuels, consultez [Configurer un espace de noms agrégé](hpc-cache-namespace.md).

Lorsque vous avez terminé, cliquez sur **OK** pour ajouter la cible de stockage.

> [!NOTE]
> Si votre pare-feu de compte de stockage est défini de façon à limiter l’accès uniquement aux « réseaux sélectionnés », utilisez la solution de contournement temporaire décrite dans [Paramètres du pare-feu de compte de Stockage Blob](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Ajouter les rôles de contrôle d’accès à votre compte

Azure HPC Cache utilise le [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) pour autoriser le service de cache à accéder aux cibles de stockage Blob Azure de votre compte de stockage.

Le propriétaire du compte de stockage doit ajouter explicitement les rôles [Contributeur de comptes de stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) et [Contributeur aux données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) pour l’utilisateur « HPC Cache Resource Provider ».

Vous pouvez le faire à l’avance, ou en cliquant sur le lien de la page à partir de laquelle vous ajoutez une cible de stockage Blob. Rappelez-vous qu’il peut falloir jusqu’à cinq minutes pour que les paramètres de rôle se propagent dans l’environnement Azure. Par conséquent, après avoir ajouté les rôles, vous devez attendre quelques minutes avant de créer une cible de stockage.

Étapes nécessaires pour ajouter des rôles RBAC :

1. Ouvrez la page **Contrôle d’accès (IAM)** du compte de stockage (le lien de la page **Ajouter une cible de stockage** ouvre automatiquement cette page pour le compte sélectionné).

1. Cliquez sur le **+** en haut de la page, puis choisissez **Ajouter une attribution de rôle**.

1. Sélectionnez le rôle « Contributeur de comptes de stockage » dans la liste.

1. Dans le champ **Attribuer l’accès à**, conservez la valeur par défaut (Utilisateur, groupe ou principal de service Azure AD).  

1. Dans le champ **Sélectionner**, recherchez « hpc ».  Cette chaîne doit correspondre au principal de service nommé « HPC Cache Resource Provider ». Cliquez sur ce principal pour le sélectionner.

   > [!NOTE]
   > Si la recherche de « hpc » ne retourne aucun résultat, essayez la chaîne « storagecache » à la place. Il se peut que les utilisateurs qui ont participé à la préversion (avant la mise à disposition générale) doivent utiliser l’ancien nom du principal de service.

1. Cliquez sur le bouton **Enregistrer** au bas de l’écran.

1. Répétez ce processus pour attribuer le rôle « Contributeur aux données Blob du stockage ».  

![capture d’écran de l’interface graphique utilisateur Ajouter une attribution de rôle](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Ajouter une cible de stockage NFS

Une cible de stockage NFS contient plus de champs que la cible de Stockage Blob. Ces champs spécifient comment atteindre l’exportation de stockage et comment mettre en cache efficacement ses données. En outre, une cible de stockage NFS vous permet de créer plusieurs chemins d’accès d’espace de noms si l’hôte NFS a plusieurs exportations disponibles.

![Capture d’écran de la page Ajouter la cible de stockage avec le type de cible défini sur NFS](media/add-nfs-target.png)

> [!NOTE]
> Avant de créer une cible de stockage NFS, assurez-vous que votre système de stockage est accessible à partir de l’Azure HPC Cache et répond aux exigences d’autorisation. La création d’une cible de stockage échoue si le cache ne peut pas accéder au système de stockage. Pour plus d’informations, consultez [Conditions requises pour le stockage NFS](hpc-cache-prereqs.md#nfs-storage-requirements) et [Résolution des problèmes de configuration NAS et des cibles de stockage NFS](troubleshoot-nas.md).

Fournissez les informations suivantes pour une cible de stockage NFS :

* **Nom de la cible de stockage** - Définissez un nom permettant d’identifier cette cible de stockage dans Azure HPC Cache.

* **Type de cible** - Choisissez **NFS**.

* **Nom d’hôte** - Entrez l’adresse IP ou le nom de domaine complet de votre système de stockage NFS (utilisez un nom de domaine uniquement si votre cache a accès à un serveur DNS capable de résoudre ce nom).

* **Modèle d’utilisation** : choisissez l’un des profils de mise en cache des données en fonction de votre workflow, comme décrit dans [Choisir un modèle d’utilisation](#choose-a-usage-model).

### <a name="nfs-namespace-paths"></a>Chemin d'espaces de noms NFS

Une cible de stockage NFS peut avoir plusieurs chemins d’accès virtuels, à condition que chaque chemin d’accès représente une exportation ou un sous-répertoire différent sur le même système de stockage.

Créez tous les chemins d’accès à partir d’une cible de stockage.

Vous pouvez [ajouter et modifier des chemins d’accès d’espace de noms](hpc-cache-edit-storage.md) sur une cible de stockage à tout moment.

Renseignez ces valeurs pour chaque chemin d’espace de noms :

* **Chemin de l’espace de noms virtuels** - Définissez le chemin côté client de cette cible de stockage. Pour plus d’informations sur la fonctionnalité Espace de noms virtuels, consultez [Configurer un espace de noms agrégé](hpc-cache-namespace.md).

* **Chemin d’exportation NFS** - Entrez le chemin de l’exportation NFS.

* **Chemin du sous-répertoire** - Si vous souhaitez monter un sous-répertoire de l’exportation, entrez-le ici. Sinon, laissez ce champ vide.

Lorsque vous avez terminé, cliquez sur **OK** pour ajouter la cible de stockage.

### <a name="choose-a-usage-model"></a>Choisir un modèle d’utilisation
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Lorsque vous créez une cible de stockage qui pointe vers un système de stockage NFS, vous devez choisir le modèle d’utilisation de cette cible. Ce modèle détermine la façon dont vos données sont mises en cache.

Vous disposez de trois options :

* **Lire les écritures lourdes et peu fréquentes** : utilisez cette option si vous souhaitez accélérer l’accès en lecture aux fichiers qui sont statiques ou rarement modifiés.

  Cette option met en cache les fichiers que les clients lisent, mais transmet immédiatement les écritures au stockage backend. Les fichiers stockés dans le cache ne sont jamais comparés aux fichiers sur le volume de stockage NFS.

  N’utilisez pas cette option s’il existe un risque de modification directe d’un fichier sur le système de stockage sans l’avoir d’abord écrit dans le cache. Si cela se produit, la version mise en cache du fichier n’est jamais mise à jour avec les modifications du backend, et le jeu de données peut devenir incohérent.

* **Opérations d’écriture supérieures à 15 %**  : cette option accélère les performances de lecture et d’écriture. Quand vous utilisez cette option, tous les clients doivent accéder aux fichiers par le biais d’Azure HPC Cache au lieu de monter le stockage backend directement. Les fichiers mis en cache comportent des modifications récentes qui ne sont pas stockées sur le backend.

  Dans ce modèle d’utilisation, les fichiers du cache ne sont pas comparés aux fichiers sur le stockage backend. La version mise en cache du fichier est supposée être plus récente. Un fichier modifié dans le cache est écrit dans le système de stockage back-end après qu’il est resté dans le cache pendant une heure sans aucune modification supplémentaire.

* **Les clients écrivent dans la cible NFS en ignorant le cache** : choisissez cette option si des clients dans votre workflow écrivent des données directement dans le système de stockage sans écrire au préalable dans le cache. Les fichiers demandés par les clients sont mis en cache, mais toute modification apportée à ces fichiers à partir du client est immédiatement retransmise au système de stockage backend.

  Avec ce modèle d’utilisation, les fichiers du cache sont fréquemment comparés aux versions backend pour y rechercher des mises à jour. Cette vérification permet de modifier les fichiers en dehors du cache tout en conservant la cohérence des données.

Ce tableau récapitule les différences entre les modèles d’utilisation :

| Modèle d’utilisation | Mode de mise en cache | Vérification backend | Délai maximal d’écriture différée |
| ---- | ---- | ---- | ---- |
| Lire les écritures lourdes et peu fréquentes | Lire | Jamais | None |
| Opérations d’écriture supérieures à 15 % | Lecture/écriture | Jamais | 1 heure |
| Les clients ignorent le cache | Lire | 30 secondes | None |

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé les cibles de stockage, vous pouvez effectuer l’une des tâches suivantes :

* [Monter le cache Azure HPC Cache](hpc-cache-mount.md)
* [Déplacer les données dans le stockage d’objets blob Azure](hpc-cache-ingest.md)

Si vous devez mettre à jour des paramètres, vous pouvez [modifier une cible de stockage](hpc-cache-edit-storage.md).
