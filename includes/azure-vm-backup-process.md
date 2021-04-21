---
author: v-amallick
ms.service: Azure Backup
ms.topic: include
ms.date: 04/16/2021
ms.author: v-amallick
ms.openlocfilehash: e7af0819a94661a1008d96b7d0738c30a4a80c18
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716741"
---
1. Pour des machines virtuelles Azure sélectionnés pour la sauvegarde, Sauvegarde Azure démarre un travail de sauvegarde conformément à la planification de sauvegarde que vous spécifiez.
1. Lors de la première sauvegarde, une extension de sauvegarde est installée sur la machine virtuelle si celle-ci est en cours d’exécution.
    - Pour les machines virtuelles Windows, l’[extension VMSnapshot](../articles/virtual-machines/extensions/vmsnapshot-windows.md) est installée.
    - Pour les machines virtuelles Linux, l’[extension VMSnapshotLinux](../articles/virtual-machines/extensions/vmsnapshot-linux.md) est installée.
1. Pour les machines virtuelles Windows en cours d’exécution, le service Sauvegarde se coordonne avec le service VSS (Volume Shadow Copy Service) Windows pour prendre un instantané de cohérence d’application de la machine virtuelle.
    - Par défaut, Sauvegarde Azure effectue des sauvegardes VSS complètes.
    - Si la sauvegarde ne peut pas prendre d’instantané de cohérence d’application, elle prend un instantané cohérent au niveau fichier du stockage sous-jacent (parce qu’aucune écriture d’application n’a lieu quand la machine virtuelle est arrêtée).
1. Pour les machines virtuelles Linux, Sauvegarde Azure effectue une sauvegarde cohérente au niveau fichier. Pour les instantanés de cohérence d’application, vous devez personnaliser manuellement le pré-script et le post-script.
1. Une fois que le service Sauvegarde a pris l’instantané, il transfère les données au coffre.
    - La sauvegarde est optimisée grâce à la sauvegarde de chaque disque de machine virtuelle en parallèle.
    - Pour chaque disque sauvegardé, Sauvegarde Azure lit les blocs sur le disque, puis identifie et transfère uniquement les blocs de données ayant changé (le delta) depuis la sauvegarde précédente.
    - Les données d’instantanés peuvent ne pas être immédiatement copiées dans le coffre. Aux heures de pointe, cela peut prendre quelques heures. La durée de sauvegarde totale d’une machine virtuelle est inférieure à 24 heures pour les stratégies de sauvegarde quotidienne.
1. Les modifications apportées à une machine virtuelle Windows une fois la Sauvegarde Azure activée sur celle-ci sont les suivantes :
    - Le composant redistribuable de Microsoft Visual C++ 2013 (x64) – 12.0.40660 est installé sur la machine virtuelle
    - Le type de démarrage du Service VSS est passé de manuel à automatique
    - Le service Windows IaaSVmProvider est ajouté

1. Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé.

![Architecture de la sauvegarde des machines virtuelles Azure](../articles/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)
