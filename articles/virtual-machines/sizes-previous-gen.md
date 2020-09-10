---
title: Tailles des machines virtuelles Azure – Générations précédentes | Microsoft Docs
description: Répertorie les tailles des générations précédentes disponibles pour les machines virtuelles dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines
ms.subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 18389f3058bcc60c22f8598040f4fea4b036f1ee
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298910"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Tailles de machines virtuelles des générations précédentes

Cette section fournit des informations sur les tailles de machines virtuelles des générations précédentes. Ces tailles peuvent toujours être utilisées, mais des générations plus récentes sont disponibles.

## <a name="f-series"></a>Série F

La série F est basée sur le processeur Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz pouvant aller jusqu’à 3,1 GHz avec Intel Turbo Boost Technology 2.0. Ce sont les mêmes performances d’UC que la série Dv2 de machines virtuelles.  

Les machines virtuelles de la série F sont un excellent choix pour les charges de travail qui exigent des processeurs plus rapides, mais n’ont pas besoin d’autant de mémoire ou de stockage temporaire par processeur virtuel.  Les charges de travail telles que l’analyse, les serveurs de jeux, les serveurs web et le traitement par lots tireront avantage de la série F.

ACU : 210 - 250

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS/Mbits/s en lecture/Mbits/s en écriture | Nombre de disques de données/débit max. : E/S par seconde | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1 500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3 000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6 000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12 000 |

## <a name="fs-series-sup1sup"></a>Série Fs <sup>1</sup>

La série Fs propose tous les avantages de la série F, en plus du stockage Premium.

ACU : 210 - 250

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS/Mbits/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4 000/32 (12)    | 3 200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8 000/64 (24)    | 6 400/96   | 2/1 500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16 000/128 (48)  | 12 800/192 | 4/3 000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32 000/256 (96)  | 25 600/384 | 8/6 000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64 000/512 (192) | 51 200/768 | 8/12 000 |

MBps = 10^6 octets par seconde, et Gio = 1024^3 octets.

<sup>1</sup> Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série Fs peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d’informations, consultez [Conception pour de hautes performances](premium-storage-performance.md).


## <a name="nvv2-series"></a>Série NVv2

**Recommandation de taille plus récente** : [Série NVv3](nvv3-series.md)

Les machines virtuelles de la série NVv2 sont pilotées par des GPU [Tesla M60 de NVIDIA](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) et la technologie NVIDIA GRID avec des processeurs Intel Broadwell. Ces machines virtuelles sont destinées aux bureaux virtuels et aux applications graphiques avec accélération par GPU sur lesquels les clients souhaitent visualiser leurs données, simuler des résultats à afficher, travailler en CAO ou effectuer la restitution ou la diffusion du contenu. Par ailleurs, ces machines virtuelles peuvent exécuter des charges de travail de précision unique, telles que le codage et le rendu. Les machines virtuelles NVv2 prennent en charge le Stockage Premium et offrent deux fois plus de mémoire RAM que la série NV précédente.  

Chaque GPU dans les instances NVv2 est fourni avec une licence GRID. Cette licence vous donne la possibilité d’utiliser une instance NV comme station de travail virtuelle pour un seul utilisateur. 25 utilisateurs simultanés peuvent aussi se connecter à la machine virtuelle pour un scénario d’application virtuelle.

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau | Stations de travail virtuelles | Applications virtuelles |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1 280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>Tailles de machines virtuelles des générations antérieures

Cette section fournit des informations sur les tailles de machines virtuelles des anciennes générations. Ces tailles sont toujours prises en charge mais ne peuvent pas recevoir de capacité supplémentaire. Il existe des tailles plus récentes ou alternatives qui sont généralement disponibles. Pour choisir les tailles de machine virtuelle les plus adaptées à vos besoins, voir [Tailles des machines virtuelles Linux dans Azure](./sizes.md).  

Pour plus d’informations sur le redimensionnement d’une machine virtuelle Linux, consultez [Redimensionner une machine virtuelle Linux](linux/change-vm-size.md).  

<br>

### <a name="basic-a"></a>De base A  

**Recommandation de taille plus récente** : [Série Av2](av2-series.md)

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les tailles du niveau De base sont principalement destinées aux charges de travail de développement et aux autres applications qui ne requièrent pas d’équilibrage de charge ou de mise à l’échelle automatique, ou aux machines virtuelles utilisant beaucoup de mémoire.

