---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/16/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 8d90d71b8d29d26f09ef617ddd56ce91eb4e5e2e
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541465"
---
Cette section fournit des informations sur les tailles de machines virtuelles des générations précédentes. Ces tailles peuvent toujours être utilisées, mais des générations plus récentes sont disponibles. 

## <a name="f-series"></a>Série F

La série F est basée sur le processeur Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz pouvant aller jusqu’à 3,1 GHz avec Intel Turbo Boost Technology 2.0. Ce sont les mêmes performances d’UC que la série Dv2 de machines virtuelles.  

Les machines virtuelles de la série F sont un excellent choix pour les charges de travail qui exigent des processeurs plus rapides, mais n’ont pas besoin d’autant de mémoire ou de stockage temporaire par processeur virtuel.  Les charges de travail telles que l’analyse, les serveurs de jeux, les serveurs web et le traitement par lots tireront avantage de la série F.

ACU : 210 - 250

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Size         | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Disques de données max. / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>Série Fs <sup>1</sup>

La série Fs propose tous les avantages de la série F, en plus du stockage Premium.

ACU : 210 - 250

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000 / 32 (12) |3200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8000 / 64 (24) |6400 / 96 |2 / 1 500 |
| Standard_F4s |4 |8 |16 |16 |16000 / 128 (48) |12800 / 192 |4 / 3 000 |
| Standard_F8s |8 |16 |32 |32 |32000 / 256 (96) |25600 / 384 |8 / 6 000 |
| Standard_F16s |16 |32 |64 |64 |64000 / 512 (192) |51200 / 768 |8 / 12000 |

MBps = 10^6 octets par seconde, et Gio = 1024^3 octets.

<sup>1</sup> Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série Fs peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d’informations, consultez [Conception pour de hautes performances](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="nvv2-series"></a>Série NVv2

**Recommandation de taille plus récente** : [Série NVv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series-preview-1)

Les machines virtuelles de la série NVv2 sont pilotées par des GPU [Tesla M60 de NVIDIA](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) et la technologie NVIDIA GRID avec des processeurs Intel Broadwell. Ces machines virtuelles sont destinées aux bureaux virtuels et aux applications graphiques avec accélération par GPU sur lesquels les clients souhaitent visualiser leurs données, simuler des résultats à afficher, travailler en CAO ou effectuer la restitution ou la diffusion du contenu. Par ailleurs, ces machines virtuelles peuvent exécuter des charges de travail de précision unique, telles que le codage et le rendu. Les machines virtuelles NVv2 prennent en charge le Stockage Premium et offrent deux fois plus de mémoire RAM que la série NV précédente.  

Chaque GPU dans les instances NVv2 est fourni avec une licence GRID. Cette licence vous donne la possibilité d’utiliser une instance NV comme station de travail virtuelle pour un seul utilisateur. 25 utilisateurs simultanés peuvent aussi se connecter à la machine virtuelle pour un scénario d’application virtuelle.

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau | Stations de travail virtuelles | Applications virtuelles | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1 280 | 4 | 32 | 32 | 8 | 4 | 100 |
