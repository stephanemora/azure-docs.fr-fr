---
title: Désactiver la fonctionnalité de sauvegarde pour un volume Azure NetApp Files | Microsoft Docs
description: Cet article explique comment désactiver la fonctionnalité de sauvegarde pour un volume qui n’a plus besoin d’une protection de sauvegarde.
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
ms.topic: how-to
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: 1555a1c42f6365202787750f149b345ccd0cd55c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224140"
---
# <a name="disable-backup-functionality-for-a-volume"></a>Désactiver la fonctionnalité de sauvegarde pour un volume 

Vous pouvez désactiver la fonctionnalité de sauvegarde pour un volume si vous n’avez plus besoin d’une protection de sauvegarde. 

> [!IMPORTANT]
> La désactivation des sauvegardes pour un volume entraînera la suppression de toutes les sauvegardes stockées dans le stockage Azure pour ce volume.

Si un volume est supprimé mais que la stratégie de sauvegarde n’a pas été désactivée avant la suppression du volume, toutes les sauvegardes associées au volume sont conservées dans le Stockage Azure et sont répertoriées sous le compte NetApp associé. 

## <a name="steps"></a>Étapes

1. Sélectionner **Volumes**.
2. Sélectionnez le volume spécifique dont vous souhaitez désactiver les fonctionnalités de sauvegarde.
3. Sélectionnez **Configurer**.
4. Dans la page Configuration des sauvegardes, faites basculer le paramètre **Activé** sur **Off** (Désactivé). Pour confirmer, entrez le nom du volume, puis cliquez sur **OK**.

    ![Capture d’écran qui montre la fenêtre Configurer les sauvegardes avec la sauvegarde désactivée.](../media/azure-netapp-files/backup-configure-backups-disable.png)

## <a name="next-steps"></a>Étapes suivantes  

* [Présentation de la sauvegarde Azure NetApp Files](backup-introduction.md)
* [Exigences et considérations relatives à la sauvegarde Azure NetApp Files](backup-requirements-considerations.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuration de sauvegardes basées sur une stratégie](backup-configure-policy-based.md)
* [Configuration des sauvegardes manuelles](backup-configure-manual.md)
* [Gestion des stratégies de sauvegarde](backup-manage-policies.md)
* [Recherche dans les sauvegardes](backup-search.md)
* [Restaurer une sauvegarde sur un nouveau volume](backup-restore-new-volume.md)
* [Supprimer des sauvegardes d’un volume](backup-delete.md)
* [Métriques de sauvegarde d’un volume](azure-netapp-files-metrics.md#volume-backup-metrics)
* [FAQ sur la sauvegarde Azure NetApp Files](faq-backup.md)

