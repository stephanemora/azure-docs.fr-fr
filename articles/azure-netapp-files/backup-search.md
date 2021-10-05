---
title: Rechercher des sauvegardes de volumes Azure NetApp Files | Microsoft Docs
description: Décrit comment afficher et rechercher des sauvegardes de volumes Azure NetApp Files au niveau du volume et du compte NetApp.
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
ms.openlocfilehash: 71fcf944f0664c1d3e4d07e96348d109042047a8
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095045"
---
# <a name="search-backups-of-azure-netapp-files-volumes"></a>Rechercher des sauvegardes de volumes Azure NetApp Files

Vous pouvez afficher des sauvegardes et rechercher une sauvegarde particulière au niveau du volume ou du compte NetApp.

Les noms utilisés pour les captures instantanées sont conservés lors de la sauvegarde des captures instantanées. Les noms d’instantanés incluent le préfixe `daily` , `weekly` ou `monthly` . Elles incluent également l’heure lors de la création de l’instantané. Le premier instantané pris lorsque la fonctionnalité de sauvegarde est activée est appelé *instantané de base*, et son nom inclut le préfixe `snapmirror`.

Si un volume est supprimé, ses sauvegardes sont toujours conservées. Les sauvegardes sont listées dans les comptes NetApp associés, sous la section sauvegardes. Cette liste inclut toutes les sauvegardes au sein de l’abonnement (dans les comptes NetApp) de la région. Il peut être utilisé pour restaurer une sauvegarde sur un volume d’un autre compte NetApp sous le même abonnement.

## <a name="search-backups-at-volume-level"></a>Rechercher dans les sauvegardes au niveau du volume    

Vous pouvez afficher et rechercher des sauvegardes au niveau du volume :

1. Sélectionner **Volumes**.

2. Accédez à **Sauvegardes** pour afficher les sauvegardes du volume.   
    La colonne **Type** indique si la sauvegarde est générée par une sauvegarde *Planifiée* (basée sur une stratégie) ou une sauvegarde *Manuelle*.

3. Dans la zone **Rechercher sauvegardes**, entrez le nom de la sauvegarde que vous souhaitez rechercher.  

    Une recherche partielle est prise en charge ; vous n’êtes pas obligé de spécifier le nom complet de la sauvegarde. La recherche filtre les sauvegardes en fonction de la chaîne de recherche.

    ![Capture d’écran montrant une liste de sauvegardes pour un volume.](../media/azure-netapp-files/backup-search-volume-level.png)

## <a name="search-backups-at-netapp-account-level"></a>Rechercher dans les sauvegardes au niveau du compte NetApp 

Vous pouvez afficher et rechercher des sauvegardes associées à un abonnement sous tous les comptes NetApp dans une région d’un abonnement.   

1. Sélectionner le **compte NetApp**.

2. Accéder aux **Sauvegardes**.
    La colonne **Type** indique si la sauvegarde est générée par une sauvegarde *Planifiée* (basée sur une stratégie) ou une sauvegarde *Manuelle*. 

3. Dans la zone **Rechercher sauvegardes**, entrez le nom de la sauvegarde que vous souhaitez rechercher.

    * La liste de sauvegarde comprend toutes les sauvegardes de tous les volumes (actifs et supprimés) qui appartiennent à tous les comptes NetApp d’un abonnement.

    * La liste de sauvegarde peut être utilisée pour restaurer une sauvegarde sur un nouveau volume et pour supprimer une sauvegarde. Le flux de travail pour la restauration d’une sauvegarde est le même que pour la restauration de la [sauvegarde au niveau du volume](backup-restore-new-volume.md). Le flux de travail pour la suppression d’une sauvegarde est le même que pour la [suppression de la sauvegarde au niveau du volume](backup-delete.md).

    * La meilleure pratique recommandée pour la Convention d’affectation de noms de [sauvegarde manuelle](backup-configure-manual.md) vous aide à identifier le volume auquel appartient la sauvegarde. Cette pratique est particulièrement utile dans le cas où la sauvegarde appartient à un volume qui a été supprimé (avec des sauvegardes conservées).

    ![Capture d’écran montrant une liste de sauvegardes pour un compte NetApp.](../media/azure-netapp-files/backup-search-account-level.png)

## <a name="next-steps"></a>Étapes suivantes  

* [Présentation de la sauvegarde Azure NetApp Files](backup-introduction.md)
* [Exigences et considérations relatives à la sauvegarde Azure NetApp Files](backup-requirements-considerations.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuration de sauvegardes basées sur une stratégie](backup-configure-policy-based.md)
* [Configuration des sauvegardes manuelles](backup-configure-manual.md)
* [Gestion des stratégies de sauvegarde](backup-manage-policies.md)
* [Restauration d’une sauvegarde sur un nouveau volume](backup-restore-new-volume.md)
* [Désactivation de la fonctionnalité de sauvegarde pour un volume](backup-disable.md)
* [Supprimer des sauvegardes d’un volume](backup-delete.md)
* [Métriques des sauvegardes de volume](azure-netapp-files-metrics.md#volume-backup-metrics)
* [FAQ sur la sauvegarde Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
