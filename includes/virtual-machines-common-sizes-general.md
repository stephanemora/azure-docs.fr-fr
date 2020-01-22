---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn;joelpell
ms.custom: include file
ms.openlocfilehash: e66314e06aa87a01dcd2a3ff0a51ae86082173e3
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904429"
---
Les tailles de machine virtuelle à usage général assurent un ratio processeur/mémoire équilibré. Idéal pour le test et le développement, les bases de données petites à moyennes et les serveurs web au trafic faible à moyen. Cet article donne des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur le débit de stockage pour chacune des tailles de ce regroupement.

- La [série DC](#dc-series) est une famille de machines virtuelles dans Azure qui permet de protéger la confidentialité et l’intégrité de vos données et de votre code lors de leur traitement dans le cloud public. Ces machines bénéficient du processeur 3,7 GHz Intel XEON E-2176G de dernière génération avec la technologie SGX. Avec Intel Turbo Boost Technology, ces machines peuvent atteindre 4,7 GHz. Les instances de la série DC permettent aux clients de créer des applications sécurisées basées sur enclave pour protéger leur code et leurs données en cours d’utilisation.

- Les machines virtuelles de la série Av2 peuvent être déployées sur différents types de matériel et différents processeurs. Les machines virtuelles de la série A affichent des performances d’unité centrale et des configurations de mémoire idéales pour les charges de travail de niveau d’entrée, propres au développement et au test. La taille est limitée, en fonction du matériel, pour offrir des performances de processeur cohérentes pour l’instance en cours d’exécution, quel que soit le matériel sur lequel elle est déployée. Pour déterminer le matériel physique sur lequel cette taille est déployée, interrogez le matériel virtuel à partir de la machine virtuelle.

  Voici des exemples de cas d’usage : serveurs de développement et de test, serveurs web à faible trafic, bases de données de petite et moyenne tailles, preuves de concept et dépôts de code.

- La série Dv2, qui a suivi la série D d’origine, est dotée d’un processeur plus performant et d’une configuration avec un ratio processeur/mémoire optimal, ce qui en fait la série idéale pour la plupart des charges de travail de production. La série Dv2 est environ 35 % plus rapide que la série D. La série Dv2 s’exécute sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.

- La série Dv3 s’exécute sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) dans une configuration hyper-thread, ce qui offre une meilleure proposition de valeur pour la plupart des charges de travail à usage général.  La mémoire a été étendue (d’environ 3,5 Gio/vCPU à 4 Gio/vCPU) et les limites de disque et de réseau ont été ajustées au niveau du cœur pour s’aligner sur la transition vers l’hyperthreading.  La série Dv3 n’offre plus les tailles de machine virtuelle à mémoire élevée de la série D/Dv2 ; celles-ci ont été déplacées vers la série Ev3 à mémoire optimisée pour [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#ev3-series) et [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#ev3-series).

  Voici des exemples de cas d’usage de la série D : applications de classe Entreprise, bases de données relationnelles, mise en cache en mémoire et analytique.

- Les séries Dav4 et Dasv4 sont de nouvelles tailles qui utilisent le processeur EPYC<sup>TM</sup> 7452 2,35 GHz d’AMD dans une configuration multithread, avec un cache L3 allant jusqu’à 256 Go, dont 8 Go sont dédiés tous les 8 cœurs, ce qui offre aux clients davantage d’options pour exécuter leurs charges de travail à usage général. Elles ont les mêmes configurations de mémoire et de disque que les séries D et Dsv3.
  
## <a name="b-series"></a>Série B

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les machines virtuelles extensibles de la série B sont idéales pour les charges de travail ne nécessitant pas en permanence les performances complètes du processeur, comme les serveurs web, les petites bases de données et les environnements de test et de développement. Ces charges de travail ont généralement des exigences modulables en termes de performances. La série B permet à ces clients d’acheter une taille de machine virtuelle aux performances de base sensibles au prix qui permet à l’instance de machine virtuelle de générer des crédits quand la machine virtuelle n’utilise pas la totalité de ses performances de base. Dès que la machine virtuelle a cumulé des crédits, celle-ci peut étendre ses performances en utilisant jusqu’à 100 % du processeur virtuel lorsque l’application requiert des performances de processeur plus élevées.

Voici des exemples de cas d’usage : serveurs de développement et de test, serveurs web à faible trafic, bases de données de petite taille, microservices, serveurs de preuve de concept et serveurs de build.


| Size             | Processeurs virtuels  | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Perf. du processeur de base de machine virtuelle | Perf. du processeur max. de machine virtuelle | Crédits initiaux | Crédits cumulés/heure | Crédits cumulés max. | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0.5              | 4                          | 5 %                   | 100 %                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10 %                   | 100 %                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100 %                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202 %                  | 1200 %                   | 360                   | 121                 | 2909           | 16                                     | 6480 / 75                                 | 4320 / 50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270 %                  | 1600 %                   | 480                   | 162                 | 3888           | 32                                     | 8640 / 100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337 %                  | 2000 %                   | 600                   | 203                 | 4860           | 32                                     | 10800 / 125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls est pris en charge uniquement par Linux

## <a name="dsv3-series-sup1sup"></a>Série Dsv3 <sup>1</sup>

ACU : 160-190

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

Les tailles de la série Dsv3 s’exécutent sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0, et elles utilisent le Stockage Premium. Les tailles des machines virtuelles de la série Dsv3 offrent une combinaison de processeur virtuel, mémoire et stockage temporaire pour la plupart des charges de travail de production.


| Size             | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1 000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2 000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4/4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16 000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000 / 768 (1200)                                                    | 76800 / 1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000 / 1024 (1600)                                                    | 80000 / 1200                              | 8 / 30000                                               |

<sup>1</sup> Machines virtuelles de la série Dsv3 dotées de la technologie Hyper-Threading d’Intel®

## <a name="dasv4-series"></a>Série Dasv4

ACU :  230-260

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge

Les tailles de la série Dasv4 sont basées sur le processeur AMD EPYC<sup>TM</sup> 7452 2,35 GHz, qui peut atteindre une fréquence maximale renforcée de 3,35 GHz et utiliser un SSD Premium. Elles offrent une combinaison de processeur virtuel, de mémoire et de stockage temporaire adaptée à la plupart des charges de travail de production.

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max. de cartes réseau / Bande passante réseau attendue (Mbits/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000 / 32 (50)|3200 / 48|2 / 1 000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400 / 96|2 / 2 000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)|12800 / 192|4/4000 |
| Standard_D16as_v4|16|64|128|32|32 000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64 000 / 510 (800)|51200 / 768|8 / 16 000 |
| Standard_D48as_v4 <sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4 <sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4 <sup>**</sup>|96|384|768|32| | | 

<sup>**</sup> Ces tailles sont en préversion.  Si vous souhaitez essayer ces grandes tailles, inscrivez-vous à l’adresse [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dv3-series-sup1sup"></a>Série Dv3 <sup>1</sup>

ACU : 160-190

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les tailles de la série Dv3 s’exécutent sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0. Les tailles des machines virtuelles de la série Dv3 offrent une combinaison de processeur virtuel, mémoire et stockage temporaire pour la plupart des charges de travail de production.

Le stockage sur disque de données est facturé séparément des machines virtuelles. Pour utiliser les disques de stockage Premium, utilisez des machines virtuelles au format Dsv3. Les tarifs et compteurs de facturation pour les tailles Dsv3 sont identiques à celles de la série Dv3. 


| Size            | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Cartes réseau (max)/Bande passante réseau (Mbits/s) |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1 000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2 000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4/4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48000/750/375                                            | 8 / 16 000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1 600           | 32             | 96000/1000/500                                           | 8 / 30000                   |

<sup>1</sup> Machines virtuelles de série Dv3 dotées de la technologie Hyper-Threading d’Intel®

## <a name="dav4-series"></a>Série Dav4

ACU : 230-260

Premium Storage : Non pris en charge

Mise en cache du Stockage Premium : Non pris en charge

Les tailles de la série Dav4 sont basées sur le processeur AMD EPYC<sup>TM</sup> 7452 2,35 GHz, qui peut atteindre une fréquence maximale renforcée de 3,35 GHz. Elles offrent une combinaison de processeur virtuel, de mémoire et de stockage temporaire adaptée à la plupart des charges de travail de production. Le stockage sur disque de données est facturé séparément des machines virtuelles. Pour utiliser un SSD Premium, optez pour les tailles Dasv4. Les tarifs et les compteurs de facturation de ces tailles sont identiques à ceux de la série Dav4.

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Nombre max. de cartes réseau / Bande passante réseau attendue (Mbits/s) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 / 1 000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 / 2 000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4/4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 / 16 000 |
| Standard_D48a_v4<sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4<sup>**</sup> | 64 | 256 | 1 600 | 32 | | |
| Standard_D96a_v4<sup>**</sup> | 96 | 384 | 2 400 | 32 | | |

<sup>**</sup> Ces tailles sont en préversion.  Si vous souhaitez essayer ces grandes tailles, inscrivez-vous à l’adresse [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dsv2-series"></a>Séries DSv2

ACU : 210-250

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

Les tailles de la série DSv2 s’exécutent sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0, et elles utilisent le Stockage Premium.

| Size | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |4 |4000 / 32 (43) |3200 / 48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000 / 64 (86) |6400 / 96 |2 / 1 500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000 / 128 (172) |12800 / 192 |4 / 3 000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000 / 256 (344) |25600 / 384 |8 / 6 000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000 / 512 (688) |51200 / 768 |8 / 12000 |

## <a name="dv2-series"></a>Série Dv2

ACU : 210-250

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les tailles de la série DSv2 s’exécutent sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0.

| Size           | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Disques de données max. | Débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 4              | 4 x 500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8 x 500            | 2 / 1 500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16 x 500           | 4 / 3 000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32 x 500           | 8 / 6 000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64 x 500           | 8 / 12000                                    |

## <a name="av2-series"></a>Série Av2

ACU : 100

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

| Size            | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / MBps en lecture / MBps en écriture | Disques de données max. / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1 000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500             | 8 / 2 000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1 000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500             | 8 / 2 000                     |

## <a name="dc-series"></a>Série DC

Premium Storage : Prise en charge

Mise en cache du Stockage Premium : Prise en charge



| Size          | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / MBps | Nombre max de cartes réseau / Bande passante réseau attendue (MBps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1 500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3 000                                     |
