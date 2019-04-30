---
title: Prise en charge pour l’utilisation d’Azure Site Recovery avec sauvegarde Azure | Microsoft Docs
description: Fournit une vue d’ensemble de la façon dont Azure Site Recovery et la sauvegarde Azure peuvent être utilisés ensemble.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035761"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Prise en charge pour l’utilisation de la récupération de Site avec sauvegarde Azure

Cet article résume la prise en charge pour l’utilisation de la [service Site Recovery](site-recovery-overview.md) conjointement avec la [service Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

**Action** | **Prise en charge de la récupération de site** | **Détails**
--- | --- | ---
**Déployer les services** | Pris en charge | Les services sont interopérables et peuvent être configurés conjointement.
**Sauvegarde/restauration de fichiers** | Pris en charge | Lors de la sauvegarde et réplication sont activées pour une machine virtuelle et les sauvegardes sont effectuées, il n’existe aucun problème de restauration des fichiers sur le côté de la source des machines virtuelles, ou groupe de machines virtuelles. Réplication continue comme d’habitude avec aucune modification de l’intégrité de la réplication.
**Disque de sauvegarde/restauration** | Aucune prise en charge en cours | Si vous restaurez un disque sauvegardé, vous devez désactiver et réactiver la réplication pour la machine virtuelle à nouveau.
**Sauvegarde/restauration de machine virtuelle** | Aucune prise en charge en cours | Si vous sauvegardez ou restaurez une machine virtuelle ou un groupe de machines virtuelles, vous devez désactiver et réactiver la réplication pour la machine virtuelle.  


