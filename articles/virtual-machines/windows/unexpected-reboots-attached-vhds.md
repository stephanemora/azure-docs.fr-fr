---
title: Résoudre les problèmes de redémarrage inattendu de machines virtuelles Windows Azure avec VHD joints | Microsoft Docs
description: Guide pratique pour résoudre les problèmes de redémarrage inattendu de machines virtuelles Windows.
keywords: connexion ssh refusée, erreur ssh, ssh azure, échec de connexion SSH
services: virtual-machines-windows
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 5c16c2d08a1a317f8f718330ff6c86a452ac1815
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362599"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Résoudre les problèmes de redémarrage inattendu de machines virtuelles avec VHD joints

Si une machine virtuelle Azure est associée à un grand nombre de VHD dans le même compte de stockage, vous risquez de dépasser les cibles d’extensibilité pour un même compte de stockage, ce qui provoquera un redémarrage inattendu de la machine virtuelle. Consultez les métriques par minute pour le compte de stockage (**TotalRequests**/**TotalIngress**/**TotalEgress**) et vérifiez la présence de pics dépassant les cibles d’évolutivité pour un compte de stockage. La section [Les métriques indiquent une augmentation de la valeur PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) vous aidera à déterminer si une limitation a été appliquée à votre compte de stockage.

En général, chaque opération d’entrée ou de sortie individuelle sur un disque dur virtuel à partir d’une machine virtuelle traduit les opérations **Get Page** ou **Put Page** sur l’objet blob de la page sous-jacente. Vous pouvez donc utiliser les opérations d’E/S par seconde (IOPS) estimées pour votre environnement pour déterminer le nombre de disques durs virtuels que vous pouvez avoir dans un compte de stockage unique, sur base du comportement spécifique de votre application. Microsoft recommande qu’un compte de stockage ne comporte pas plus de 40 disques. Pour obtenir des détails sur les cibles d’extensibilité des comptes de stockage, en particulier le taux de demandes total et la bande passante totale pour le type de compte de stockage que vous utilisez, consultez [Cibles de performance et d’extensibilité Stockage Azure](../../storage/common/storage-scalability-targets.md).

Si vous dépassez les cibles d’évolutivité pour votre compte de stockage, placez vos VHD dans plusieurs comptes de stockage afin de réduire l’activité dans chaque compte individuel.
