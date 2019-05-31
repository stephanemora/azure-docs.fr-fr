---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/16/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 87fca23cab27ec27bfc9799066c126994167f46e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391368"
---
Les tailles de machine virtuelle à mémoire optimisée offrent un ration mémoire/processeur supérieur pour les serveurs de base de données relationnelle, les caches moyens à grands et l’analytique en mémoire. Cet article fournit des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau ainsi que sur la bande passante réseau et le débit de stockage pour chaque taille de ce regroupement. 

* La série Mv2 offre le nombre de processeurs virtuels le plus élevé (jusqu'à 208 processeurs virtuels) et une plus grande mémoire (jusqu'à 5.7 TIO) parmi toutes les machines virtuelles dans le cloud. Elle est idéale pour les très grandes bases de données ou d’autres applications qui bénéficient d’un nombre élevé de processeurs virtuels et de grandes quantités de mémoire.
 
* La série M offre un nombre élevé de processeurs virtuels (jusqu'à 128 processeurs virtuels) et une grande quantité de mémoire (jusqu'à 3,8 TIO). Il est également idéale pour les bases de données très volumineuses ou d’autres applications qui bénéficient d’un nombre élevé de processeurs virtuels et de grandes quantités de mémoire.

* Série Dv2, série G et DSv2/GS sont idéales pour les applications qui exigent des processeurs virtuels plus rapides, de meilleures performances de stockage temporaire, ou qui ont des exigences de mémoire plus élevées. Elles offrent une combinaison puissante pour de nombreuses applications professionnelles.

* La série Dv2, suite de la série D d’origine, comprend un processeur plus puissant. Le processeur de la série Dv2 est environ 35 % plus rapide que le processeur de la série D. Il est basé sur la dernière génération 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz ou processeurs E5-2673 v4 (Broadwell) de 2,3 GHz pouvant aller jusqu'à 3,1 GHz avec Intel Turbo Boost Technology 2.0,. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.

* La série Ev3 dispose du processeur E5-2673 v4 de 2,3 GHz (Broadwell), mais dans une configuration hyperthread, ce qui lui permet d’offrir ce qui se fait de mieux pour les charges de travail à usage général et d’aligner la série Ev3 sur les machines virtuelles à usage général que l’on retrouve dans la plupart des autres clouds.  La mémoire a été étendue (de 7 Gio/vCPU à 8 Gio/vCPU) et les limites de disque et de réseau ont été ajustées au niveau du cœur pour s’aligner sur la transition vers l’hyperthreading.  La série Ev3 constitue la suite des tailles de machines virtuelles à mémoire haute des familles D/Dv2.

