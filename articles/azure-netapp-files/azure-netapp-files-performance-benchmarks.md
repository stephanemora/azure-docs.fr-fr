---
title: Tests de performances pour Azure NetApp Files | Microsoft Docs
description: Décrit les résultats des tests d’évaluation des performances pour Azure NetApp Files au niveau du volume.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870881"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Tests de performances pour Azure NetApp Files

Cet article décrit les résultats des tests d’évaluation des performances pour Azure NetApp Files au niveau du volume. 

## <a name="sample-application-used-for-the-tests"></a>Exemple d’application utilisée pour les tests

Les tests de performances ont été exécutés avec un exemple d’application utilisant Azure NetApp Files. L’application présente les caractéristiques suivantes : 

* Application Linux conçue pour le cloud.
* Application capable d’adapter son échelle linéairement au nombre de machines virtuelles ajoutées afin d’augmenter la puissance de calcul si nécessaire.
* Nécessite un accès rapide au lac de données.
* Intègre des modèles d’E/S qui sont parfois aléatoires, parfois séquentiels. 
    * Un modèle aléatoire nécessite une faible latence pour de grandes quantités d’E/S. 
    * Un modèle séquentiel nécessite une bande passante conséquente. 

## <a name="about-the-workload-generator"></a>À propos du générateur de charge de travail

Les résultats sont extraits de fichiers de synthèse Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) est un utilitaire de ligne de commande qui génère des charges de travail d’E/S de disque pour la validation des performances de stockage. La configuration client-serveur utilisée est évolutive.  Elle comprend un ordinateur maître/client mixte et 14 machines virtuelles clients dédiées.

## <a name="about-the-tests"></a>À propos des tests

Les tests ont été conçus pour identifier les limites potentielles de l’exemple d’application et la courbe de temps de réponse jusqu’aux limites.  

Les tests exécutés sont les suivants : 

* Lecture aléatoire de 8 Kio 100 %
* Écriture aléatoire de 8 Kio 100 %
* Lecture séquentielle de 64 Kio 100 %
* Écriture séquentielle de 64 Kio 100 %
* Lecture séquentielle de 64 Kio 50 %, écriture séquentielle de 64 Kio 50 %
* Lecture aléatoire de 8 Kio 50 %, écriture aléatoire de 8 Kio 50 %

## <a name="bandwidth"></a>Bande passante

Azure NetApp Files propose plusieurs [niveaux de service](azure-netapp-files-service-levels.md). Chaque niveau de service offre une quantité distincte de bande passante par Tio de capacité approvisionnée (quota de volume). La limite de bande passante pour un volume est approvisionnée en fonction de la combinaison du niveau de service et du quota de volume. Notez que la limite de bande passante est le seul facteur déterminant de la quantité réelle du débit réalisé.  

Actuellement, le 4 500 Mio est le débit le plus élevé atteint par une charge de travail sur un seul volume dans le cadre d’un test.  Avec le niveau de service Premium, un quota de volume de 70,31 Tio approvisionnera une bande passante suffisante pour réaliser ce débit en vertu du calcul ci-dessous : 

![Formule de bande passante](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Niveau de quota et de service](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Charges de travail intensives en débit

Le test de débit a utilisé Vdbench et une combinaison de machines virtuelles de stockage 12xD32s V3. L’exemple de volume dans le test a obtenu les chiffres de débit suivants :

![Test de débit](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Charges de travail intensives en E/S

Le test d’E/S a utilisé Vdbench et une combinaison de machines virtuelles de stockage 12xD32s V3. L’exemple de volume dans le test a obtenu les chiffres d’E/S suivants :

![Test d’E/S](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latence

La distance entre les machines virtuelles de test et le volume Azure NetApp Files a une incidence sur les performances d’E/S.  Le graphique ci-dessous compare les IOPS aux courbes de réponse de latence pour deux groupes de machines virtuelles.  Un groupe de machines virtuelles est proche d’Azure NetApp Files et l’autre en est plus éloigné.  Notez que la latence accrue pour le groupe éloigné de machines virtuelles a une incidence sur la quantité d’IOPS obtenue à un niveau donné de parallélisme.  Malgré tout, les lectures sur un volume peuvent dépasser 300 000 E/S, comme illustré ci-dessous : 

![Étude de latence](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Résumé

Les charges de travail sensibles à la latence (bases de données) peuvent avoir un temps de réponse d’une milliseconde. Les performances transactionnelle peuvent dépasser 300 000 IOPS pour un seul volume.

Les applications sensibles au débit (pour la diffusion en continu et l’acquisition d’images) peuvent atteindre un débit de 4,5 Gio/s.
