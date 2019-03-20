---
title: Fichier Include
description: Fichier Include
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553681"
---
Les limites suivantes s’appliquent à la sauvegarde Azure.

| **Limite** | **Par défaut** |
| --- | --- |
| Serveurs ou machines virtuelles qui peuvent être inscrits dans un coffre. | Windows Server/Windows Client/System Center Data Protection Manager : 50. <br/><br/> Machines virtuelles IaaS : 1,000.  |
| Taille de la source de données dans le stockage d’archivage. |54,400 Go maximum. La limite ne s’applique pas à la sauvegarde d’IaaS VM. |
| Coffres de sauvegarde dans un abonnement Azure. |500 coffres par région. |
| Planifier des sauvegardes quotidiennes. |Windows Server/Client : Trois par jour.<br/> System Center DPM : Deux un jour. <br/> Machines virtuelles IaaS : Une fois par jour.  |
| Disques de données attachés à une machine virtuelle Azure pour la sauvegarde. | 16 |
| Disque de données attaché à la machine virtuelle Azure pour la sauvegarde.| 4 095 GO|
