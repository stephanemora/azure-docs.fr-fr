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
ms.openlocfilehash: 63922eb623576379058c9a8a367d6e52249115f2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109091"
---
Les limites suivantes s’appliquent à la sauvegarde Azure.

| **Limite** | **Par défaut** |
| --- | --- |
| Serveurs/ordinateurs pouvant être inscrits dans un coffre | Windows Server/Windows Client/System Center DPM : 50 <br/><br/> Machines virtuelles IaaS : 1 000  |
| Taille d’une source de données dans le stockage du coffre |54 400 Go maximum – la limite ne s’applique pas à la sauvegarde de machines virtuelles IaaS |
| Coffres de sauvegarde dans un abonnement Azure |500 coffres par région |
| Sauvegardes quotidiennes planifiées |Windows Server/Client : 3 par jour<br/> System Center DPM : 2 par jour <br/> Machines virtuelles IaaS : 1 par jour  |
| Disques de données attachés à une machine virtuelle Azure pour la sauvegarde | 32 |
| Disque de données unique attaché à une machine virtuelle Azure pour la sauvegarde| 4095 Go|



