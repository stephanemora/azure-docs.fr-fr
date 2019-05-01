---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/08/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: eaa6ff1384116064d88061b6eaf7f448e2222cd3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744122"
---
Les tailles de machine virtuelle à usage général assurent un ratio processeur/mémoire équilibré. Idéal pour le test et le développement, les bases de données petites à moyennes et les serveurs web au trafic faible à moyen. Cet article donne des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur le débit de stockage pour chacune des tailles de ce regroupement. 

- La [série DC](#dc-series) est une nouvelle famille de machines virtuelles dans Azure qui peut aider à protéger la confidentialité et l’intégrité de vos données et de votre code lors du traitement dans le cloud public. Ces machines bénéficient du processeur 3,7 GHz Intel XEON E-2176G de dernière génération avec la technologie SGX. Avec Intel Turbo Boost Technology, ces machines peuvent atteindre 4,7 GHz. Les instances de la série DC permettent aux clients de créer des applications sécurisées basées sur enclave pour protéger leur code et leurs données en cours d’utilisation.

- Les machines virtuelles de la série Av2 peuvent être déployées sur différents types de matériel et différents processeurs. Les machines virtuelles de la série A affichent des performances d’unité centrale et des configurations de mémoire idéales pour les charges de travail de niveau d’entrée, propres au développement et au test. La taille est limitée, en fonction du matériel, pour offrir des performances de processeur cohérentes pour l’instance en cours d’exécution, quel que soit le matériel sur lequel elle est déployée. Pour déterminer le matériel physique sur lequel cette taille est déployée, interrogez le matériel virtuel à partir de la machine virtuelle.

  Voici des exemples de cas d’usage : serveurs de développement et de test, serveurs web à faible trafic, bases de données de petite et moyenne tailles, preuves de concept et dépôts de code.

- La série Dv2, qui a suivi la série D d’origine, est dotée d’un processeur plus performant et d’une configuration avec un ratio processeur/mémoire optimal, ce qui en fait la série idéale pour la plupart des charges de travail de production. Le processeur de la série Dv2 est environ 35 % plus rapide que le processeur de la série D. Il est basé sur la dernière génération Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz ou processeurs E5-2673 v4 (Broadwell) de 2,3 GHz et peuvent aller jusqu'à 3,1 GHz avec Intel Turbo Boost Technology 2.0,. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.

- La série Dv3 est dotée du processeur Intel Xeon® E5-2673 v3 (Haswell) cadencé à 2,4 Ghz ou du dernier processeur Intel XEON® E5-2673 v4 (Broadwell) cadencé à 2,3 GHz dans une configuration multithread, fournissant ainsi une meilleure proposition de valeur pour la plupart des charges de travail à usage général.  La mémoire a été étendue (d’environ 3,5 Gio/vCPU à 4 Gio/vCPU) et les limites de disque et de réseau ont été ajustées au niveau du cœur pour s’aligner sur la transition vers l’hyperthreading.  Le Dv3 n’offre plus les tailles de machines virtuelles à haute mémoire des familles D/Dv2, qui ont été déplacées vers la nouvelle famille Ev3.

  Exemples de cas d’utilisation de la série D incluent des applications d’entreprise, bases de données relationnelles, la mise en cache en mémoire et analytique. 
  
## <a name="b-series"></a>Série B

Premium Storage :  Pris en charge

Mise en cache de stockage Premium :  Non pris en charge

Les machines virtuelles extensibles de la série B sont idéales pour les charges de travail ne nécessitant pas en permanence les performances complètes du processeur, comme les serveurs web, les petites bases de données et les environnements de test et de développement. Ces charges de travail ont généralement des exigences modulables en termes de performances. La série B permet à ces clients d’acheter une taille de machine virtuelle aux performances de base sensibles au prix qui permet à l’instance de machine virtuelle de générer des crédits quand la machine virtuelle n’utilise pas la totalité de ses performances de base. Dès que la machine virtuelle a cumulé des crédits, celle-ci peut étendre ses performances en utilisant jusqu’à 100 % du processeur virtuel lorsque l’application requiert des performances de processeur plus élevées.

Voici des exemples de cas d’usage : serveurs de développement et de test, serveurs web à faible trafic, bases de données de petite taille, microservices, serveurs de preuve de concept et serveurs de build.


| Taille             | Processeurs virtuels  | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Perf. du processeur de base de machine virtuelle | Perf. du processeur max. de machine virtuelle | Crédits cumulés/heure | Crédits cumulés max. | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / Mbit/s  | Débit du disque non mis en cache max. : IOPS / Mbit/s  | Nombre max de cartes réseau |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5 %                   | 100 %                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100 %                   | 6.                  | 144            | 2                                      | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100 %                   | 12                 | 288            | 2                                      | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60 %                   | 200%                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |

<sup>1</sup> B1ls est pris en charge uniquement sur Linux

## <a name="dsv3-series-sup1sup"></a>Série Dsv3 <sup>1</sup>

ACU : 160-190

Premium Storage :  Pris en charge

Mise en cache de stockage Premium :  Pris en charge

Les tailles des machines virtuelles de la série Dsv3 sont basées sur le processeur Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz ou sur le dernier processeur Intel XEON® E5-2673 v4 (Broadwell) de 2,3 GHz qui peuvent aller jusqu’à 3,5 GHz avec Intel Turbo Boost Technology 2.0 et utilisent un stockage premium. Les tailles des machines virtuelles de la série Dsv3 offrent une combinaison de processeur virtuel, mémoire et stockage temporaire pour la plupart des charges de travail de production.


| Taille             | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / Mbit/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / Mbit/s | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4 000 / 32 (50)                                                       | 3 200 / 48                                | 2 / 1 000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8 000 / 64 (100)                                                      | 6 400 / 96                                | 2 / 2 000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16 000 / 128 (200)                                                    | 12 800 / 192                              | 4 / 4 000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32 000 / 256 (400)                                                    | 25 600 / 384                              | 8 / 8 000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64 000 / 512 (800)                                                    | 51 200 / 768                              | 8 / 16 000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128,000 / 1024 (1600)                                                    | 80 000 / 1 200                              | 8 / 30 000                                               |

<sup>1</sup> Machines virtuelles de la série Dsv3 dotées de la technologie Hyper-Threading d’Intel®

## <a name="dv3-series-sup1sup"></a>Série Dv3 <sup>1</sup>

ACU : 160-190

Premium Storage :  Non pris en charge

Mise en cache de stockage Premium :  Non pris en charge

Les tailles des machines virtuelles de la série Dv3 sont basées sur le processeur Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz ou sur le processeur Intel XEON® E5-2673 v4 (Broadwell) de 2,3 GHz qui peuvent aller jusqu’à 3,5 GHz avec Intel Turbo Boost Technology 2.0. Les tailles des machines virtuelles de la série Dv3 offrent une combinaison de processeur virtuel, mémoire et stockage temporaire pour la plupart des charges de travail de production.

Le stockage sur disque de données est facturé séparément des machines virtuelles. Pour utiliser les disques de stockage Premium, utilisez des machines virtuelles au format Dsv3. Les tarifs et compteurs de facturation pour les tailles Dsv3 sont identiques à celles de la série Dv3. 


| Taille            | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire max. : IOPS / Mbit/s en lecture / Mbit/s  en écriture | Cartes réseau (max)/Bande passante réseau |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1 000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2 000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4 000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8 000                    |
| Standard_D32_v3 | 32        | 128          | 800            | 32             | 48000/750/375                                            | 8 / 16 000                             |
| Standard_D64_v3 | 64        | 256          | 1 600            | 32             | 96000/1000/500                                            | 8 / 30 000                             |

<sup>1</sup> Machines virtuelles de série Dv3 dotées de la technologie Hyper-Threading d’Intel®

## <a name="dsv2-series"></a>Séries DSv2

ACU : 210-250

Premium Storage :  Pris en charge

Mise en cache de stockage Premium :  Pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / Mbit/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / Mbit/s | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |4 |4 000 / 32 (43) |3 200 / 48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8 000 / 64 (86) |6 400 / 96 |2 / 1 500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16 000 / 128 (172) |12 800 / 192 |4 / 3 000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32 000 / 256 (344) |25 600 / 384 |8 / 6 000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64 000 / 512 (688) |51 200 / 768 |8 / 12000 |

## <a name="dv2-series"></a>Série Dv2

ACU : 210-250

Premium Storage :  Non pris en charge

Mise en cache de stockage Premium :  Non pris en charge

| Taille           | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / Mbit/s en lecture / Mbit/s  en écriture | Disques de données max. | Débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 4              | 4 x 500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8 x 500            | 2 / 1 500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16 x 500           | 4 / 3 000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32 x 500           | 8 / 6 000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64 x 500           | 8 / 12000                                    |

## <a name="av2-series"></a>Série Av2

ACU : 100

Premium Storage :  Non pris en charge

Mise en cache de stockage Premium :  Non pris en charge


| Taille            | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / Mbit/s en lecture / Mbit/s en écriture | Disques de données max. / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1 000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500             | 8 / 2 000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1 000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500             | 8 / 2 000                     |

## <a name="dc-series"></a>Série DC

Premium Storage : Pris en charge

Mise en cache de stockage Premium : Pris en charge



| Taille          | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / Mbit/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / Mbit/s | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1 500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3 000                                     |







