---
title: Fichier Include
description: Fichier Include
services: backup
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: ca6c57b139b195b94dc7171336cfcc34d18b1872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828865"
---
Les limites suivantes s’appliquent à la sauvegarde Azure.

| **Limite** | **Par défaut** |
| --- | --- |
| Serveurs ou machines pouvant être inscrites dans un coffre. | Windows Server/Windows Client/System Center Data Protection Manager : 50. <br/><br/> Machines virtuelles IaaS : 1 000.  |
| Taille d’une source de données dans le stockage du coffre. |54 400 Go, maximum. La limite ne s’applique pas à la sauvegarde de machines virtuelles IaaS. |
| Coffres de sauvegarde dans un abonnement Azure. |500 coffres par région. |
| Sauvegardes quotidiennes planifiées. |Windows Server/Client : Trois fois par jour.<br/> System Center DPM : Deux fois par jour. <br/> Machines virtuelles IaaS : Une fois par jour.  |
| Disques de données attachés à une machine virtuelle Azure pour la sauvegarde. | 16 |
| Disque de données unique attaché à une machine virtuelle Azure pour la sauvegarde.| 32 To|
