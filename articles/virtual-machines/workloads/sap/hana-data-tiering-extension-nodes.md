---
title: Couches Données et nœuds d’extension pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: En savoir plus sur la couche Données et les nœuds d’extension pour SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/17/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d13edf6e2512f6f70af8265e1859b106e75885c
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110577546"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Utiliser une hiérarchisation des données et des nœuds d’extension SAP HANA

SAP prend en charge un modèle de hiérarchisation des données pour SAP Business Warehouse (BW) avec différentes versions SAP NetWeaver et SAP BW/4HANA. Pour plus d’informations sur le modèle de hiérarchisation des données, consultez [SAP BW/4HANA et SAP BW sur HANA avec nœuds d’extension SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).

La grande instance HANA vous permet d’utiliser la configuration de l’option 1 des nœuds d’extension SAP HANA, comme détaillé dans la FAQ et les documents de blog SAP. Les configurations de l’option 2 peuvent être définies avec les références SKU de grandes instances HANA suivantes : S72m, S192, S192m, S384 et S384m.

## <a name="advantages-of-sap-hana-extension-nodes"></a>Avantages des nœuds d’extension SAP HANA

L’utilisation des nœuds d’extension SAP HANA, option 1 ou 2, est un moyen simple de mieux utiliser la mémoire SAP HANA. Les avantages des nœuds d’extension SAP HANA deviennent évidents lorsque vous examinez les instructions de dimensionnement de SAP. Voici quelques exemples :

- Les instructions de dimensionnement de SAP HANA exigent généralement de doubler la quantité du volume de données par rapport à la mémoire. Lorsque vous exécutez votre instance SAP HANA avec des données à chaud, 50 % ou moins de votre mémoire stocke des données. Dans l’idéal, la mémoire restante est conservée pour que SAP HANA effectue son travail.
- Autrement dit, dans une unité S192 de grande instance HANA, dotée de 2 To de mémoire et exécutant une base de données SAP BW, vous ne disposez que de 1 To en volume de données.
- Si vous utilisez un autre nœud d’extension SAP HANA de l’option 1, ainsi qu’une référence SKU S192 de grande instance HANA, vous obtenez une autre capacité de 2 To en volume de données. Dans la configuration de l’option 2, vous disposez de 4 To supplémentaires pour le volume de données tièdes. Par rapport au nœud à accès rapide, la capacité de mémoire totale du nœud d’extension « à accès moyen » peut servir au stockage des données pour l’option 1. Le doublage de la mémoire peut être utilisé pour le volume de données dans la configuration du nœud d’extension SAP HANA de l’option 2.
- Vous vous retrouvez avec une capacité de 3 To pour vos données et d’un rapport accès rapide/accès moyen de 1:2 pour l’option 1. Vous avez 5 To de données et un rapport de 1:4 avec la configuration de nœud d’extension de l’option 2.

Plus le volume de données est élevé par rapport à la mémoire, plus il est probable que les données à chaud que vous demandez sont stockées sur le disque.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le modèle d’opérations pour SAP HANA sur Azure (grandes instances) et vos responsabilités.

> [!div class="nextstepaction"]
> [Responsabilités et modèle opérationnel](hana-operations-model.md)
