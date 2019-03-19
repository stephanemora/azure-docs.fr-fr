---
title: Azure Site Recovery - sauvegarde de l’interopérabilité | Microsoft Docs
description: Fournit une vue d’ensemble de la façon dont Azure Site Recovery et la sauvegarde Azure peuvent être utilisés ensemble.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/26/2019
ms.author: sideeksh
ms.openlocfilehash: 6658ab8c967c70ac1deaeba3d1dfeac602515591
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731872"
---
# <a name="about-site-recovery-and-backup-interoperability"></a>Sur la récupération de Site et de l’interopérabilité de sauvegarde

Cet article fournit des conseils pour correctement à l’aide de la sauvegarde des machines virtuelles IaaS Azure et machine virtuelle Azure la récupération d’urgence.

## <a name="azure-backup"></a>Sauvegarde Azure

Sauvegarde Azure permet de protéger les données pour les serveurs locaux, des machines virtuelles, charges de travail virtualisées, serveurs SQL, des serveurs SharePoint et bien plus encore. Azure Site Recovery orchestre et gère la récupération d’urgence pour les machines virtuelles Azure, machines virtuelles locales et des serveurs physiques.

## <a name="azure-site-recovery"></a>Azure Site Recovery

Il est possible de configurer la sauvegarde Azure et Azure Site Recovery sur une machine virtuelle ou un groupe de machines virtuelles. Ces deux produits sont interopérables. Voici quelques scénarios où l’interopérabilité entre la sauvegarde et la récupération de Site Azure devient importante :

### <a name="file-backuprestore"></a>Sauvegarde/restauration de fichiers

Si la sauvegarde et la réplication sont tous deux activés, et une sauvegarde est effectuée, il n’existe aucun problème avec la restauration des fichiers sur la machine virtuelle côté source ou le groupe de machines virtuelles. Réplication continue comme d’habitude avec aucune modification de l’intégrité de la réplication.

### <a name="disk-backuprestore"></a>Disque de sauvegarde/restauration

Si vous restaurez le disque à partir de la sauvegarde de protection de la machine virtuelle doit être activée à nouveau.

### <a name="vm-backuprestore"></a>Sauvegarde/restauration de machine virtuelle

Sauvegarde et restauration d’une machine virtuelle ou un groupe de machines virtuelles n’est pas pris en charge. Pour que cela fonctionne, la protection doit être réactivé.

**Scénario** | **Prise en charge par Azure Site Recovery ?** | **Solution de contournement, le cas échéant**  
--- | --- | ---
Sauvegarde de fichiers/dossiers | Oui | Non applicable
Sauvegarde sur disque | Pas actuellement. | Désactiver et activer la Protection
Sauvegarde de machine virtuelle | Non  | Désactiver et activer la Protection
