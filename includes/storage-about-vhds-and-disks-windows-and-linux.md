---
title: Fichier Include
description: Fichier Include
services: storage
author: rogara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2ab2ac1e98e25da4065611d32fb50aa8ce809361
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060353"
---
## <a name="about-vhds"></a>À propos des VHD

Les VHD utilisés dans Azure sont des fichiers .vhd stockés comme objets blob de pages dans un compte de stockage Standard ou Premium dans Azure. Pour des informations sur les objets blob de pages, consultez la page [Présentation des objets blob de blocs et des objets blob de pages](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Pour plus d’informations sur le stockage Premium, consultez [High-performance premium storage and Azure VMs](../articles/virtual-machines/windows/premium-storage.md) (Stockage Premium hautes performances et machines virtuelles Azure).

Azure prend en charge le format VHD de disque fixe. Le format fixe définit linéairement le disque logique dans le fichier de façon que l'offset du disque X soit stocké à l'offset de l'objet blob X. Un petit pied de page à la fin de l'objet blob décrit les propriétés du disque dur virtuel. Souvent, le format fixe gaspille de l’espace du fait que la plupart des disques comportent de grandes plages inutilisées. Cependant, Azure stocke les fichiers .vhd dans un format fragmenté : vous profitez donc en même temps de disques fixes et dynamiques. Pour plus d’informations, consultez la page [Prise en main des disques durs virtuels](https://technet.microsoft.com/library/dd979539.aspx).

Dans Azure, tous les fichiers VHD que vous souhaitez utiliser comme source pour créer des disques ou des images sont en lecture seule, à l’exception des fichiers .vhd chargés ou copiés vers le stockage Azure par l’utilisateur (en lecture seule ou en lecture-écriture). Lorsque vous créez un disque ou une image, Azure copie les fichiers .vhd source. Ces copies peuvent être accessibles en lecture seule et en lecture/écriture selon votre utilisation du VHD.

Lorsque vous créez une machine virtuelle à partir d’une image, Azure crée, pour la machine virtuelle, un disque qui est une copie du fichier .vhd source. Pour vous protéger contre une suppression accidentelle, Azure place un bail sur tout fichier .vhd source utilisé pour créer une image, un disque de système d’exploitation ou un disque de données.

Avant de pouvoir supprimer un fichier .vhd source, vous devrez supprimer le bail en supprimant le disque ou l’image. Pour supprimer un fichier .vhd utilisé par une machine virtuelle comme disque de système d’exploitation, vous pouvez supprimer simultanément la machine virtuelle, le disque de système d’exploitation et le fichier .vhd source en supprimant la machine virtuelle et tous les disques associés. Toutefois, la suppression d’un fichier .vhd qui constitue une source pour un disque de données requiert l’exécution de plusieurs étapes dans un ordre défini. Détachez tout d’abord le disque de la machine virtuelle, supprimez le disque, puis supprimez le fichier .vhd.

> [!WARNING]
> Si vous supprimez un fichier .vhd source d’un stockage ou si vous supprimez votre compte de stockage, Microsoft ne pourra pas récupérer ces données pour vous.

## <a name="types-of-disks"></a>Types de disques

Les disques Azure sont conçus pour offrir une disponibilité de 99,999 %. Les disques Azure ont toujours fourni une durabilité de classe Entreprise pour les disques IaaS, avec un taux de défaillance annuel inégalé dans le secteur de zéro %.

Lorsque vous créez vos disques, vous avez le choix entre trois niveaux de performances : disques SSD Premium, SSD Standard (préversion) et stockage HDD Premium. Il existe également deux types de disques : non managés et managés.

### <a name="standard-hdd-disks"></a>Disques HDD Standard

Les disques SSD Standard s’appuient sur des HDD et offrent un stockage économique. Le stockage HDD Standard peut être répliqué localement dans un centre de données ou être géoredondant avec des centres de données principal et secondaire. Pour plus d’informations sur la réplication du stockage, consultez [Réplication du stockage Azure](../articles/storage/common/storage-redundancy.md). 

Pour plus d’informations sur l’utilisation des disques HDD Standard, consultez [Stockage Standard et disques](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks"></a>Disques SSD Standard

Les disques SSD Standard sont conçus pour traiter le même type de charges de travail que les disques HDD Standard, mais offrent des performances et une fiabilité plus homogènes que les HDD. Les disques SSD Standard combinent des éléments de disques SSD Premium et de disques HDD Standard pour former une solution économique parfaitement adaptée aux applications telles que les serveurs web n’ayant pas besoin d’E/S élevées sur les disques. Si applicable, les disques SSD Standard sont l’option de déploiement recommandé pour la plupart des charges de travail. Les disques SSD Standard sont disponibles en tant que disques managés dans toutes les régions, mais sont actuellement disponibles uniquement avec le type de résilience du stockage localement redondant (LRS).

### <a name="premium-ssd-disks"></a>Disques SSD Premium

Les disques SSD Premium s’appuient sur des SSD, et offrent de hautes performances et une faible latence pour les machines virtuelles qui exécutent des charges de travail nécessitant de nombreuses E/S. Généralement, vous pouvez utiliser les disques SSD Premium avec des tailles dont le nom de série comporte un « s ». Par exemple, pour les noms de série Dv3 et Dsv3, vous pouvez utiliser la taille de série Dsv3 avec des disques SSD Premium.  Pour plus d’informations, consultez [Stockage Premium](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Disques non gérés

Les disques non gérés sont le type de disques utilisé traditionnellement par les machines virtuelles. Avec ces disques, vous créez votre propre compte de stockage et spécifiez ce dernier lors de la création du disque. Veillez à ne pas placer un trop grand nombre de disques dans le même compte de stockage, car vous risquez de dépasser les [objectifs d’extensibilité](../articles/storage/common/storage-scalability-targets.md) du compte de stockage (20 000 E/S par seconde, par exemple), avec pour conséquence la limitation des machines virtuelles. Avec les disques non gérés, vous devez déterminer comment maximiser l’utilisation d’un ou plusieurs comptes de stockage pour obtenir des performances optimales de vos machines virtuelles.

### <a name="managed-disks"></a>Disques gérés

Managed Disks gère la création/gestion du compte de stockage en arrière-plan, éliminant les préoccupations liées aux limites d’extensibilité du compte de stockage. Vous spécifiez simplement la taille du disque et le niveau de performances (Standard/Premium) et Azure crée et gère le disque pour vous. Lorsque vous ajoutez des disques ou faites monter ou descendre en puissance la machine virtuelle, vous n’avez pas à vous soucier du stockage utilisé.

Vous pouvez également gérer vos images personnalisées dans un compte de stockage par région Azure et les utiliser pour créer des centaines de machines virtuelles dans le même abonnement. Pour plus d’informations sur les disques gérés, consultez [Vue d’ensemble des disques gérés](../articles/virtual-machines/windows/managed-disks-overview.md).

Nous vous recommandons d’utiliser Azure Managed Disks pour les nouvelles machines virtuelles et de convertir vos anciens disques non gérés en disques gérés afin de tirer parti des nombreuses fonctionnalités disponibles dans Managed Disks.

### <a name="disk-comparison"></a>Comparaison des disques

Le tableau suivant offre une comparaison des niveaux HDD Standard, SSD Standard et SSD Premium pour les disques managés et non managés afin de vous aider dans votre choix. Les tailles signalées par un astérisque sont actuellement en préversion.

|    | Disque Premium Azure |Disque SSD Standard Azure | Disque HDD Standard Azure
|--- | ------------------ | ------------------------------- | -----------------------
| Type de disque | SSD (Solid State Drive) | SSD (Solid State Drive) | Disques durs  
| Vue d’ensemble  | Stockage hautes performances et à faible latence sur disque SSD pour les machines virtuelles qui exécutent des charges de travail nécessitant de nombreuses E/S ou qui hébergent un environnement de production stratégique |Performances et fiabilité plus homogènes qu’un HDD. Optimisé pour les charges de travail à faibles E/S| Disque basé sur HDD économique pour l’accès peu fréquent
| Scénario  | Charges de travail de production et sensibles aux performances |Serveurs web, applications d’entreprise peu utilisées et Dev/Test| Sauvegarde, non critique, accès peu fréquent
| Taille du disque | P4 : 32 Gio (Disques managés uniquement)<br>P6 : 64 Gio (Disques managés uniquement)<br>P10 : 128 Gio<br>P15 : 256 Gio (Disques managés uniquement)<br>P20 : 512 Gio<br>P30 : 1 024 Gio<br>P40 : 2 048 Gio<br>P50 : 4,095 Gio<br>P60 : 8 192 Gio * (8 Tio)<br>P70 : 16 384 Gio * (16 Tio)<br>P80 : 32 767 Gio * (32 Tio) |Disques managés uniquement :<br>E10 : 128 Gio<br>E15 : 256 Gio<br>E20 : 512 Gio<br>E30 : 1 024 Gio<br>E40 : 2 048 Gio<br>E50 : 4 095 Gio<br>E60 : 8 192 Gio * (8 Tio)<br>E70 : 16 384 Gio * (16 TiB)<br> E80 : 32 767 Gio * (32 Tio) | Disques non managés : 1 Gio – 4 Tio (4 095 Gio) <br><br>Disques gérés :<br> S4 : 32 Gio <br>S6 : 64 Gio <br>S10 : 128 Gio <br>S15 : 256 Gio <br>S20 : 512 Gio <br>S30 : 1 024 Gio <br>S40 : 2 048 Gio<br>S50 : 4 095 Gio<br>S60 : 8 192 Gio * (8 Tio)<br>S70 : 16 384 Gio * (16 Tio)<br>S80 : 32 384 Gio * (32 Tio)
| Débit max. par disque | P4 : 25 Mio/s<br> P6 : 50 Mio/s<br> P10 : 100 Mio/s<br> P15 : 200 Mio/s<br> P20 : 150 Mio/s<br> P30 : 200 Mio/s<br> P40-50 : 250 Mio/s<br> P60 : 480 Mio/s<br> P70-80 : 750 Mio/s | E10-E50 : jusqu’à 60 Mio/s<br> E60 : jusqu’à 300 Mio/s *<br> E70-E80 : 500 Mio/s *| S4-S50 : jusqu’à 60 Mio/s<br> S60 : jusqu’à 300 Mio/s *<br> S70-S80 : jusqu’à 500 Mio/s *
| Nb max. d’E/S par seconde par disque | P4 : 120 IOPS<br> P6 : 240 IOPS<br> P10 : 500 IOPS<br> P15 : 1 100 IOPS<br> P20 : 2 300 IOPS<br> P30 : 500 IOPS<br> P40-P50 : 7 500 IOPS<br> P60 : 12 500 IOPS *<br> P70 : 15 000 IOPS *<br> P80 : 20 000 IOPS * | E10-E50 : jusqu’à 500 IOPS<br> E60 : jusqu’à 1 300 IOPS *<br> E70-E80 : jusqu’à 2 000 IOPS * | S4-S50 : jusqu’à 500 IOPS<br> S60 : jusqu’à 1 300 IOPS *<br> S70-S80 : jusqu’à 2 000 IOPS *