| Taille - Taille\Nom | Processeurs virtuels | Mémoire|Cartes réseau (max)| Taille max. du disque temporaire | Bande passante disques de données (1 023 Go chacun)| Bande passante Nombre maximal d’opérations d’E/S par seconde (300 par disque) |
|---|---|---|---|---|---|---|
| 0A0\Basic_A0 | 1 | 768 Mo  | 2 | 20 Go  | 1  | 1 x 300  |
| A1\Basic_A1 | 1 | 1,75 Go | 2 | 40 Go  | 2  | 2 x 300  |
| A2\Basic_A2 | 2 | 3,5 Go  | 2 | 60 Go  | 4  | 4 x 300  |
| A3\Basic_A3 | 4 | 7 Go    | 2 | 120 Go | 8  | 8 x 300  |
| A4\Basic_A4 | 8 | 14 Go   | 2 | 240 Go | 16 | 16 x 300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0-A4 à l’aide de l’interface de ligne de commande et de Powershell

Dans le modèle de déploiement classique, certains noms de tailles de machines virtuelles sont légèrement différents dans Powershell et dans l’interface de ligne de commande :

* Standard_A0 est ExtraSmall (Très petit)
* Standard_A1 est Small (Petit)
* Standard_A2 est Medium (Moyen)
* Standard_A3 est Large (Grand)
* Standard_A4 est ExtraLarge (Très grand)

### <a name="a-series"></a>Série A  

**Recommandation de taille plus récente** : [Série Av2](av2-series.md)

ACU : 50-100

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (HDD) : Gio | Disques de données max. | Débit de disque de données max. : E/S par seconde | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> | 1 | 0,768 | 20 | 1 | 1 x 500 | 2/100 |
| Standard_A1 | 1 | 1,75 | 70  | 2  | 2 x 500  | 2/500  |
| Standard_A2 | 2 | 3,5  | 135 | 4  | 4 x 500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8 x 500  | 2/1 000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16 x 500 | 4/2 000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4 x 500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8 x 500  | 2/1 000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16 x 500 | 4/2 000 |

<sup>1</sup> La taille A0 est trop sollicitée sur le matériel physique. Pour cette taille spécifique uniquement, les autres déploiements de clients peuvent affecter les performances de la charge de travail en cours d’exécution. Les performances relatives sont décrites ci-dessous comme référence attendue, soumises à une variation approximative de 15 pour cent.

<br>

### <a name="a-series---compute-intensive-instances"></a>Série A - Instances de calcul intensif  

**Recommandation de taille plus récente** : [Série Av2](av2-series.md)

ACU : 225

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les tailles A8 à A11 et celles de la série H sont également appelées *instances nécessitant beaucoup de ressources système*. Le matériel qui exécute ces tailles a été conçu et optimisé pour les applications nécessitant beaucoup de ressources système et réseau, notamment les applications en cluster pour des calculs complexes, la modélisation et les simulations. La série A8-A11 utilise un processeur Intel Xeon E5-2670 cadencé à 2,6 GHZ, et la série H un processeur Intel Xeon E5-2667 v3 cadencé à 3,2 GHz.  

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (HDD) : Gio | Disques de données max. | Débit de disque de données max. : E/S par seconde | Nombre max de cartes réseau|
|---|---|---|---|---|---|---|
| Standard_A8&nbsp;<sup>1</sup> | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A9&nbsp;<sup>1</sup> | 16 | 112 | 382 | 64 | 64 x 500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64 x 500 | 4 |

<sup>1</sup>Pour les applications MPI, un réseau principal RDMA dédié est activé par un réseau InfiniBand FDR, qui garantit une très faible latence et une large bande passante.  

