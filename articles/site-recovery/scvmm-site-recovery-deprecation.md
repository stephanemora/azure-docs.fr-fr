---
title: Dépréciation à venir de la récupération d’urgence entre sites appartenant à un client avec Hyper-V et entre sites gérés par SCVMM vers Azure | Microsoft Docs
description: Détails sur la dépréciation à venir de la récupération d’urgence entre sites appartenant à un client avec Hyper-V et entre sites gérés par SCVMM vers Azure et alternatives
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/13/2019
ms.author: rajanaki
ms.openlocfilehash: 68881d57f251d2f26996b2a837780106326ec1d3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492284"
---
# <a name="upcoming-deprecation-of-dr-between-customer-owned-sites-using-hyper-v-and-between-sites-managed-by-scvmm-to-azure"></a>Dépréciation à venir de la récupération d’urgence entre sites appartenant à un client avec Hyper-V et entre sites gérés par SCVMM vers Azure

Cet article décrit le plan de dépréciation à venir, les implications correspondantes et les options alternatives disponibles pour les clients pour les scénarios suivants qui étaient pris en charge par Azure Site Recovery. 

- La récupération d'urgence entre machines virtuelles Hyper-V gérées par SCVMM entre des sites appartenant à un client 
- La récupération d'urgence de machines virtuelles Hyper-V gérées par SCVMM vers Azure

> [!IMPORTANT]
> Ces scénarios sont actuellement marqués pour dépréciation et les clients doivent prendre des mesures correctives au plus tôt pour éviter toute interruption dans leur environnement. 
 

## <a name="what-changes-should-you-expect"></a>À quelles modifications devez-vous vous attendre ?

- À compter de novembre 2019, aucune nouvelle inscription d’utilisateur n’est autorisée pour ces scénarios. **Les opérations de réplication et de gestion existantes**, y compris le basculement, le basculement de test, la surveillance, etc. **ne seront pas affectées**.

- Une fois les scénarios dépréciés, cela aura les conséquences suivantes, sauf si le client suit les étapes recommandées.

    - Récupération d'urgence entre machines virtuelles Hyper-V gérées par SCVMM entre des sites appartenant à un client : Les réplications continueront à fonctionner, car la capacité sous-jacente de la réplication Hyper-V continue à fonctionner, mais les clients ne pourront pas afficher, gérer ou effectuer des opérations de récupération d’urgence via l’expérience Azure Site Recovery dans le portail Azure. 
    - Récupération d'urgence de machines virtuelles Hyper-V gérées par SCVMM vers Azure : Les réplications existantes seront interrompues et les clients ne seront pas en mesure d’afficher, de gérer ou d’effectuer des opérations de récupération d’urgence via Azure Site Recovery


## <a name="recommended-actions-to-be-taken"></a>Actions recommandées à entreprendre

Vous trouverez ci-dessous les options dont dispose le client pour s’assurer que sa stratégie de récupération d’urgence ne sera pas affectée lorsque le scénario sera déprécié. 

- Choisissez de [commencer à utiliser Azure comme cible de récupération d’urgence pour les machines virtuelles sur les hôtes Hyper-V](hyper-v-azure-tutorial.md).

> [!IMPORTANT]
> Notez que votre environnement local peut toujours avoir SCVMMM, mais que vous configurerez ASR avec des références uniquement vers les hôtes Hyper-V.

- Choisissez de continuer la réplication de site à site, mais en utilisant la [solution de réplication Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica) sous-jacente ; mais vous ne serez alors pas en mesure de gérer les configurations de récupération d’urgence à l’aide d’Azure Site Recovery dans le portail Azure. 


## <a name="next-steps"></a>Étapes suivantes
Planifiez la dépréciation et choisissez l’alternative qui convient le mieux à votre infrastructure et votre entreprise. Si vous avez des questions sur ce sujet, merci de contacter le Support Microsoft

