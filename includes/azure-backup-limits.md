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
ms.openlocfilehash: 9ad1208283527f35e04dede2706fd0e424096dc5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54300695"
---
Les limites suivantes s’appliquent à la sauvegarde Azure.

| **Limite** | **Par défaut** |
| --- | --- |
| Serveurs/ordinateurs pouvant être inscrits dans un coffre | Windows Server/Windows Client/System Center DPM : 50 <br/><br/> Machines virtuelles IaaS : 1 000  |
| Taille d’une source de données dans le stockage du coffre |54 400 Go maximum – la limite ne s’applique pas à la sauvegarde de machines virtuelles IaaS |
| Coffres de sauvegarde dans un abonnement Azure |500 coffres par région |
| Sauvegardes quotidiennes planifiées |Windows Server/Client : 3 par jour<br/> System Center DPM : 2 par jour <br/> Machines virtuelles IaaS : 1 par jour  |
| Disques de données attachés à une machine virtuelle Azure pour la sauvegarde | 16 |
| Disque de données unique attaché à une machine virtuelle Azure pour la sauvegarde| 4095 Go|
