---
title: Mettre à jour les cibles de stockage Azure HPC Cache
description: Comment modifier les cibles de stockage Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rohogue
ms.openlocfilehash: 115e75c0149a35104d9c3696710bf8231a98743d
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168520"
---
# <a name="edit-storage-targets"></a>Modifier les cibles de stockage

Vous pouvez modifier les cibles de stockage, afin de changer la valeur de certaines de leurs propriétés. Différentes propriétés peuvent être modifiées, en fonction du type de stockage :

* Pour les cibles de stockage d’objets blob, vous pouvez modifier le chemin d’accès à l’espace de noms.

* Pour les cibles de stockage NFS, vous pouvez modifier les propriétés suivantes :

  * Chemin de l’espace de noms
  * Modèle d’utilisation
  * Exportation
  * Exporter un sous-répertoire

Vous ne pouvez pas modifier le nom, le type ou le système de stockage back-end d’une cible de stockage (conteneur d’objets blob ou nom d’hôte/adresse IP NFS). Si vous avez besoin de modifier ces propriétés, supprimez la cible de stockage et créez-en une autre, avec la nouvelle valeur.

Pour modifier une cible de stockage, cliquez sur son nom afin d’ouvrir la page de détails correspondante. Certains champs de la page peuvent être modifiés.

![Capture d’écran de la page de modification d’une cible de stockage NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Mettre à jour cible de stockage NFS

Pour une cible de stockage NFS, vous pouvez mettre à jour plusieurs propriétés. (Reportez-vous à la capture d’écran ci-dessus pour obtenir un exemple des modifications possibles.)

* **Modèle d’utilisation** : le modèle d’utilisation influe sur le mode de conservation des données par le cache. Pour en savoir plus, voir [Choisir un modèle d’utilisation](hpc-cache-add-storage.md#choose-a-usage-model).
* **Chemin de l’espace de noms virtuels** : chemin d’accès utilisé par les clients pour monter cette cible de stockage. Pour en savoir plus, voir [Planifier l’espace de noms agrégé](hpc-cache-namespace.md).
* **Chemin d’exportation NFS** : chemin d’exportation du système de stockage à utiliser pour ce chemin d’accès à l’espace de noms.
* **Chemin de sous-répertoire** : sous-répertoire (sous l’exportation) à associer à ce chemin d’accès à l’espace de noms. Laissez ce champ vide si vous n’avez pas besoin de spécifier un sous-répertoire.

Chaque chemin d’accès à l’espace de noms a besoin d’une combinaison unique de chemin d’exportation et de sous-répertoire. Autrement dit, vous ne pouvez pas créer deux chemins d’accès côté client différents dans le même répertoire sur le système de stockage back-end.

Après avoir apporté les modifications souhaitées, cliquez sur **OK** pour mettre à jour la cible de stockage ou cliquez sur **Annuler** pour abandonner les modifications.

## <a name="update-an-azure-blob-storage-target"></a>Mettre à jour une cible de stockage d’objets blob Azure

La page de détails d’une cible de stockage d’objets blob vous permet de modifier le chemin d’accès à l’espace de noms virtuels.

![Capture d’écran de la page de modification d’une cible de stockage d’objets blob](media/hpc-cache-edit-storage-blob.png)

Lorsque vous avez terminé, cliquez sur **OK** pour mettre à jour la cible de stockage ou cliquez sur **Annuler** pour abandonner les modifications.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur ces options, voir [Ajouter des cibles de stockage](hpc-cache-add-storage.md).
* Pour plus d’informations sur l’utilisation des chemins d’accès virtuels, voir [Planifier l’espace de noms agrégé](hpc-cache-namespace.md).
