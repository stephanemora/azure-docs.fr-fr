---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/17/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8f485ef5d49ff2f0b9bfec4c6abc6db1b78c3826
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288335"
---
Les seules options de redondance d’infrastructure disponibles pour les disques Ultra sont les zones de disponibilité. Les machines virtuelles utilisant d’autres options de redondance ne peuvent pas attacher un disque Ultra.

Le tableau suivant présente les régions où les disques Ultra sont disponibles, ainsi que les options de disponibilité correspondantes.

> [!NOTE]
> Si l’une des régions de la liste suivante ne dispose pas de zones de disponibilité compatibles avec les disques Ultra, les machines virtuelles de cette région doivent être déployées sans redondance d’infrastructure pour pouvoir attacher un disque Ultra.

| Options de redondance | Régions |
|--------------------|---------|
| **Machines virtuelles individuelles** | Centre de l’Australie<br/>Brésil Sud<br/>Inde centrale<br/>Asie Est<br/>Allemagne Centre-Ouest<br/>Centre de la Corée<br/>USA Centre Nord, USA Centre Sud, USA Ouest<br/>US Gov Arizona, US Gov Virginie, US Gov Texas |
| **Deux zones de disponibilité** | France Centre |
| **Trois zones de disponibilité** | Australie Est<br/>Centre du Canada<br/>Europe Nord, Europe Ouest<br/>Japon Est<br/>Asie Sud-Est<br/>Sud du Royaume-Uni<br/>USA Centre, USA Est, USA Est 2, USA Ouest 2 |

Toutes les tailles de machines virtuelles ne sont pas disponibles dans toutes les régions prises en charge avec des disques Ultra. Le tableau suivant répertorie les séries de machines virtuelles compatibles avec les disques Ultra.

|Type de machine virtuelle     |Tailles    |Description  |
|------------|---------|-------------|
| Usage général|[Série DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series), [Série Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series), [Série Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series), [Série Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)| Ratio processeur/mémoire équilibré. Idéal pour le test et le développement, les bases de données petites à moyennes et les serveurs web au trafic faible à moyen.|
| Optimisé pour le calcul|[Série FSv2](../articles/virtual-machines/fsv2-series.md)| Ratio processeur/mémoire élevé. Convient pour les serveurs web au trafic moyen, les appareils réseau, les processus de traitement par lots et les serveurs d’application.|
| Mémoire optimisée|[Série ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series), [Série Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series), [Série Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series), [Série Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series), [Série M](../articles/virtual-machines/m-series.md), [Série Mv2](../articles/virtual-machines/mv2-series.md)|Ratio mémoire/processeur élevé. Idéal pour les serveurs de base de données relationnelle, les caches moyens à grands et l’analytique en mémoire.
| Optimisé pour le stockage|[Série LSv2](../articles/virtual-machines/lsv2-series.md)|Débit et nombre d’E/S de disque élevés, idéal pour les Big Data, SQL, les bases de données NoSQL, l’entreposage de données et les grandes bases de données transactionnelles.|
| Optimisé pour le GPU|[Série NCv2](../articles/virtual-machines/ncv2-series.md), [Série NCv3](../articles/virtual-machines/ncv3-series.md), [Série NCasT4_v3](../articles/virtual-machines/nct4-v3-series.md), [Série ND](../articles/virtual-machines/nd-series.md), [Série NDv2](../articles/virtual-machines/ndv2-series.md), [Série NVv3](../articles/virtual-machines/nvv3-series.md), [Série NVv4](../articles/virtual-machines/nvv4-series.md)| Machines virtuelles spécialisées, ciblées pour l’affichage de graphiques complexes et le montage vidéo, ainsi que pour la formation et l’inférence de modèles avec apprentissage approfondi. Disponible avec un ou plusieurs GPU.|
| <nobr>Performances optimisées</nobr> |[Série HB](../articles/virtual-machines/hb-series.md), [Série HC](../articles/virtual-machines/hc-series.md), [Série HBv2](../articles/virtual-machines/hbv2-series.md)|Les machines virtuelles les plus rapides et dotées des processeurs les plus puissants avec interfaces réseau haut débit en option (RDMA).|

Les disques Ultra ne peuvent pas être utilisés avec certaines fonctionnalités, notamment les captures instantanées de disque, l’exportation de disque, le changement de type de disque, les images de machine virtuelle, les groupes à haute disponibilité, Azure Dedicated Hosts ou Azure Disk Encryption. Sauvegarde Azure et Azure Site Recovery ne prennent pas encore en charge les disques Ultra. En outre, seules les lectures non mises en cache et les écritures non mises en cache sont prises en charge.

Les disques Ultra prennent en charge par défaut une taille de secteur physique de 4 Ko. La taille de secteur 512E est proposée en disponibilité générale sans inscription obligatoire. La plupart des applications sont compatibles avec les tailles de secteur de 4 Ko, toutefois certaines nécessitent des tailles de secteur de 512 octets. Oracle Database par exemple nécessite la version 12.2 ou une version ultérieure pour prendre en charge les disques natifs de 4 Ko. Pour les versions antérieures d’Oracle DB, la taille de secteur de 512 octets est requise.
