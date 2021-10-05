---
title: Restaurer une sauvegarde sur un nouveau volume de Azure NetApp Files | Microsoft Docs
description: Décrit comment restaurer une sauvegarde sur un nouveau volume.
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
ms.openlocfilehash: 1a0b2e59f2d11caf1db9985cfba937e2df0f44c7
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095049"
---
# <a name="restore-a-backup-to-a-new-volume"></a>Restaurer une sauvegarde sur un nouveau volume

La restauration d’une sauvegarde crée un nouveau volume avec le même type de protocole. Cet article explique l’opération de restauration. 

## <a name="considerations"></a>Considérations

* Le nouveau volume créé par l’opération de restauration ne peut pas être monté tant que la restauration n’est pas terminée. 

* Vous devez déclencher l’opération de restauration en l’absence de sauvegardes de base. Dans le cas contraire, la restauration peut augmenter la charge sur le compte d’objet BLOB Azure où vos données sont sauvegardées. 

## <a name="steps"></a>Étapes

1. Sélectionner **Volumes**. Accéder aux **Sauvegardes**.

    > [!NOTE]
    > Si un volume est supprimé mais que la stratégie de sauvegarde n’a pas été désactivée avant la suppression du volume, toutes les sauvegardes associées au volume sont conservées dans le Stockage Azure et sont répertoriées sous le compte NetApp associé.  Consultez [Rechercher dans les sauvegardes au niveau du compte NetApp](backup-search.md#search-backups-at-netapp-account-level).


2. Dans la liste des sauvegardes, sélectionnez la sauvegarde à restaurer. Cliquez sur les trois points (`…`) à droite de la sauvegarde, puis cliquez sur **Restaurer vers un nouveau volume** dans le menu Action.   

    ![Capture d’écran montrant l’option de restauration de la sauvegarde sur un nouveau volume.](../media/azure-netapp-files/backup-restore-new-volume.png)

3. Dans la page créer un volume qui s’affiche, fournissez des informations pour les champs de la page selon le cas, puis cliquez sur **Analyser + Créer** pour commencer la restauration de la sauvegarde vers un nouveau volume.   

    * Le champ **Protocole** est pré-rempli à partir du volume d’origine et ne peut pas être modifié.    
        Toutefois, si vous restaurez un volume à partir de la liste de sauvegarde au niveau du compte NetApp, vous devez spécifier le champ protocole. Le champ protocole doit correspondre au protocole du volume d’origine. Dans le cas contraire, l’opération de restauration échouera avec l’erreur suivante :  
        `Protocol Type value mismatch between input and source volume of backupId <backup-id of the selected backup>. Supported protocol type : <Protocol Type of the source volume>`

    * La valeur de **quota** doit être supérieure ou égale à la taille de la sauvegarde à partir de laquelle la restauration est déclenchée (minimum 100 Gio).

    * Le **pool de capacité** dans lequel la sauvegarde est restaurée doit avoir une capacité inutilisée suffisante pour héberger le nouveau volume restauré. Sinon, l’opération échoue.   

    ![Capture instantanée montrant la page Créer un volume.](../media/azure-netapp-files/backup-restore-create-volume.png)

## <a name="next-steps"></a>Étapes suivantes  

* [Présentation de la sauvegarde Azure NetApp Files](backup-introduction.md)
* [Exigences et considérations relatives à la sauvegarde Azure NetApp Files](backup-requirements-considerations.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuration de sauvegardes basées sur une stratégie](backup-configure-policy-based.md)
* [Configuration des sauvegardes manuelles](backup-configure-manual.md)
* [Gestion des stratégies de sauvegarde](backup-manage-policies.md)
* [Recherche dans les sauvegardes](backup-search.md)
* [Désactiver la fonctionnalité de sauvegarde pour un volume](backup-disable.md)
* [Supprimer des sauvegardes d’un volume](backup-delete.md)
* [Métriques de sauvegarde d’un volume](azure-netapp-files-metrics.md#volume-backup-metrics)
* [FAQ sur la sauvegarde Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)



