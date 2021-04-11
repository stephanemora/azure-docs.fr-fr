---
title: Options de disponibilité pour machines virtuelles Azure
description: Familiarisez-vous avec les options de disponibilité permettant d'exécuter des machines virtuelles dans Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 821c50b5cb48bddb586c3589326a7f31c7bafded
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604530"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Options de disponibilité pour machines virtuelles Azure
Cet article fournit une vue d'ensemble des options de disponibilité pour machines virtuelles Azure.

## <a name="availability-zones"></a>Zones de disponibilité

Les [zones de disponibilité](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context) élargissent le niveau de contrôle dont vous disposez pour maintenir la disponibilité des applications et des données sur vos machines virtuelles. Une zone de disponibilité est une zone physiquement séparée au sein d’une région Azure. On distingue trois zones de disponibilité par région Azure prise en charge. 

Chaque zone de disponibilité possède une source d’alimentation, un réseau et un système de refroidissement propres. En concevant vos solutions de manière à utiliser des machines virtuelles répliquées dans des zones, vous pouvez protéger vos applications et vos données de la perte d'un centre de données. Si une zone est compromise, les applications et données répliquées sont instantanément rendues disponibles dans une autre zone. 

## <a name="availability-sets"></a>Groupes à haute disponibilité
Un [groupe à haute disponibilité](availability-set-overview.md) est un regroupement logique de machines virtuelles qui permet à Azure de comprendre comment votre application est conçue, afin de garantir la redondance et la disponibilité. Il est recommandé de créer au moins deux machines virtuelles dans un groupe à haute disponibilité, de manière à fournir une application hautement disponible et à répondre aux exigences du [niveau de 99,95 % inscrit dans les contrats de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Le groupe à haute disponibilité proprement dit ne vous coûte rien ; vous payez uniquement pour chaque instance de machine virtuelle que vous créez.


## <a name="virtual-machines-scale-sets"></a>Groupes de machines virtuelles identiques 

Les [groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context) vous permettent de créer et de gérer un groupe de machines virtuelles à charge équilibrée. Le nombre d’instances de machine virtuelle peut augmenter ou diminuer automatiquement en fonction d’une demande ou d’un calendrier défini. Les groupes identiques offrent une haute disponibilité à vos applications, et vous permettent de gérer, configurer et mettre à jour de manière centralisée de nombreuses machines virtuelles. Il est recommandé de créer au moins deux machines virtuelles dans un groupe identique, de manière à fournir une application hautement disponible et à répondre aux exigences du [niveau de 99,95 % inscrit dans les contrats de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Le groupe identique proprement dit ne vous coûte rien ; vous payez uniquement pour chaque instance de machine virtuelle que vous créez.

Les machines virtuelles d’un groupe identique peuvent également être déployées dans une seule zone de disponibilité, ou dans une région. Les options de déploiement de la zone de disponibilité peuvent varier en fonction du [mode d'orchestration](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context).

## <a name="load-balancer"></a>Équilibrage de charge
Combinez [Azure Load Balancer](../load-balancer/load-balancer-overview.md) avec une zone de disponibilité ou un groupe à haute disponibilité pour une meilleure résilience de votre application. L'équilibrage de charge Azure répartit le trafic entre plusieurs machines virtuelles. L'équilibrage de charge Azure est compris pour nos machine virtuelles de niveau Standard. Certains niveaux de machines virtuelles n’intègrent pas Azure Load Balancer. Pour plus d’informations sur l’équilibrage de charge des machines virtuelles, consultez **Équilibrage de charge des machines virtuelles** pour [Linux](linux/tutorial-load-balancer.md) ou [Windows](windows/tutorial-load-balancer.md).


## <a name="azure-storage-redundancy"></a>Redondance de Stockage Azure
Le service Stockage Azure stocke toujours plusieurs copies de vos données afin qu’elles soient protégées contre des événements planifiés ou non, notamment des défaillances matérielles temporaires, des pannes de réseau ou de courant et des catastrophes naturelles majeures. La redondance garantit que votre compte de stockage répond à ses objectifs de disponibilité et de durabilité, même en cas de défaillance.

Lorsque vous choisissez l’option de redondance la mieux adaptée à votre scénario, réfléchissez aux compromis possibles entre, d’une part, des coûts réduits et, de l’autre, une disponibilité accrue. Les facteurs déterminant le choix de l’option de redondance sont les suivants :
- Mode de réplication de vos données dans la région primaire
- Réplication éventuelle de vos données vers une deuxième région géographiquement éloignée de la région primaire, afin d’offrir une protection contre des catastrophes régionales
- Nécessité ou non pour l’application d’avoir accès en lecture aux données répliquées dans la région secondaire si la région primaire devient indisponible pour une raison quelconque

Pour plus d'informations, consultez [Redondance de Stockage Azure](../storage/common/storage-redundancy.md).

## <a name="azure-site-recovery"></a>Azure Site Recovery
En tant qu’organisation, vous devez adopter une stratégie de continuité d’activité et de reprise d’activité (BCDR) capable de garantir la sécurité de vos données et de maintenir vos applications et vos charges de travail en ligne, lorsque des interruptions planifiées et non planifiées se produisent.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) permet d'assurer la continuité de l'activité en maintenant l'exécution des charges de travail et des applications métier lors des interruptions. Site Recovery réplique les charges de travail s’exécutant sur des machines virtuelles et physiques depuis un site principal vers un emplacement secondaire. En cas d’interruption au niveau de votre site principal, vous basculez vers l’emplacement secondaire, depuis lequel vous pouvez accéder aux applications. Quand l’emplacement principal est de nouveau fonctionnel, vous pouvez effectuer une restauration automatique vers celui-ci.

Site Recovery peut gérer la réplication pour :
- Les machines virtuelles Azure qui répliquent des données entre des régions Azure.
- Machines virtuelles locales, machines virtuelles Azure Stack et serveurs physiques.

## <a name="next-steps"></a>Étapes suivantes
- [Créer une machine virtuelle dans une zone de disponibilité](/azure/virtual-machines/linux/create-cli-availability-zone)
- [Créer une machine virtuelle dans un groupe à haute disponibilité](/azure/virtual-machines/linux/tutorial-availability-sets)
- [Créer un groupe de machines virtuelles identiques](../virtual-machine-scale-sets/quick-create-portal.md)