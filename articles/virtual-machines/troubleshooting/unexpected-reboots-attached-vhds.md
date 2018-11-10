---
title: Résoudre les redémarrages inattendus de machines virtuelles avec des VHD joints sur des machines virtuelles Azure | Microsoft Docs
description: Comment résoudre les redémarrages inattendus des machines virtuelles.
keywords: connexion ssh refusée, erreur ssh, ssh azure, échec de connexion SSH
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 6273087e28be8b784168d5808918d04d0e4cf303
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748151"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Résoudre les problèmes de redémarrage inattendu de machines virtuelles avec VHD joints

Si une machine virtuelle Azure est associée à un grand nombre de VHD dans le même compte de stockage, vous risquez de dépasser les cibles d’extensibilité pour un même compte de stockage, ce qui provoquera un redémarrage inattendu de la machine virtuelle. Consultez les métriques par minute pour le compte de stockage (**TotalRequests**/**TotalIngress**/**TotalEgress**) et vérifiez la présence de pics dépassant les cibles d’évolutivité pour un compte de stockage. La section [Les métriques indiquent une augmentation de la valeur PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) vous aidera à déterminer si une limitation a été appliquée à votre compte de stockage.

En général, chaque opération d’entrée ou de sortie individuelle sur un disque dur virtuel à partir d’une machine virtuelle traduit les opérations **Get Page** ou **Put Page** sur l’objet blob de la page sous-jacente. Vous pouvez donc utiliser les opérations d’E/S par seconde (IOPS) estimées pour votre environnement pour déterminer le nombre de disques durs virtuels que vous pouvez avoir dans un compte de stockage unique, sur base du comportement spécifique de votre application. Microsoft recommande qu’un compte de stockage ne comporte pas plus de 40 disques. Pour obtenir des détails sur les cibles d’extensibilité des comptes de stockage, en particulier le taux de demandes total et la bande passante totale pour le type de compte de stockage que vous utilisez, consultez [Cibles de performance et d’extensibilité Stockage Azure](../../storage/common/storage-scalability-targets.md).

Si vous dépassez les cibles d’évolutivité pour votre compte de stockage, placez vos VHD dans plusieurs comptes de stockage afin de réduire l’activité dans chaque compte individuel.
