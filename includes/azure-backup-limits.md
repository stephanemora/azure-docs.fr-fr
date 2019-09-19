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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67177463"
---
Les limites suivantes s’appliquent à la sauvegarde Azure.

| **Limite** | **Par défaut** |
| --- | --- |
| Serveurs ou machines pouvant être inscrites dans un coffre. | Windows Server/Windows Client/System Center Data Protection Manager : 50. <br/><br/> Machines virtuelles IaaS : 1 000.  |
| Taille d’une source de données dans le stockage du coffre. |54 400 Go, maximum. La limite ne s’applique pas à la sauvegarde de machines virtuelles IaaS. |
| Coffres de sauvegarde dans un abonnement Azure. |500 coffres par région. |
| Sauvegardes quotidiennes planifiées. |Windows Server/Client : Trois fois par jour.<br/> System Center DPM : Deux fois par jour. <br/> Machines virtuelles IaaS : Une fois par jour.  |
| Disques de données attachés à une machine virtuelle Azure pour la sauvegarde. | 16 |
| Disque de données unique attaché à une machine virtuelle Azure pour la sauvegarde.| 4 095 Go|
