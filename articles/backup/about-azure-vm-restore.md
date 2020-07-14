---
title: À propos du processus de restauration de machine virtuelle Azure
description: Découvrez comment le service Sauvegarde Azure restaure les machines virtuelles Azure
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: b9a2636a2144ea40457bdc3d88786785cb012e0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84325674"
---
# <a name="about-azure-vm-restore"></a>À propos de la restauration de machine virtuelle Azure

Cet article décrit la manière dont le [service Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-overview) restaure les machines virtuelles Azure. Il existe un certain nombre d’options de restauration. Nous allons aborder les différents scénarios qu’ils prennent en charge.

## <a name="concepts"></a>Concepts

- **Point de récupération** (également appelé **Point de restauration**) : un point de récupération est une copie des données d’origine en cours de sauvegarde.

- **Niveau (instantané et coffre)**  :  la sauvegarde de machine virtuelle Azure s’effectue en deux phases :

  - Durant la phase 1, la capture instantanée prise est stockée avec le disque. On parle alors **niveau d’instantané**. Les restaurations du niveau d’instantané sont plus rapides (que celles opérées à partir du coffre), car elles font l’économie du temps d’attente nécessaire pour la copie des instantanés dans le coffre avant le déclenchement de la restauration. Par conséquent, la restauration à partir du niveau d’instantané est également appelée [restauration instantané](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability).
  - Durant la phase 2, l’instantané est transféré et stocké dans le coffre géré par le service Sauvegarde Azure. C’est ce que l’on appelle **niveau coffre**.

- **Récupération à l’emplacement d’origine (OLR)**  : récupération opérée à partir du point de restauration vers la machine virtuelle Azure source à partir de laquelle les sauvegardes ont été effectuées, en la remplaçant par l’état stocké dans le point de récupération. L’opération remplace le disque du système d’exploitation et les disques de données de la machine virtuelle source.

- **Récupération à un autre emplacement (ALR)**  : récupération opérée à partir du point de récupération vers un serveur autre que le serveur d’origine à partir duquel les sauvegardes ont été effectuées.

- **Restauration au niveau élément (ILR)**  : restauration de fichiers ou dossiers individuels à l’intérieur de la machine virtuelle à partir du point de récupération.

- **Disponibilité (types de réplication)**  : le service Sauvegarde Azure offre deux types de réplication pour conserver votre stockage/vos données hautement disponibles :
  - Le [stockage localement redondant (LRS)](../storage/common/storage-redundancy-lrs.md) réplique vos données trois fois (il crée trois copies de vos données) dans une unité d’échelle de stockage d’un centre de données. Toutes les copies des données existent dans la même région. Le stockage LRS est une option à faible coût qui protège vos données contre les défaillances matérielles locales.
  - Le [stockage géoredondant (GRS)](../storage/common/storage-redundancy-grs.md) est l’option de réplication par défaut : c’est l’option recommandée. Le stockage géo-redondant réplique vos données vers une région secondaire, distante de plusieurs centaines de kilomètres de l’emplacement principal des données sources. Le stockage GRS est plus onéreux que le stockage LRS, mais il offre une durabilité des données supérieure, même en cas de panne au niveau régional.

- **Restauration entre régions (CRR)**  : parmi les [options de restauration](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options), la restauration entre régions (CRR) vous permet de restaurer des machines virtuelles Azure dans une région secondaire, qui est une [région jumelée Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).

## <a name="restore-scenarios"></a>Scénarios de restauration

![Scénarios de restauration ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **Scénario**                                                 | **Opération effectuée**                                             | **Quand utiliser**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Restaurer pour créer une machine virtuelle](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms) | Restaure l’ensemble de la machine virtuelle à l’emplacement d’origine (si la machine virtuelle source existe toujours) ou à un autre emplacement | <li> Si la machine virtuelle source est perdue ou endommagée, vous pouvez la restaurer dans sa totalité  <li> Vous pouvez créer une copie de la machine virtuelle  <li> Vous pouvez effectuer une simulation de restauration à des fins d’audit ou de conformité  <li> Cette option ne fonctionne pas pour des machines virtuelles Azure créées à partir d’images de la Place de marché (c’est-à-dire qui ne sont pas disponibles parce que la licence a expiré). |
| [Restaurer des disques de la machine virtuelle](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) | Restaure les disques attachés à la machine virtuelle                             |  Tous les disques : cette option crée le modèle et restaure le disque. Vous pouvez modifier ce modèle avec des configurations spéciales (par exemple, des groupes à haute disponibilité) pour répondre à vos besoins, puis utiliser le modèle et restaurer le disque afin de recréer la machine virtuelle. |
| [Restaurer des fichiers spécifiques à l’intérieur de la machine virtuelle](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) | Choisissez un point de restauration, parcourez, sélectionnez des fichiers, puis restaurez-les sur le même système d’exploitation (ou un système compatible) que la machine virtuelle sauvegardée. |  Si vous connaissez les fichiers spécifiques à restaurer, utilisez cette option au lieu de restaurer la machine virtuelle entière. |
| [Restaurer une machine virtuelle chiffrée](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) | À partir du portail, restaurez les disques, puis utilisez PowerShell pour créer la machine virtuelle | <li> [Machine virtuelle chiffrée avec Azure Active Directory (AAD)](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows-aad)  <li> [Machine virtuelle chiffrée sans AAD](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-windows) <li> [Machine virtuelle chiffrée *avec AAD* migrée *sans AAD*](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-faq#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [Restauration entre régions](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) | Crée une machine virtuelle ou restaure des disques dans une région secondaire (région jumelé Azure) | <li> **Interruption complète** :  avec la fonctionnalité de restauration entre régions, il n’y a pas de temps d’attente pour récupérer des données dans la région secondaire. Vous pouvez lancer des restaurations dans la région secondaire, même avant qu’Azure déclare une interruption. <li> **Interruption partielle** : un temps d’arrêt peut survenir dans des clusters de stockage spécifiques où le service Sauvegarde Azure stocke vos données sauvegardées, voire dans le réseau, en connectant Sauvegarde Azure et les clusters de stockage associés à vos données sauvegardées. Avec une restauration entre régions, vous pouvez effectuer une restauration dans la région secondaire à l’aide d’un réplica des données sauvegardées dans la région secondaire. <li> **Aucune interruption** : vous pouvez effectuer des simulations continuité d’activité et reprise d’activité (BCDR) à des fins d’audit ou de conformité avec les données de la région secondaire. Cela vous permet d’effectuer une restauration des données sauvegardées dans la région secondaire, même en cas d’interruption complète ou partielle dans la région primaire à des fins de continuité d’activité et reprise d’activité.  |

------





## <a name="next-steps"></a>Étapes suivantes

- [Forum aux questions sur la restauration de machine virtuelle](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#restore)
- [Méthodes de restauration prises en charge](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#supported-restore-methods)
- [Résoudre les problèmes de restauration](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#restore)
