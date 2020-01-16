---
title: Demandes d’augmentation des quotas de processeurs virtuels pour Azure Resource Manager | Microsoft Docs
description: Demandes d’augmentation des quotas de processeurs virtuels pour Azure Resource Manager
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c3248b10b4ad343e8776056d42ec153130f0061f
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898801"
---
# <a name="quota-increase-requests"></a>Demandes d’augmentation de quota

Les quotas de processeurs virtuels de Resource Manager pour les machines virtuelles et les groupes de machines virtuelles identiques sont appliqués sur deux niveaux pour chaque abonnement, dans chaque région. 

Le premier niveau est la limite de total de processeurs virtuels régionaux (sur toutes les séries de machines virtuelles) et le deuxième niveau est la limite de processeurs virtuels par série de machines virtuelles (par exemple, les processeurs virtuels de série D). Chaque fois qu’une nouvelle machine virtuelle est déployée, la somme des processeurs virtuels nouveaux et existants pour cette série de machines virtuelles ne doit pas dépasser le quota de processeurs virtuels approuvé pour cette série de machines virtuelles particulière. En outre, le nombre total de processeurs virtuels nouveaux et existants déployés sur toutes les séries de machines virtuelles ne doit pas dépasser le quota de processeurs virtuels régionaux total approuvé pour l’abonnement. Si l’un de ces quotas est dépassé, le déploiement des machines virtuelles n’est pas autorisé.
Vous pouvez demander une augmentation de la limite de quota de processeurs virtuels pour une série de machines virtuelles à partir du portail Azure. Une augmentation du quota de série de machines virtuelles augmente automatiquement la limite totale de processeurs virtuels régionaux du même montant. 

Lorsqu’un nouvel abonnement est créé, la valeur par défaut du total des processeurs virtuels régionaux peut ne pas être égale à la somme des quotas de processeurs virtuels par défaut pour toutes les séries de machines virtuelles individuelles. Cela peut conduire à un abonnement avec un quota suffisant pour chaque série de machines virtuelles individuelle que vous souhaitez déployer, mais insuffisant pour le total de processeurs virtuels régionaux pour tous les déploiements. Dans ce cas, vous devez envoyer une demande d’augmentation de la limite totale de processeurs virtuels régionaux explicitement. La limite du nombre total de processeurs virtuels régionaux ne peut pas dépasser la somme des quotas approuvés sur toutes les séries de machines virtuelles pour la région.

Apprenez-en davantage sur les quotas sur la page [Quotas de processeurs virtuels pour les machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) et la page [Limites du service et de l’abonnement Azure](https://aka.ms/quotalimits). 

