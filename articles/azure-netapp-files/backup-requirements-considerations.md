---
title: Exigences et considérations pour la sauvegarde des fichiers Azure NetApp | Microsoft Docs
description: Décrit les exigences et les considérations dont vous devez être conscient avant d'utiliser la sauvegarde Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: b-juche
ms.openlocfilehash: f396904b3832403337a6fa4553e2ae4a4b8c86b4
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667950"
---
# <a name="requirements-and-considerations-for-azure-netapp-files-backup"></a>Exigences et considérations relatives à la sauvegarde Azure NetApp Files 

Cet article décrit les exigences et les considérations dont vous devez être conscient avant d'utiliser la sauvegarde Azure NetApp Files.

## <a name="requirements-and-considerations"></a>Conditions requises et éléments à prendre en compte

Vous devez être conscient de plusieurs exigences et considérations avant d'utiliser la sauvegarde des fichiers Azure NetApp : 

* La sauvegarde Azure NetApp Files est disponible dans les régions associées à votre abonnement Azure NetApp Files. La sauvegarde d'Azure NetApp Files dans une région ne peut protéger qu'un volume Azure NetApp Files situé dans cette même région. Par exemple, les sauvegardes créées par le service dans l'Ouest américain 2 pour un volume situé dans l'Ouest américain 2 sont envoyées au stockage Azure qui est également situé dans l'Ouest américain 2. Azure NetApp Files ne prend pas en charge les sauvegardes ou la réplication des sauvegardes vers une autre région.  

* L'affichage d'une sauvegarde peut prendre jusqu'à 5 minutes après la fin de la sauvegarde.

* Actuellement, la fonction de sauvegarde Azure NetApp Files prend en charge la sauvegarde des instantanés locaux quotidiens, hebdomadaires et mensuels créés par la politique d'instantanés associée vers le stockage Azure. Les sauvegardes horaires ne sont pas prises en charge actuellement.

* La sauvegarde Azure NetApp Files utilise le compte de [stockage redondant](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) (ZRS) qui réplique les données de manière synchrone sur trois zones de disponibilité Azure de la région, à l'exception des régions répertoriées ci-dessous où seul le [stockage redondant local](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) (LRS) est pris en charge :   

    * USA Ouest   

    LRS peut récupérer les pannes de serveurs et de lecteurs. Toutefois, si un sinistre tel qu'un incendie ou une inondation se produit dans le centre de données, toutes les répliques d'un compte de stockage utilisant LRS peuvent être perdues ou irrécupérables. 

* L’utilisation de la sauvegarde Azure NetApp Files basée sur une stratégie (planifiée) nécessite la configuration et l’activation de la stratégie d’instantané. Consultez [Gérer les instantanés avec Azure NetApp Files](azure-netapp-files-manage-snapshots.md).   
    Le volume qui doit être sauvegardé nécessite une politique de création d'instantanés configurée. Le nombre configuré de sauvegardes est stocké dans le stockage Azure. 

* Si un problème survient (par exemple, il ne reste pas suffisamment d'espace sur le volume) et que la politique d'instantanés arrête de créer de nouveaux instantanés, la fonction de sauvegarde n'aura pas de nouveaux instantanés à sauvegarder. 

* Dans un paramètre de réplication entre les régions, la sauvegarde Azure NetApp Files ne peut être configurée que sur un volume source. Elle n’est pas prise en charge sur un volume de *destination* de réplication entre régions.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de la sauvegarde Azure NetApp Files](backup-introduction.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuration de sauvegardes basées sur une stratégie](backup-configure-policy-based.md)
* [Configuration des sauvegardes manuelles](backup-configure-manual.md)
* [Gestion des stratégies de sauvegarde](backup-manage-policies.md)
* [Recherche dans les sauvegardes](backup-search.md)
* [Restaurer une sauvegarde sur un nouveau volume](backup-restore-new-volume.md)
* [Désactivation de la fonctionnalité de sauvegarde pour un volume](backup-disable.md)
* [Supprimer des sauvegardes d’un volume](backup-delete.md)
* [Métriques de sauvegarde d’un volume](azure-netapp-files-metrics.md#volume-backup-metrics)
* [FAQ sur la sauvegarde Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
* [Comment fonctionnent les instantanés Azure NetApp Files](snapshots-introduction.md)
