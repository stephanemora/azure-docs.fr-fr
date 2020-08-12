---
title: Considérations sur les performances pour Azure NetApp Files | Microsoft Docs
description: Découvrez les performances d’Azure NetApp Files, notamment la relation de quota et la limite de débit, et la façon d’augmenter ou de diminuer de manière dynamique le quota de volume.
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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 5f88b4755c7b4c0b20f27065cf9de2351251bc1c
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513874"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Considérations sur les performances pour Azure NetApp Files

La [limite de débit](azure-netapp-files-service-levels.md) d’un volume est déterminée par une combinaison de quota attribué au volume et par le niveau de service sélectionné. Lorsque vous effectuez des plans de performances concernant Azure NetApp Files, vous devez comprendre plusieurs considérations. 

## <a name="quota-and-throughput"></a>Quota et débit  

La limite de débit n’est qu’un seul déterminant des performances réalisées.  

Les considérations sur les performances de stockage typiques, dont les mélanges de lecture et d’écriture, la taille du transfert, les modèles aléatoires ou séquentiels, et plusieurs autres facteurs contribueront aux performances totales offertes.  

Le débit empirique maximal ayant été observé lors des tests est de 4 500 Mio/s.  Sur le niveau de stockage Premium, un quota de 70,31 Tio de volume configure une limite de débit suffisamment élevée pour atteindre ce niveau de performances.  

Si vous envisagez d’affecter des montants de quota supérieurs à 70,31 Tio, un quota supplémentaire peut être affecté à un volume pour stocker des données supplémentaires. Toutefois, le quota ajouté n’entraîne pas une augmentation du débit réel.  

## <a name="overprovisioning-the-volume-quota"></a>Sur-approvisionnement du quota de volume

Si les performances d’une charge de travail sont liée par limite de débit, il est possible de sur-approvisionner le quota de volume pour définir un niveau plus élevé de débit et obtenir de meilleures performances.  

Par exemple, si un volume dans le niveau de stockage Premium a seulement 500 Gio de données, mais requiert 128 Mio/s de débit, vous pouvez définir le quota à 2 Tio afin que le niveau de débit soit défini en conséquence (64 Mio/s par To * 2 Tio = 128 Mio/s).  

Si vous sur-appprovisionnez systématiquement un volume pour atteindre un débit plus élevé, envisagez plutôt d’utiliser un niveau de service supérieur.  Dans l’exemple ci-dessus, vous pouvez obtenir la même limite de débit avec moitié moins de quota de volume en utilisant le niveau de stockage Ultra à la place (128 Mio/s par Tio * 1 Tio = 128 Mio/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Augmentation ou diminution dynamique du volume de quota

Si vos exigences de performances sont temporaires par nature, ou si vous avez augmenté les besoins de performances pour une période fixe, vous pouvez dynamiquement augmenter ou diminuer le quota de volume pour ajuster instantanément la limite de débit.  Tenez compte des points suivants : 

* Le quota de volume peut être augmenté ou diminué sans avoir à suspendre l’E/S, et l’accès au volume n’est pas interrompu ni affecté.  

    Vous pouvez ajuster le quota pendant une transaction active d’E/S par rapport à un volume.  Remarque : ce quota de volume ne peut jamais être diminué sous la quantité de données logiques stockées dans le volume.

* Lorsque le quota de volume est modifié, la modification correspondante de limite de débit est presque instantanée. 

    La modification n’interrompt ni n’affecte l’accès au volume ou l’E/S.  

* L’ajustement du quota de volume nécessite une modification de la taille du pool de capacité.  

    La taille du pool de capacité peut être ajustée dynamiquement et sans affecter la disponibilité des volumes ou des E/S.

## <a name="next-steps"></a>Étapes suivantes

- [Niveaux de service pour Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Test d’évaluation des performances pour Linux](performance-benchmarks-linux.md)