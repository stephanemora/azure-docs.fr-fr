---
title: Possibilité de prise en charge de l’ajout de machines virtuelles Azure à un groupe à haute disponibilité | Microsoft Docs
description: Cet article fournit un tableau de prise en charge sur les séries de machines virtuelles différentes que vous pouvez ajouter dans le même groupe à haute disponibilité.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: cb0034f2b353284e94d6f1508541b31040a5b076
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028408"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Possibilité de prise en charge de l’ajout de machines virtuelles Azure à un groupe à haute disponibilité

Vous pouvez parfois rencontrer des limitations lorsque vous ajoutez des machines virtuelles à un groupe à haute disponibilité. Le tableau suivant détaille les séries de machines virtuelles que vous pouvez combiner dans le même groupe à haute disponibilité.

Voici la matrice de possibilité de prise en charge pour combiner différents types de machines virtuelles :

Série et groupe à haute disponibilité|Deuxième machine virtuelle|Un|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Première machine virtuelle|||||||
|Un||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Les autres séries ne pourraient pas être dans le même groupe à haute disponibilité, car elles nécessitent un matériel spécifique.

Vous ne pouvez pas combiner des machines virtuelles de taille A8/A9 en raison des exigences du réseau principal RDMA dédié.
