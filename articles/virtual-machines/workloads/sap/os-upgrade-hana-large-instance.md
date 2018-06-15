---
title: Mise à niveau du système d’exploitation pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Effectuer la mise à niveau du système d’exploitation pour SAP HANA sur Azure (grandes instances)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f6af1a6612360c2433c05a7add79d2e7b3b9d754
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658258"
---
# <a name="operating-system-upgrade"></a>Mise à niveau du système d’exploitation
Ce document décrit en détail les mises à niveau du système d’exploitation sur les grandes instances HANA.

>[!NOTE]
>La mise à niveau du système d’exploitation est la responsabilité des clients. Le support opérations de Microsoft peut vous orienter vers les endroits clés à surveiller pendant la mise à niveau. Consultez également le fournisseur de votre système d’exploitation avant de planifier une mise à niveau.

Au moment du provisionnement de l’unité HLI, l’équipe des opérations de Microsoft installe le système d’exploitation. Au fil du temps, il vous appartient d’effectuer la maintenance du système d’exploitation sur l’unité HLI : mise à jour corrective, réglage, mise à niveau, etc.

Avant d’apporter des changements majeurs au système d’exploitation (comme la mise à niveau d’un système d’exploitation), **tenez compte** de la matrice de compatibilité suivante. Vous **devez** également contacter l’équipe des opérations de Microsoft avant de lancer des activités majeures sur le système d’exploitation, comme une mise à niveau. Pour cela, ouvrez un ticket de support.

Pour consulter la matrice de prise en charge des différentes versions SAP HANA avec les différentes versions Linux, reportez-vous à [Remarque SAP n° 2235581](https://launchpad.support.sap.com/#/notes/2235581).

La compatibilité suivante a été testée pour les HLI. Si votre serveur HLI ne figure pas dans la matrice de compatibilité, contactez le support opérations de Microsoft.

## <a name="for-type-i-class-sku-category"></a>Catégorie de références SKU de classe de type I

| Configuration | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Microprogramme du serveur | 3.1(2b) | 3.1(2b) | 3.1(2b) | 3.1(2b) |
| Version ENIC | 2.3.0.44 | 2.3.0.44 | 2.3.0.30 | 2.3.0.44 |
| Version FNIC | 1.6.0.34 | 1.6.0.34 | 1.6.0.27 | 1.6.0.36 |
| EDAC | Désactivé | Désactivé | Désactivé | Désactivé |
| Version du noyau | 4.4.21-69-default | 3.12.49-11-default | 3.10.0-327.el7.x86_64 | 3.10.0-693.17.1 |


## <a name="for-type-ii-class-sku-category"></a>Catégorie de références SKU de classe de type II

| Configuration | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Version du microprogramme RMC | 1.1.121  | 1.1.121  | 1.1.121  | 1.1.121 |
| Version du microprogramme BMC | 1.0.43   | 1.0.43   | 1.0.43   | 1.0.43  |
| Version de SFS (Software Foundation Server) | 2.16    | 2.16    | 2.14/2.16   | 2.16   |
| BIOS | 5.2.6    | 5.2.6    | 5.2.6    | 5.2.6   |
| Version i40e    | 2.0.19     | 2.0.19     | 1.5.10-k    | 1.5.10-k   |
| Version du noyau    | 3.12.49-11.1     | 4.4.21-69.1     | 3.10.0-327    | 3.10.0-693.17.1   |


## <a name="known-issues"></a>Problèmes connus

Voici quelques problèmes affectant couramment la mise à niveau :
- Dans la référence SKU de classe de type II, le logiciel SFS est supprimé après la mise à niveau du système d’exploitation. Vous devez réinstaller le logiciel SFS compatible après la mise à niveau du système d’exploitation.
- Une version antérieure des pilotes de cartes Ethernet (ENIC et FNIC) est restaurée. Vous devez réinstaller la version compatible des pilotes après la mise à niveau.

## <a name="next-steps"></a>Étapes suivantes
- Pour la classe de références SKU de type I de sauvegarde du système d’exploitation, consultez [Sauvegarde et restauration](hana-overview-high-availability-disaster-recovery.md).
- Pour la classe de références SKU de type II, consultez [Sauvegarde du système d’exploitation pour références SKU de type II](os-backup-type-ii-skus.md).