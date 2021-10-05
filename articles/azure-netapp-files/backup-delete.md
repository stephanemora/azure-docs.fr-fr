---
title: Supprimer les sauvegardes d'un volume Azure NetApp Files | Microsoft Docs
description: Décrit comment supprimer les sauvegardes individuelles que vous n'avez plus besoin de conserver pour un volume.
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
ms.openlocfilehash: c63fc37000fbd6cf376dd5a80b392e87466e9ce3
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095037"
---
# <a name="delete-backups-of-a-volume"></a>Supprimer des sauvegardes d’un volume 

Vous pouvez supprimer les sauvegardes individuelles que vous n'avez plus besoin de conserver pour un volume. La suppression des sauvegardes entraîne la suppression des objets associés dans votre compte Azure Storage, ce qui permet de gagner de l'espace.  

Vous ne pouvez pas supprimer la dernière sauvegarde.  

## <a name="steps"></a>Étapes

1. Sélectionner **Volumes**.
2. Accéder aux **Sauvegardes**.
3. Dans la liste des sauvegardes, sélectionnez la sauvegarde à supprimer. Cliquez sur les trois points (`…`) à droite de la sauvegarde, puis cliquez sur **Supprimer** dans le menu Action.

    ![Capture d'écran qui montre le menu Supprimer pour les sauvegardes.](../media/azure-netapp-files/backup-action-menu-delete.png)

## <a name="next-steps"></a>Étapes suivantes  

* [Présentation de la sauvegarde Azure NetApp Files](backup-introduction.md)
* [Exigences et considérations relatives à la sauvegarde Azure NetApp Files](backup-requirements-considerations.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuration de sauvegardes basées sur une stratégie](backup-configure-policy-based.md)
* [Configuration des sauvegardes manuelles](backup-configure-manual.md)
* [Gestion des stratégies de sauvegarde](backup-manage-policies.md)
* [Recherche dans les sauvegardes](backup-search.md)
* [Restauration d’une sauvegarde sur un nouveau volume](backup-restore-new-volume.md)
* [Désactivation de la fonctionnalité de sauvegarde pour un volume](backup-disable.md)
* [Métriques des sauvegardes de volume](azure-netapp-files-metrics.md#volume-backup-metrics)
* [FAQ sur la sauvegarde Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
