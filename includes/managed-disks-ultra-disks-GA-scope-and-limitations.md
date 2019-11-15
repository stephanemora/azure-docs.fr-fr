---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c847b110cb821ebe3ca7e706fcaec592dcc58fa8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600971"
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
- Toutes les tailles de machines virtuelles ne sont pas disponibles dans toutes les régions prises en charge avec des disques Ultra.
- Ils sont disponibles seulement comme des disques de données et prennent en charge uniquement une taille de secteur physique de 4 k  
- Ils peuvent être créés seulement comme des disques vides  
- Ils ne prennent pas encore en charge les captures instantanées de disques, les images de machines virtuelles, les groupes à haute disponibilité et Azure Disk Encryption.
- Ils ne prennent pas encore en charge l’intégration aux services Sauvegarde Azure ou Azure Site Recovery
- À l’heure actuelle, le nombre limite d’IOPS sur les machines virtuelles en disponibilité générale est de 80 000.