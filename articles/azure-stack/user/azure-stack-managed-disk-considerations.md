---
title: Différences et considérations relatives aux disques managés dans Azure Stack | Microsoft Docs
description: Découvrez les différences et considérations à prendre en compte lors de l’utilisation de Managed Disks dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: 2870bf8911c48ac4cbb442278f172b37a474152b
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078051"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Service Managed Disks d’Azure Stack : différences et considérations
Cet article résume les différences connues entre les services Managed Disks d’Azure Stack et Managed Disks pour Azure. Pour en savoir plus sur les principales différences entre Azure Stack et Azure, consultez l’article [Principales considérations](azure-stack-considerations.md).

Le service Managed Disks simplifie la gestion des disques des machines virtuelles Azure IaaS en gérant les [comptes de stockage](/azure/azure-stack/azure-stack-manage-storage-accounts) associés aux disques de machines virtuelles.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Aide-mémoire : différences de disques managés

| Fonctionnalité | Azure (global) | Azure Stack |
| --- | --- | --- |
|Chiffrement des données au repos |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE)     |Chiffrement AES 128 bits BitLocker      |
|Image          | Prise en charge d’image personnalisée gérée |Pas encore pris en charge|
|Options de sauvegarde |Prise en charge du service Sauvegarde Azure |Pas encore pris en charge |
|Options de récupération d’urgence |Prise en charge d’Azure Site Recovery |Pas encore pris en charge|
|Types de disques     |SSD Premium, SSD Standard (préversion) et HDD Standard |SSD Premium, HDD Standard |
|Disques Premium  |Entièrement pris en charge |Peut être approvisionné, mais sans limite ni garantie de performances  |
|IOPS des disques Premium  |Varie selon la taille du disque  |2 300 IOPS par disque |
|Débit des disques Premium |Varie selon la taille du disque |145 Mo/seconde par disque |
|Taille maximale de disque  |4 To       |1 To       |
|Analyse des performances des disques |Agréger les métriques et les métriques par disque prises en charge |Pas encore pris en charge |
|Migration      |Fournir l’outil pour migrer à partir de machines virtuelles Azure Resource Manager non managées existantes sans devoir recréer de machine virtuelle  |Pas encore pris en charge |


## <a name="metrics"></a>Mesures
Il existe également des différences sur le plan des métriques de stockage :
- Avec Azure Stack, les données de transaction dans les métriques de stockage ne font pas la distinction entre les bandes passantes réseau interne et externe.
- Les données de transaction d’Azure Stack dans les métriques de stockage n’incluent l’accès des machines virtuelles aux disques montés.


## <a name="api-versions"></a>Versions d’API
Les service Managed Disks d’Azure Stack prend en charge les versions d’API suivantes :
- 2017-03-30


## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur les machines virtuelles Azure Stack](azure-stack-compute-overview.md)
