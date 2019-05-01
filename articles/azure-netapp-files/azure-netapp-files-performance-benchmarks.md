---
title: Tests de performances pour les fichiers de NetApp Azure | Microsoft Docs
description: Décrit les résultats des tests d’évaluation de performances pour les fichiers de NetApp Azure au niveau du volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870881"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Tests de performances pour les fichiers de NetApp Azure

Cet article décrit les résultats des tests d’évaluation de performances pour les fichiers de NetApp Azure au niveau du volume. 

## <a name="sample-application-used-for-the-tests"></a>Exemple d’application utilisé pour les tests

Tests de performances ont été exécutés avec un exemple d’application à l’aide de fichiers NetApp de Azure. L’application présente les caractéristiques suivantes : 

* Une application Linux conçue pour le cloud
* Peut augmenter proportionnellement au nombre avec ajout machines virtuelles (VM) pour augmenter la puissance de calcul en fonction des besoins
* Nécessite un accès rapide de data lake
* Modèles d’e/s qui sont parfois aléatoire et séquentiel parfois 
    * Un modèle aléatoire nécessite une faible latence pour de grandes quantités d’e/s. 
    * Un modèle séquentiel requiert des grandes quantités de bande passante. 

## <a name="about-the-workload-generator"></a>Sur le Générateur de charge de travail

Les résultats proviennent de fichiers de synthèse Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) est un utilitaire de ligne de commande qui génère des charges de travail d’e/s de disque pour la validation des performances de stockage. La configuration du client-serveur utilisée est évolutive.  Il comprend un seul master mixte/client et client dédiés 14 machines virtuelles.

## <a name="about-the-tests"></a>Sur les tests

Les tests ont été conçus pour identifier les limites susceptibles de présenter l’exemple d’application et le temps de réponse que courbes jusqu’aux limites.  

Les tests suivants ont été exécutés : 

* Lire des 8 Kio aléatoire de 100 %
* 100 % 8 écriture aléatoires Kio
* Lire des 64 Kio séquentiel de 100 %
* 100 % 64 Kio séquentiel en écriture
* 50 % 64 Kio séquentielle de lecture, écriture séquentielle de 50 % 64 Kio
* Lire des 8 Kio aléatoire de 50 %, 50 % 8 écriture aléatoires Kio

## <a name="bandwidth"></a>Bande passante

Les fichiers NetApp Azure propose plusieurs [les niveaux de service](azure-netapp-files-service-levels.md). Chaque niveau de service offre une quantité différente de la bande passante par To de capacité déployée (quota de volume). La limite de bande passante pour un volume est configurée selon la combinaison du niveau de service et le quota de volume. Notez que la limite de bande passante est le seul facteur dans la détermination de la quantité réelle de débit qui est réalisée.  

Actuellement, le MiB 4 500 est le débit le plus élevé qui a été effectuée par une charge de travail par rapport à un seul volume dans le test.  Avec le niveau de service Premium, un quota de volume de TIO 70.31 configurera une bande passante suffisante pour réaliser ce débit par le calcul ci-dessous : 

![Formule de la bande passante](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Niveau de service et de quota](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Les charges de travail gourmandes en débit

Le test de débit utilisé Vdbench et une combinaison de machines virtuelles de stockage 12xD32s V3. Le volume d’exemple dans le test obtenu les chiffres de débit suivants :

![Test de débit](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>E/S intensives de charges de travail

Le test d’e/s utilisé Vdbench et une combinaison de machines virtuelles de stockage 12xD32s V3. Le volume d’exemple dans le test obtenu les numéros d’e/s suivants :

![Test d’e/s](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latence

La distance entre les machines virtuelles de test et le volume de fichiers de NetApp Azure a un impact sur les performances d’e/s.  Le tableau ci-dessous compare les IOPS par rapport aux courbes de latence pour deux différents ensembles de machines virtuelles.  Un ensemble de machines virtuelles est proche de fichiers NetApp de Azure et l’autre ensemble est absent supplémentaire.  Notez que la latence plus élevée pour l’ensemble supplémentaire de machines virtuelles a un impact sur la quantité d’e/s obtenue à un niveau donné de parallélisme.  Malgré tout, les lectures en comparaison avec un volume peuvent dépasser 300 000 e/s comme illustré ci-dessous : 

![Étude de la latence](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Résumé

Charges de travail sensibles à la latence (bases de données) peuvent avoir un temps de réponse une milliseconde. Les performances transactionnelle peuvent être supérieure à 300 Ko IOPS d’un seul volume.

Débit des applications (pour la diffusion en continu et d’acquisition d’images) peut avoir 4.5GiB / s de débit.
