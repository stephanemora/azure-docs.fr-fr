---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935926"
---
Pour le moment, les disques Ultra ont des limitations supplémentaires, notamment :

- Ils sont pris en charge dans les régions suivantes, avec un nombre variable de zones de disponibilité par région :
    - USA Est 2
    - USA Est
    - USA Ouest 2
    - Asie Sud-Est
    - Europe Nord
    - Europe Ouest
    - Sud du Royaume-Uni 
- Ils peuvent être utilisés seulement avec des zones de disponibilité (les groupes à haute disponibilité et les déploiements de machine virtuelle individuelle en dehors des zones n’ont pas la possibilité d’attacher un disque Ultra)
- Ils ne sont pris en charge que sur les séries de machines virtuelles suivantes :
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Les tailles de machines virtuelles ne sont pas toutes disponibles dans toutes les régions prises en charge avec des disques Ultra
- Ils sont disponibles seulement comme des disques de données et prennent en charge uniquement une taille de secteur physique de 4 K. En raison de la taille native de secteur de 4 K des disques Ultra, certaines applications ne sont pas compatibles avec ces derniers. Citons à titre d’exemple Oracle Database, qui nécessite la version 12.2 ou une version ultérieure afin de prendre en charge les disques Ultra.  
- Ils peuvent être créés seulement comme des disques vides  
- Ils ne prennent pas encore en charge les captures instantanées de disques, les images de machines virtuelles, les groupes à haute disponibilité et Azure Disk Encryption.
- Ils ne prennent pas encore en charge l’intégration aux services Sauvegarde Azure ou Azure Site Recovery
- À l’heure actuelle, le nombre limite d’IOPS sur les machines virtuelles en disponibilité générale est de 80 000.
- Si vous souhaitez participer à une préversion limitée d’une machine virtuelle qui peut obtenir 160 000 IOPS par seconde avec des disques ultra, envoyez un e-mail à UltraDiskFeedback@microsoft. com