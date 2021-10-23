---
title: Présentation de la sauvegarde Azure NetApp Files | Microsoft Docs
description: Décrit ce que fait la sauvegarde Azure NetApp Files, les régions prises en charge et le modèle de coût.
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
ms.date: 10/13/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: 81d14b6c03e8ef27697e8f069a5d9ebd5ff38d7f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993128"
---
# <a name="understand-azure-netapp-files-backup"></a>Présentation de la sauvegarde Azure NetApp Files

La sauvegarde Azure NetApp Files étend les fonctionnalités de protection des données d’Azure NetApp Files en fournissant une solution de sauvegarde entièrement managée pour la récupération, l’archivage et la conformité à long terme. Les sauvegardes créées par le service sont stockées dans le service Stockage Azure, indépendamment des captures instantanées de volume disponibles pour la récupération ou le clonage à très court terme. Il est possible de restaurer les sauvegardes effectuées par le service sur de nouveaux volumes Azure NetApp Files dans la région. La sauvegarde Azure NetApp Files prend en charge des sauvegardes basées sur des stratégies (planifiées) et des sauvegardes manuelles (à la demande). Pour plus d’informations, consultez [Fonctionnement des instantanés Azure NetApp Files](snapshots-introduction.md).

> [!IMPORTANT]
> La fonctionnalité de sauvegarde d’Azure NetApp Files est actuellement en préversion. Vous devez envoyer une demande de liste d’attente pour accéder à la fonctionnalité via la page **[Préversion publique de la sauvegarde Azure NetApp Files](https://aka.ms/anfbackuppreviewsignup)** . Attendez de recevoir un e-mail de confirmation officiel de l’équipe Azure NetApp Files avant d’utiliser la fonctionnalité de sauvegarde Azure NetApp Files.

## <a name="supported-regions"></a>Régions prises en charge 

La sauvegarde Azure NetApp Files est prise en charge pour les régions suivantes :   

* USA Est
* USA Est 2
* USA Ouest 
* Centre sud
* USA Ouest 2
* Europe Nord 
* Europe Ouest
* Australie Est
* Japon Est

## <a name="cost-model-for-azure-netapp-files-backup"></a>Modèle de coût pour la sauvegarde Azure NetApp Files

La tarification de la sauvegarde Azure NetApp Files est basée sur la quantité totale de stockage consommée par la sauvegarde. Il n’y a pas de frais de configuration ou d’utilisation minimale. La restauration de la sauvegarde est tarifée en fonction de la quantité totale de capacité de sauvegarde restaurée pendant le cycle de facturation.

Pour illustrer la tarification, considérez les situations suivantes :

* Votre volume source provient du niveau de service Azure NetApp Files Premium. Il a une taille de quota de volume de 1000 Gio et une taille de volume consommé de 500 Gio au début du premier jour du mois. Le volume se trouve dans la région USA Centre Sud.
* Vous avez configuré une stratégie d’*instantané* quotidien avec 5 instantanés locaux à conserver, et une stratégie de *sauvegarde* quotidienne pour conserver 30 copies de sauvegarde.
* Pour simplifier, supposons que votre volume source a un changement de données constant de 1 % chaque jour, mais que la taille du volume total consommé n’augmente pas (elle reste stabilisée à 500 Gio).

Quand la stratégie de sauvegarde est affectée au volume, la sauvegarde de base vers le stockage Azure géré par le service est lancée. Une fois la sauvegarde terminée, la sauvegarde de base de 500 Gio est ajoutée à la liste de sauvegarde du volume. Après le transfert de base, les sauvegardes quotidiennes n’ont trait qu’aux blocs modifiés. Supposons que des sauvegardes incrémentielles quotidiennes de 5 Gio sont ajoutées. Le stockage de sauvegarde total consommé est alors de `500GiB + 30*5GiB = 650GiB`.

Vous êtes facturé à la fin du mois pour la sauvegarde au tarif de 0,05 USD par mois pour la quantité totale de stockage consommée par la sauvegarde.  Autrement dit, 650 Gio avec un coût de sauvegarde mensuel total de `650*$0.05=$32.5`. La capacité de stockage standard d’Azure NetApp Files s’applique aux instantanés locaux. Pour plus d’informations, consultez la page [Tarification Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/).

Si vous choisissez de restaurer une sauvegarde de, par exemple, 600 Gio sur un nouveau volume, vous êtes facturé au taux 0,02 USD par Gio de restaurations de capacité de sauvegarde. Dans ce cas, il s’agit de `600*$0.02 = $12` pour l’opération de restauration. 

## <a name="next-steps"></a>Étapes suivantes

* [Exigences et considérations relatives à Azure NetApp Files](backup-requirements-considerations.md)
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
