---
title: Tailles des machines virtuelles Azure - Usage général | Microsoft Docs
description: Liste les différentes tailles à usage général disponibles pour les machines virtuelles dans Azure. Liste des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: b5e597eebda6a730d301eefdbd2d7e6859f5a7cc
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248309"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Tailles de machine virtuelle à usage général

Les tailles de machine virtuelle à usage général assurent un ratio processeur/mémoire équilibré. Idéal pour le test et le développement, les bases de données petites à moyennes et les serveurs web au trafic faible à moyen. Cet article fournit des informations sur les produits informatiques à usage général.

- Les machines virtuelles de la [série Av2](av2-series.md) peuvent être déployées sur différents types de matériel et différents processeurs. Les machines virtuelles de la série A affichent des performances d’unité centrale et des configurations de mémoire idéales pour les charges de travail de niveau d’entrée, propres au développement et au test. La taille est limitée, en fonction du matériel, pour offrir des performances de processeur cohérentes pour l’instance en cours d’exécution, quel que soit le matériel sur lequel elle est déployée. Pour déterminer le matériel physique sur lequel cette taille est déployée, interrogez le matériel virtuel à partir de la machine virtuelle. Voici des exemples de cas d’usage : serveurs de développement et de test, serveurs web à faible trafic, bases de données de petite et moyenne tailles, preuves de concept et dépôts de code.

  > [!NOTE]
  > La mise hors service des machines virtuelles A8-A11 est planifiée pour le mois de mars 2021. Pour plus d’informations, voir le [Guide de migration HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

- Les machines virtuelles [modulables de la série B](sizes-b-series-burstable.md) sont idéales pour les charges de travail ne nécessitant pas en permanence les performances complètes du processeur, comme les serveurs web, les petites bases de données et les environnements de test et de développement. Ces charges de travail ont généralement des exigences modulables en termes de performances. La série B permet à ces clients d’acheter une taille de machine virtuelle aux performances de base sensibles au prix qui permet à l’instance de machine virtuelle de générer des crédits quand la machine virtuelle n’utilise pas la totalité de ses performances de base. Dès que la machine virtuelle a cumulé des crédits, celle-ci peut étendre ses performances en utilisant jusqu’à 100 % du processeur virtuel lorsque l’application requiert des performances de processeur plus élevées.

- Les [séries Dav4 et Dasv4](dav4-dasv4-series.md) sont de nouvelles tailles qui utilisent le processeur EPYC<sup>TM</sup> 7452 2,35 GHz d’AMD dans une configuration multithread, avec un cache L3 allant jusqu’à 256 Mo, dont 8 Mo sont dédiés tous les 8 cœurs, ce qui offre aux clients davantage d’options pour exécuter leurs charges de travail à usage général. Elles ont les mêmes configurations de mémoire et de disque que les séries D et Dsv3.

- La [série DCv2](dcv2-series.md) permet de protéger la confidentialité et l’intégrité de vos données et de votre code lors de leur traitement dans le cloud public. Ces machines bénéficient du processeur Intel XEON E-2288G de dernière génération avec la technologie SGX. Avec Intel Turbo Boost Technology, ces machines peuvent atteindre 5,0 GHz. Les instances de la série DCv2 permettent aux clients de créer des applications sécurisées basées sur enclave pour protéger leur code et leurs données en cours d’utilisation.

- Les machines virtuelles de la [série Dv2 et Dsv2](dv2-dsv2-series.md), suite de la série D d’origine, présentent un processeur plus performant et une configuration avec un ratio processeur/mémoire optimal, ce qui en fait la série idéale pour la plupart des charges de travail de production. La série Dv2 est environ 35 % plus rapide que la série D. La série Dv2 s’exécute sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) avec la technologie Intel Turbo Boost 2.0. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.

- Les machines virtuelles de la [série Dv3 et Dsv3](dv3-dsv3-series.md) s’exécutent sur les processeurs Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) dans une configuration hyper-thread, ce qui offre une meilleure valeur pour la plupart des charges de travail à usage général. La mémoire a été étendue (d’environ 3,5 Gio/vCPU à 4 Gio/vCPU) et les limites de disque et de réseau ont été ajustées au niveau du cœur pour s’aligner sur la transition vers l’hyperthreading. La série Dv3 n’offre plus les tailles de machine virtuelle à mémoire élevée de la série D/Dv2, qui ont été déplacées vers les séries [Ev3 et Esv3](ev3-esv3-series.md) à mémoire optimisée.

- Les machines virtuelles des [séries Ddv4 et Ddsv4](ddv4-ddsv4-series.md) s'exécutent sur des processeurs Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) de 2e génération, ce qui apporte davantage de valeur ajoutée à la plupart des charges de travail à usage général. Caractéristiques : vitesse d'horloge Turbo continue de 3,4 GHz, [technologie Intel&reg; Turbo Boost 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [technologie Intel&reg; Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) et [Intel&reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). 


## <a name="other-sizes"></a>Autres tailles

- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
