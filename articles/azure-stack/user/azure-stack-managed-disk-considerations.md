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
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 05efa7eaf6d95cbf63efd17b00d321d8c8509f28
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246777"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Managed Disks Azure Stack : différences et considérations

Cet article résume les différences connues entre les services [Managed Disks Azure Stack](azure-stack-manage-vm-disks.md) et [Managed Disks pour Azure](../../virtual-machines/windows/managed-disks-overview.md). Pour en savoir plus sur les principales différences entre Azure Stack et Azure, consultez l’article [Principales considérations](azure-stack-considerations.md).

Le service Managed Disks simplifie la gestion des disques des machines virtuelles Azure IaaS en gérant les [comptes de stockage](../azure-stack-manage-storage-accounts.md) associés aux disques de machines virtuelles.

> [!Note]  
> Le service Managed Disks sur Azure Stack est disponible depuis la mise à jour 1808. Il activé par défaut lors de la création de machines virtuelles via le portail Azure Stack depuis la mise à jour 1811.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Aide-mémoire : Différences entre les disques managés

| Fonctionnalité | Azure (global) | Azure Stack |
| --- | --- | --- |
|Chiffrement des données au repos |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE)     |Chiffrement AES 128 bits BitLocker      |
|Image          | Prise en charge d’image personnalisée gérée |Pas encore pris en charge|
|Options de sauvegarde |Prise en charge du service Sauvegarde Azure |Pas encore pris en charge |
|Options de récupération d’urgence |Prise en charge d’Azure Site Recovery |Pas encore pris en charge|
|Types de disque     |SSD Premium, SSD Standard (préversion) et HDD Standard |SSD Premium, HDD Standard |
|Disques Premium  |Entièrement pris en charge |Peut être approvisionné, mais sans limite ni garantie de performances  |
|IOPS des disques Premium  |Varie selon la taille du disque  |2 300 IOPS par disque |
|Débit des disques Premium |Varie selon la taille du disque |145 Mo/seconde par disque |
|Taille du disque  |Disque Premium Azure : P4 (32 Gio) à P80 (32 Tio)<br>Disque SSD Standard Azure : E10 (128 Gio) à E80 (32 Tio)<br>Disque HDD Standard Azure : S4 (32 Gio) à S80 (32 Tio) |M4 : 32 Gio<br>M6 : 64 Gio<br>M10 : 128 Go<br>M15 : 256 Gio<br>M20 : 512 Go<br>M30 : 1 024 Gio |
|Copie d’un instantané de disques|Instantané de disques managés Azure attaché à une machine virtuelle en cours d’exécution pris en charge|Pas encore pris en charge |
|Analyse des performances des disques |Agréger les métriques et les métriques par disque prises en charge |Pas encore pris en charge |
|Migration      |Fournir l’outil pour migrer à partir de machines virtuelles Azure Resource Manager non managées existantes sans devoir recréer de machine virtuelle  |Pas encore pris en charge |

> [!NOTE]  
> Les IOPS et le débit de disques managés dans Azure Stack sont exprimés en nombre plafond plutôt qu’en nombre provisionné, qui peut être affecté par le matériel et les charges de travail en cours d’exécution dans Azure Stack.

## <a name="metrics"></a>Mesures

Il existe également des différences sur le plan des métriques de stockage :

- Avec Azure Stack, les données de transaction dans les métriques de stockage ne font pas la distinction entre les bandes passantes réseau interne et externe.
- Les données de transaction d’Azure Stack dans les métriques de stockage n’incluent pas l’accès des machines virtuelles aux disques montés.

## <a name="api-versions"></a>Versions d’API

Les service Managed Disks d’Azure Stack prend en charge les versions d’API suivantes :

- 2017-03-30

## <a name="configuration"></a>Configuration

Après avoir appliqué la mise à jour 1808 ou une version ultérieure, vous devez appliquer la configuration suivante avant d’utiliser Managed Disks :

- Si un abonnement a été créé avant la mise à jour 1808, procédez comme suit pour mettre à jour l’abonnement. Sinon, le déploiement de machines virtuelles dans cet abonnement peut échouer avec un message d’erreur « Erreur interne dans le gestionnaire de disque ».
   1. Dans le portail locataire, accédez à **Abonnements** et recherchez l’abonnement. Cliquez sur **Fournisseurs de ressources**, sur **Microsoft.Compute**, puis sur **Réinscrire**.
   2. Sous le même abonnement, accédez à **Contrôle d’accès (IAM)** et vérifiez que l’élément **Azure Stack – Managed Disks** est répertorié.
- Si vous utilisez un environnement mutualisé, demandez à votre opérateur cloud (dans votre organisation ou chez votre fournisseur de services) de reconfigurer chacun de vos annuaires invités conformément aux étapes décrites dans [cet article](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). Sinon, le déploiement de machines virtuelles dans un abonnement associé à cet annuaire invité peut échouer avec un message d’erreur « Erreur interne dans le gestionnaire de disque ».


## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les machines virtuelles Azure Stack](azure-stack-compute-overview.md)
