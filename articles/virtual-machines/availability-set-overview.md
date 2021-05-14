---
title: Vue d’ensemble des groupes à haute disponibilité
description: Découvrir les groupes à haute disponibilité dans Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: b4e9f106354915fe40a4bcf9b60bc35443345202
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530573"
---
# <a name="availability-sets-overview"></a>Vue d’ensemble des groupes à haute disponibilité

Cet article fournit une vue d’ensemble des fonctionnalités de disponibilité des machines virtuelles Azure.

## <a name="what-is-an-availability-set"></a>Qu’est-ce qu’un groupe à haute disponibilité ? 

Un groupe à haute disponibilité est un regroupement logique de machines virtuelles qui permet à Azure de comprendre comment votre application est conçue, afin de garantir la redondance et la disponibilité. Il est recommandé de créer au moins deux machines virtuelles dans un groupe à haute disponibilité, de manière à fournir une application hautement disponible et à répondre aux exigences du [niveau de 99,95 % inscrit dans les contrats de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Le groupe à haute disponibilité proprement dit ne vous coûte rien ; vous payez uniquement pour chaque instance de machine virtuelle que vous créez.

## <a name="how-do-availability-sets-work"></a>Comment fonctionnent les groupes à haute disponibilité ?
Chaque machine virtuelle de votre groupe à haute disponibilité se voit attribuer un **domaine de mise à jour** et un **domaine d’erreur** par la plateforme Azure sous-jacente. Chaque groupe à haute disponibilité peut être configuré avec un maximum de trois domaines d’erreur et vingt domaines de mise à jour. Les domaines de mise à jour indiquent les groupes de machines virtuelles et les équipements physiques sous-jacents pouvant être redémarrés en même temps. Dans le cas où un seul groupe à haute disponibilité comprend plus de cinq machines virtuelles, la sixième machine est placée dans le même domaine de mise à jour que la première, la septième dans le même que la deuxième, etc. Le redémarrage des domaines de mise à jour peut ne pas suivre un ordre séquentiel au cours de la maintenance planifiée, mais un seul domaine de mise à jour peut être redémarré à la fois. Un domaine de mise à jour redémarré bénéficie de 30 minutes pour récupérer avant que la maintenance ne soit lancée sur un autre domaine de mise à jour.

Les domaines d’erreur définissent le groupe de machines virtuelles partageant une source d’alimentation et un commutateur réseau communs. Par défaut, les machines virtuelles configurées dans votre groupe à haute disponibilité sont réparties entre trois domaines de défaillance au maximum. Le fait de placer vos machines virtuelles dans un groupe à haute disponibilité ne protège pas vos applications des défaillances du système d’exploitation ou propres aux applications, mais limite l’effet des défaillances des équipements physiques, des pannes du serveur et des coupures d’électricité.

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="Diagramme montrant différents clusters de calcul répartis en domaines d’erreur ; dans ces domaines d’erreur, nous avons plusieurs domaines de mise à jour":::

Les machines virtuelles sont également alignées avec les domaines d’erreur de disque. Cet alignement garantit que tous les disques managés attachés à une machine virtuelle se trouvent dans les mêmes domaines d’erreur. 

Seules des machines virtuelles avec des disques managés peuvent être créées dans un groupe à haute disponibilité géré. Le nombre de domaines d’erreur de disques gérés varie en fonction de la région (deux ou trois par région). 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="Diagramme montrant comment les domaines d’erreur pour les disques et les machines virtuelles sont alignés.":::

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant commencer à utiliser ces fonctionnalités de gestion de la redondance et de la disponibilité pour créer votre environnement Azure. Pour en savoir plus sur les meilleures pratiques, consultez la rubrique relative aux [meilleures pratiques Azure en matière de disponibilité](/azure/architecture/checklist/resiliency-per-service).

