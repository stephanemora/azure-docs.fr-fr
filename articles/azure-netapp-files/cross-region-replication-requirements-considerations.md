---
title: Configuration requise et considérations relatives de la réplication inter-région d’Azure NetApp Files | Microsoft Docs
description: Décrit la configuration requise et les considérations relatives à l’utilisation de la fonctionnalité de réplication inter-région de volume d’Azure NetApp Files.
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
ms.date: 10/14/2021
ms.author: b-juche
ms.openlocfilehash: b098243c3f4cf160aadfb2fa001c23c4cad691bd
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042386"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Configuration requise et considérations pour la réplication inter-région 

Cet article décrit les exigences et les considérations suivantes relatives à l’[utilisation de la fonctionnalité de réplication inter-région](cross-region-replication-create-peering.md) d’Azure NetApp Files.

## <a name="requirements-and-considerations"></a>Conditions requises et éléments à prendre en compte 

* La réplication Azure NetApp Files n’est disponible que dans certaines paires de régions fixes. Voir [Paires de régions prises en charge](cross-region-replication-introduction.md#supported-region-pairs). 
* Les volumes SMB et les volumes NFS sont pris en charge. La réplication des volumes SMB nécessite une connexion Active Directory dans les comptes NetApp source et de destination. La connexion Active Directory de destination doit avoir accès aux serveurs DNS ou aux contrôleurs de domaine ADDS qui sont accessibles à partir du sous-réseau délégué dans la région de destination. Pour plus d’informations, consultez [Configuration requise pour les connexions Active Directory](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* Le compte de destination doit se trouver dans une région différente du volume source. Vous pouvez également sélectionner un compte NetApp existant dans une autre région.  
* Le volume de destination de réplication est en lecture seule jusqu’à ce que vous [basculiez vers la région de destination](cross-region-replication-manage-disaster-recovery.md#fail-over-to-destination-volume) pour activer le volume de destination en lecture et en écriture. 
* La réplication Azure NetApp Files ne prend actuellement pas en charge plusieurs abonnements. Toutes les réplications doivent être effectuées sous un même abonnement.
* Consultez les [limites de ressources](azure-netapp-files-resource-limits.md) pour connaître le nombre maximal de volumes de destination de réplication entre les régions. Vous pouvez ouvrir un ticket de support pour [demander une augmentation du quota](azure-netapp-files-resource-limits.md#request-limit-increase) par défaut des volumes de destination de réplication (par abonnement dans une région).
* Il peut y avoir un délai maximal de cinq minutes avant que l’interface reflète un nouvel instantané ajouté sur le volume source.  
* Les topologies en cascade et de type fan-in/out ne sont pas prises en charge.
* La configuration de la réplication de volume pour les volumes sources créés à partir d’un instantané n’est pas prise en charge pour l’instant.
* Une fois que vous avez configuré la réplication inter-région, le processus de réplication crée des *instantanés SnapMirror* pour fournir des références entre le volume source et le volume de destination. Les instantanés SnapMirror sont parcourus automatiquement lorsqu’un nouveau est créé pour chaque transfert incrémentiel. Vous ne pouvez pas supprimer les instantanés SnapMirror tant que la relation de réplication et le volume n’ont pas été supprimés. 
* Vous pouvez supprimer des instantanés manuels sur le volume source d’une relation de réplication lorsque celle-ci est active ou interrompue, et également après la suppression de la relation de réplication. Vous ne pouvez pas supprimer des instantanés manuels du volume de destination tant que la relation de réplication n’est pas rompue.
* Vous ne pouvez pas rétablir un volume source ou un volume de destination de la réplication interrégionale vers un instantané. La fonctionnalité de restauration d’instantané est grisée pour les volumes qui sont dans une relation de réplication. 

## <a name="next-steps"></a>Étapes suivantes
* [Créer une réplication de volume](cross-region-replication-create-peering.md)
* [Afficher l’état d’intégrité de la relation de réplication](cross-region-replication-display-health-status.md)
* [Gérer la reprise d’activité après sinistre](cross-region-replication-manage-disaster-recovery.md)
* [Métriques de réplication de volume](azure-netapp-files-metrics.md#replication)
* [Supprimer des volumes ou des réplications de volume](cross-region-replication-delete.md)
* [Résoudre les problèmes de réplication inter-région](troubleshoot-cross-region-replication.md)