* Le Calcul Azure propose des tailles de machines virtuelles qui sont isolées pour un type de matériel spécifique et dédiées à un seul et même client.  Ces tailles de machines virtuelles conviennent mieux aux charges de travail qui nécessitent un niveau élevé d’isolation par rapport aux autres clients pour les charges de travail qui impliquent des éléments tels que les exigences réglementaires et de conformité.  Les clients peuvent également choisir de subdiviser les ressources de ces machines virtuelles isolées à l’aide de la [prise en charge d’Azure pour les machines virtuelles imbriquées](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Consultez les tableaux des familles de machines virtuelles ci-dessous pour connaître les options de machine virtuelle isolée.

## <a name="esv3-series"></a>Série Esv3 

ACU : 160-190 <sup>1</sup>

Premium Storage :  Pris en charge

Mise en cache de stockage Premium :  Pris en charge

Les tailles des machines virtuelles de la série ESv3 sont basées sur le processeur Intel XEON® v4-2.0 5GHz (Broadwell) de 2673 GHz, peuvent aller jusqu’à 3,5 GHz avec Intel Turbo Boost Technology 2.0 et utilisent un stockage premium. Les tailles des machines virtuelles de la série ESv3 sont idéales pour les applications d’entreprise nécessitant une mémoire importante.


| Taille             | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / Mbit/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / Mbit/s | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1 000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2 000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000 / 320 (400)                                                    | 32000 / 480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16 000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |


<sup>1</sup> Machines virtuelles de série Esv3 dotées de la technologie Hyper-Threading d’Intel®.

<sup>2</sup> Tailles avec nombre de cœurs limité disponibles.

<sup>3</sup> L’instance est isolée sur un matériel dédié à un client unique.


## <a name="ev3-series"></a>Série Ev3 

ACU : 160 - 190 <sup>1</sup>

Premium Storage :  Non pris en charge

Mise en cache de stockage Premium :  Non pris en charge

Les tailles des machines virtuelles de la série Ev3 sont basées sur le processeur Intel Xeon® v4-2.0 5GHz (Broadwell) de 2673 GHz et peuvent aller jusqu’à 3,5 GHz avec Intel Turbo Boost Technology 2.0. Les tailles des machines virtuelles de la série ESv3 sont idéales pour les applications d’entreprise nécessitant une mémoire importante.

Le stockage sur disque de données est facturé séparément des machines virtuelles. Pour utiliser les disques de stockage Premium, utilisez des machines virtuelles au format ESv3. Les tarifs et compteurs de facturation pour les tailles ESv3 sont identiques à ceux de la série Ev3. 


| Taille            | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire max. : IOPS / Mbit/s en lecture / Mbit/s  en écriture | Cartes réseau (max)/Bande passante réseau |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1 000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2 000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16 000                 |
| Standard_E64_v3 | 64        | 432         | 1 600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1 600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> Machines virtuelles de série Ev3 dotées de la technologie Hyper-Threading d’Intel®.

<sup>2</sup> Tailles avec nombre de cœurs limité disponibles.

<sup>3</sup> L’instance est isolée sur un matériel dédié à un client unique.


## <a name="mv2-series"></a>Série Mv2

Premium Storage : Pris en charge

Mise en cache de stockage Premium : Pris en charge

Accélérateur d’écriture : [Pris en charge](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

La série Mv2 fonctionnalités un débit élevé, faible latence, mappé directement le stockage local NVMe en cours d’exécution sur une platine de® multithreads Intel® Xeon 8180 M 2,5 GHz (Skylake) processeur avec une fréquence de base tous les principaux de 2,5 GHz et une fréquence turbo max de 3,8 GHz. Toutes les tailles de machines virtuelles de série Mv2 peuvent utiliser des disques persistants standards et premium. Instances de la série Mv2 sont de tailles de machine virtuelle fournissant des performances de calcul inégalées pour prendre en charge des bases de données volumineuses en mémoire et les charges de travail, avec un ratio mémoire / processeur élevé qui est idéal pour les serveurs de base de données relationnelle, des caches volumineux et en mémoire à mémoire optimisée analytique. 

|Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / Mbit/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / Mbit/s | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16 000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2 850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16 000 |

Série Mv2 dotées de la technologie Intel® Hyper-Threading  

<sup>1</sup> ces grandes machines virtuelles nécessitent l’une de ces systèmes d’exploitation invités pris en charge : Windows Server 2016, Windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> machines virtuelles de série Mv2 sont la génération 2. Si vous utilisez Linux, consultez la section suivante pour savoir comment rechercher et sélectionner une image SUSE Linux.

#### <a name="find-a-suse-image"></a>Rechercher une image SUSE

Pour sélectionner une image de SUSE Linux appropriée dans le portail Azure : 

1. Dans le portail Azure, sélectionnez **créer une ressource** 
1. Recherchez « SUSE SAP » 
1. SLES pour les images de génération 2 SAP sont disponibles sous la forme d’un paiement à l’utilisation, ou apportez votre propre abonnement (BYOS). Dans les résultats de recherche, développez la catégorie de l’image de votre choix :

    * SUSE Linux Enterprise Server (SLES) pour SAP
    * SUSE Linux Enterprise Server (SLES) pour SAP (BYOS)
    
1. Images SUSE compatibles avec la série Mv2 sont préfixés avec le nom `GEN2:`. Les images SUSE suivantes sont disponibles pour les machines virtuelles de série Mv2 :

    * GÉNÉRATION 2 : SUSE Linux Enterprise Server (SLES) 12 Service Pack 4 pour les Applications SAP
    * GÉNÉRATION 2 : SUSE Linux Enterprise Server (SLES) 15 pour les Applications SAP
    * GÉNÉRATION 2 : SUSE Linux Enterprise Server (SLES) 12 Service Pack 4 pour les Applications SAP (BYOS)
    * GÉNÉRATION 2 : SUSE Linux Enterprise Server (SLES) 15 pour les Applications SAP (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>Sélectionnez une image SUSE via Azure CLI

Pour afficher la liste la SLES actuellement disponibles pour l’image SAP pour les machines virtuelles de série Mv2, utilisez la commande suivante [ `az vm image list` ](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list) commande :

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

La commande génère l’actuellement disponible 2 machines virtuelles de génération disponibles à partir de SUSE pour les machines virtuelles de série Mv2. 

Exemple de sortie :

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>Série M 

ACU : 160-180 <sup>1</sup>

Premium Storage :  Pris en charge

Mise en cache de stockage Premium :  Pris en charge

Accélérateur d’écriture :  [Pris en charge](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Taille            | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / Mbit/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / Mbit/s | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10000 / 100 (793)  | 5000  / 125 | 4 / 2 000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20000 / 200 (1587) | 10000 / 250 | 8 / 4 000 |
| Standard_M32ts | 32 | 192    | 1 024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1 024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1 024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M64s  | 64 | 1 024   | 2 048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16 000          |
| Standard_M64ls  | 64 | 512    | 2 048 | 64 | 80000 / 800 (6348) | 40000 / 1000 | 8 / 16 000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2 048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16 000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2 048        | 4096  | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1 024 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8 / 16 000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8 / 16 000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2 048 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |



<sup>1</sup> Machines virtuelles de série M dotées de la technologie Hyper-Threading d’Intel®

<sup>2</sup> Plus de 64 processeurs virtuels nécessitent l’un de ces systèmes d’exploitation invités pris en charge : Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 et Red Hat Enterprise Linux, CentOS 7.3 ou Oracle Linux 7.3 avec LIS 4.2.1.

<sup>3</sup> Tailles avec nombre de cœurs limité disponibles.

<sup>4</sup> L’instance est isolée sur un matériel dédié à un client unique.
<br>

## <a name="gs-series"></a>Série GS 

ACU : 180 - 240 <sup>1</sup>

Premium Storage :  Pris en charge

Mise en cache de stockage Premium :  Pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / Mbit/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / Mbit/s | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10000 / 100 (264) |5000 / 125 |2 / 2 000 |
| Standard_GS2 |4 |56 |112 |16 |20000 / 200 (528) |10000 / 250 |2 / 4 000 |
| Standard_GS3 |8 |112 |224 |32 |40000 / 400 (1056) |20000 / 500 |4 / 8 000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80000 / 800 (2112) |40000 / 1000 |8 / 16 000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160000 / 1600 (4224) |80000 / 2000 |8 / 20 000 |

<sup>1</sup> Le débit de disque maximal possible (E/S par seconde ou Mbit/s) avec une machine virtuelle de la série GS peut être limité par le nombre, la taille et la répartition des disques attachés. Pour plus d'informations, consultez [Conception pour de hautes performances](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> L’instance est isolée sur un matériel dédié à un client unique.

<sup>3</sup> Tailles avec nombre de cœurs limité disponibles.

<br>

## <a name="g-series"></a>Série G

ACU : 180 - 240

Premium Storage :  Non pris en charge

Mise en cache de stockage Premium :  Non pris en charge

| Taille         | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / Mbit/s en lecture / Mbit/s en écriture | Disques de données max. / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 x 500                       | 2 / 2 000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4 000                     |
| Standard_G3  | 8         | 112         | 1536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8 000                |
| Standard_G4  | 16        | 224         | 3 072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16 000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20 000           |

<sup>1</sup> L’instance est isolée sur un matériel dédié à un client unique.
<br>

## <a name="dsv2-series-11-15"></a>Série DSv2 11-15

ACU : 210 - 250 <sup>1</sup>

Premium Storage :  Pris en charge

Mise en cache de stockage Premium :  Pris en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / Mbit/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS / Mbit/s | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000 / 64 (72) |6400 / 96 |2 / 1 500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000 / 128 (144) |12800 / 192 |4 / 3 000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000 / 256 (288) |25600 / 384 |8 / 6 000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000 / 512 (576) |51200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000 / 640 (720) |64000 / 960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> Le débit de disque maximal possible (E/S par seconde ou Mbit/s) avec une machine virtuelle de la série DSv2 peut être limité par le nombre, la taille et la répartition des disques attachés.  Pour plus d'informations, consultez [Conception pour de hautes performances](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> L’instance est isolée sur un matériel dédié à un client unique.  
<sup>3</sup> Tailles avec nombre de cœurs limité disponibles.  
<sup>4</sup> 25 000 Mbits/s avec accélération réseau. 

<br>

## <a name="dv2-series-11-15"></a>Série Dv2 11-15

ACU : 210 - 250

Premium Storage :  Non pris en charge

Mise en cache de stockage Premium :  Non pris en charge

| Taille              | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire max. : IOPS / Mbit/s en lecture / Mbit/s en écriture | Disques de données max. / débit : E/S par seconde | Nombre max de cartes réseau / Bande passante réseau attendue (Mbit/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1 500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1 000          | 60000 / 937 / 468                                        | 64 / 64 x 500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> L’instance est isolée sur un matériel dédié à un client unique.  
<sup>2</sup> 25 000 Mbit/s avec mise en réseau accélérée. 