> [!NOTE]
> La mise hors service des machines virtuelles A8-A11 est planifiée pour le mois de mars 2021. Pour plus d’informations, consultez le [Guide de migration HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

<br>

### <a name="d-series"></a>Série D  

**Recommandation de taille plus récente** : [série Dav4](dav4-dasv4-series.md), [série Dv4](dv4-dsv4-series.md) et [série Ddv4](ddv4-ddsv4-series.md)

ACU : 160-250 <sup>1</sup>

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS/Mbits/s en lecture/Mbits/s en écriture | Nombre de disques de données/débit max. : E/S par seconde | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
|---|---|---|---|---|---|---|
| D1 standard  | 1 | 3,5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| D2 standard  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1 000 |
| D3 standard  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2 000 |
| D4 standard  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4 000 |

<sup>1</sup> La famille de machines virtuelles peut s’exécuter sur l’un des processeurs suivants : Intel Xeon® E5-2660 v2 de 2,2 GHz, Intel Xeon® E5-2673 v3 de 2,4 GHz (Haswell) ou Intel XEON® E5-2673 v4 de 2,3 GHz (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Série D - à mémoire optimisée  

**Recommandation de taille plus récente** : [série Dav4](dav4-dasv4-series.md), [série Dv4](dv4-dsv4-series.md) et [série Ddv4](ddv4-ddsv4-series.md)

ACU : 160-250 <sup>1</sup>

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS/Mbits/s en lecture/Mbits/s en écriture | Nombre de disques de données/débit max. : E/S par seconde | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
|---|---|---|---|---|---|---|
| D11 standard | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1 000 |
| D12 standard | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2 000 |
| D13 standard | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4 000 |
| D14 standard | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8 000 |

<sup>1</sup> La famille de machines virtuelles peut s’exécuter sur l’un des processeurs suivants : Intel Xeon® E5-2660 v2 de 2,2 GHz, Intel Xeon® E5-2673 v3 de 2,4 GHz (Haswell) ou Intel XEON® E5-2673 v4 de 2,3 GHz (Broadwell)  

<br>

## <a name="preview-dc-series"></a>Aperçu : Série DC

**Recommandation de taille plus récente** : [Série DCsv2](dcv2-series.md)

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge

La série DC utilise le processeur Intel XEON E-2176G 3,7 GHz de dernière génération avec la technologie SGX et peut atteindre 4,7 GHz avec Intel Turbo Boost Technology. 

| Taille          | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1 500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3 000                                     |

> [!IMPORTANT]
>
> Les machines virtuelles de la série DC sont des [machines virtuelles de 2e génération](./linux/generation-2.md#creating-a-generation-2-vm) et ne prennent en charge que les images `Gen2`.


### <a name="ds-series"></a>Série DS  

**Recommandation de taille plus récente** : [série Dasv4](dav4-dasv4-series.md), [série Dsv4](dv4-dsv4-series.md) et [série Ddsv4](ddv4-ddsv4-series.md)

ACU : 160-250 <sup>1</sup>

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS/Mbits/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3,5 | 7  | 4  | 4 000/32 (43)    | 3 200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8 000/64 (86)    | 6 400/64   | 2/1 000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16 000/128 (172) | 12 800/128 | 4/2 000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32 000/256 (344) | 25 600/256 | 8/4 000 |

<sup>1</sup> La famille de machines virtuelles peut s’exécuter sur l’un des processeurs suivants : Intel Xeon® E5-2660 v2 de 2,2 GHz, Intel Xeon® E5-2673 v3 de 2,4 GHz (Haswell) ou Intel XEON® E5-2673 v4 de 2,3 GHz (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Série DS - à mémoire optimisée  

**Recommandation de taille plus récente** : [série Dasv4](dav4-dasv4-series.md), [série Dsv4](dv4-dsv4-series.md) et [série Ddsv4](ddv4-ddsv4-series.md)

ACU : 160-250 <sup>1,2</sup>

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS/Mbits/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8 000/64 (72)    | 6 400/64   | 2/1 000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16 000/128 (144) | 12 800/128 | 4/2 000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32 000/256 (288) | 25 600/256 | 8/4 000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64 000/512 (576) | 51 200/512 | 8/8 000 |

<sup>1</sup> Le débit de disque maximal possible (E/S par seconde ou Mbits/s) avec une machine virtuelle de la série DS peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d’informations, consultez [Conception pour de hautes performances](premium-storage-performance.md).
<sup>2</sup> La famille de machines virtuelles peut s’exécuter sur l’un des processeurs suivants : Intel Xeon® E5-2660 v2 de 2,2 GHz, Intel Xeon® E5-2673 v3 de 2,4 GHz (Haswell) ou Intel XEON® E5-2673 v4 de 2,3 GHz (Broadwell)  

<br>

### <a name="ls-series"></a>Série Ls

**Recommandation de taille plus récente** : [Série Lsv2](lsv2-series.md)

La série Ls offre jusqu’à 32 processeurs virtuels, grâce à la [Famille de processeurs Intel® Xeon® E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Cette série propose les mêmes performances de processeur que celles de la série G/GS, associées à 8 Gio de mémoire par processeur virtuel.

La série Ls ne prend pas en charge la création d’un cache local pour augmenter l’IOPS de façon à atteindre les performances des disques de données durables. Le débit élevé et les IOPS du disque local rendent les machines virtuelles de la série Ls idéales pour les magasins NoSQL tels qu’Apache Cassandra et MongoDB, qui répliquent les données sur plusieurs machines virtuelles pour assurer une persistance en cas de défaillance d’une seule machine virtuelle.

ACU : 180-240

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Taille | Processeurs virtuels | Mémoire (Gio) | Stockage temporaire (Gio) | Disques de données max. | Débit de stockage temporaire maximal (IOPS/Mbits/s) | Débit de disque maximal sans mise en cache (IOPS/Mbits/s) | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20 000/200 | 5 000/125  | 2/4 000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40 000/400 | 10 000/250 | 4/8 000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80 000/800 | 20 000/500 | 8/16 000 |
| Standard_L32s&nbsp;<sup>1</sup> | 32 | 256 | 5630 | 64 | 160 000/1 600 | 40 000/1 000 | 8/20 000 |

Le débit de disque maximal possible avec des machines virtuelles de la série Ls peut être limité par le nombre, la taille et la répartition des disques attachés. Pour plus d’informations, consultez [Conception pour de hautes performances](premium-storage-performance.md).

<sup>1</sup> L’instance est isolée sur un matériel dédié à un client unique.

### <a name="gs-series"></a>Série GS

**Recommandation de taille plus récente** : [série Easv4](eav4-easv4-series.md), [série Esv4](ev4-esv4-series.md), [série Edsv4](edv4-edsv4-series.md) et [série M](m-series.md)

ACU : 180 - 240 <sup>1</sup>

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10 000/100 (264)  | 5 000/125  | 2/2 000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20 000/200 (528)  | 10 000/250 | 2/4 000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40 000/400 (1056) | 20 000/500 | 4/8 000 |
| Standard_GS4&nbsp;<sup>3</sup> | 16 | 224 | 448 | 64 | 80 000/800 (2112) | 40 000/1 000 | 8/16 000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> | 32 | 448 |896 | 64 |160 000/1 600 (4224) | 80 000/2 000 | 8/20 000 |

<sup>1</sup> Le débit de disque maximal possible (E/S par seconde ou MBps) avec une machine virtuelle de la série GS peut être limité par le nombre, la taille et la répartition des disques attachés. Pour plus d’informations, consultez [Conception pour de hautes performances](premium-storage-performance.md).

<sup>2</sup> L’instance est isolée sur un matériel dédié à un client unique.

<sup>3</sup> Tailles avec nombre de cœurs limité disponibles.

<br>

### <a name="g-series"></a>Série G

**Recommandation de taille plus récente** : [série Eav4](eav4-easv4-series.md), [série Ev4](ev4-esv4-series.md), [série Edv4](edv4-edsv4-series.md) et [série M](m-series.md)

ACU : 180 - 240

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS/Mbits/s en lecture/Mbits/s en écriture | Nombre de disques de données/débit max. : E/S par seconde | Nombre de cartes réseau/bande passante réseau attendue (Mbits/s) max. |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2 000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4 000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8 000  |
| Standard_G4  | 16 | 224 | 3 072 | 48000/750/375 | 64/64x500 | 8/16 000 |
| Standard_G5&nbsp;<sup>1</sup> | 32 | 448 | 6144 | 96 000/1 500/750| 64/64x500 | 8/20 000 |

<sup>1</sup> L’instance est isolée sur un matériel dédié à un client unique.
<br>

## <a name="nv-series"></a>Série NV
**Recommandation de taille plus récente** : [Série NVv3](nvv3-series.md) et [série NVv4](nvv4-series.md)

Les machines virtuelles de la série NV sont optimisées par des GPU [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) et la technologie NVIDIA GRID pour les applications de bureautique accélérées et les bureaux virtuels où les clients peuvent visualiser leurs données ou simulations. Les utilisateurs peuvent visualiser leurs flux de travail nécessitant beaucoup de graphismes sur les instances NV afin d’obtenir des fonctionnalités graphiques de qualité supérieure et exécuter par ailleurs des charges de travail simple précision comme le codage et le rendu. Les machines virtuelles de la série NV sont également dotées de processeurs Intel Xeon E5-2690 v3 (Haswell).

Chaque GPU dans les instances NV est fourni avec une licence GRID. Cette licence vous donne la possibilité d’utiliser une instance NV comme station de travail virtuelle pour un seul utilisateur. 25 utilisateurs simultanés peuvent aussi se connecter à la machine virtuelle pour un scénario d’application virtuelle.

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Migration dynamique : Non pris en charge

Mises à jour avec préservation de la mémoire : Non pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau | Stations de travail virtuelles | Applications virtuelles |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = une moitié de carte M60.
<br>

## <a name="other-sizes"></a>Autres tailles

* [Usage général](sizes-general.md)
* [Optimisé pour le calcul](sizes-compute.md)
* [Mémoire optimisée](sizes-memory.md)
* [Optimisé pour le stockage](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [Calcul haute performance](sizes-hpc.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
