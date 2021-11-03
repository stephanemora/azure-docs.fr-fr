---
title: Vue d’ensemble des pools de disques Azure (préversion)
description: En savoir plus sur les pools de disques Azure (préversion).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: d6e2eda8fd7bc2ba3b41b911b5964c2a65e33c14
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074539"
---
# <a name="azure-disk-pools-preview"></a>Pools de disques Azure (préversion)

Un pool de disques Azure (préversion) est une ressource Azure qui permet à vos applications et charges de travail d’accéder à un groupe de disques managés depuis un seul point de terminaison. Un pool de disques peut exposer une cible iSCSI (Internet Small Computer Systems Interface) pour permettre l’accès aux disques à l’intérieur de ce pool sur l’interface SCSI. Chaque pool de disques peut avoir une cible iSCSI et chaque disque peut être exposé en tant que LUN iSCSI. Vous pouvez connecter des disques sous le pool de disques à des hôtes Azure VMware Solution en tant que magasins de données. Cela vous permet de mettre à l’échelle votre stockage indépendamment de vos hôtes Azure VMware Solution. Une fois qu’un magasin de données est configuré, vous pouvez y créer des volumes et les attacher à vos instances VMware.

## <a name="how-it-works"></a>Fonctionnement

Lorsqu’un pool de disque est déployé, un groupe de ressources managées est automatiquement créé pour vous. Ce groupe de ressources managées contient toutes les ressources Azure nécessaires au fonctionnement d’un pool de disque. La convention d’affectation de noms pour ces groupes de ressources est la suivante : MSP_(resource-group-name)_(diskpool-name)\_(region-name).

Lorsque vous ajoutez un disque managé au pool de disques, le disque est attaché aux contrôleurs iSCSI managés. Plusieurs disques managés peuvent être ajoutés en tant que cibles de stockage à un pool de disques. Chaque cible de stockage est présentée comme un LUN iSCSI sous la cible iSCSI du pool de disques. Les pools de disques offrent une prise en charge native de Azure VMware Solution. Un cluster Azure VMware Solution peut se connecter à un pool de disques, ce qui englobe tous les ordinateurs hôtes Azure VMware Solution dans cet environnement. Le schéma suivant montre comment utiliser les pools de disques avec Azure VMware Solution.

:::image type="content" source="media/disks-pools/disk-pool-diagram.png" alt-text="Schéma illustrant le fonctionnement des pools de disques, chaque disque Ultra est accessible par chaque contrôleur iSCSI sur iSCSI et les hôtes Azure VMware Solution peuvent accéder au contrôleur iSCSI sur iSCSI.":::

## <a name="restrictions"></a>Restrictions

Dans la préversion, les pools de disques présentent les restrictions suivantes :

- Seuls des disques SSD Premium et Standard ou des disques Ultra peuvent être ajoutés à un pool de disques.
    - Vous ne pouvez pas configurer un pool de disques contenant à la fois des disques Ultra et des disques SSD Premium/Standard. Si un pool de disques est configuré pour utiliser des disques Ultra, il ne peut contenir que des disques Ultra. De même, un pool de disques configuré pour utiliser des disques SSD Premium et Standard ne peut contenir que des disques SSD Premium et Standard.
- Les disques qui utilisent le [stockage redondant interzone (ZRS)](disks-redundancy.md#zone-redundant-storage-for-managed-disks) ne sont pas pris en charge pour l’instant. 

### <a name="regional-availability"></a>Disponibilité régionale

Les pools de disques sont actuellement disponibles dans les régions suivantes :

- Australie Est
- Centre du Canada
- USA Centre
- USA Est
- USA Ouest 2
- Japon Est
- Europe Nord
- Europe Ouest
- Asie Sud-Est
- Sud du Royaume-Uni


## <a name="billing"></a>Facturation

Lorsque vous déployez un pool de disques, il existe deux zones principales qui entraînent des coûts de facturation :

- Les disques ajoutés au pool de disques
- Les ressources Azure déployées dans le groupe de ressources managées qui accompagnent le pool de disques. Ces ressources sont :
    - Machines virtuelles
    - Disques managés.
    - Une interface réseau.
    - Un compte de stockage pour les journaux de diagnostic.
        
Vous serez facturé pour les ressources à l’intérieur de ce groupe de ressources managées et les disques individuels qui constituent le stockage de données réel. Par exemple, si vous avez un pool de disques avec un disque P30 ajouté, vous êtes facturé pour le disque P30 et toutes les ressources déployées dans le groupe de ressources managées. Outre ces ressources et vos disques, aucun service supplémentaire n’est facturé pour un pool de disques. Pour obtenir des informations sur les groupes de ressources managées, reportez-vous à la section [Fonctionnement](#how-it-works).

Essayez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour obtenir les tarifs régionaux des machines virtuelles et des disques et évaluer le coût d’un pool de disques. Les ressources Azure consommées par le pool de disques peuvent être comptabilisées dans Azure Reservations, si vous en avez.


## <a name="next-steps"></a>Étapes suivantes

Voir le [guide de planification des pools de disques](disks-pools-planning.md).
