---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/14/2021
ms.author: alkohli
ms.openlocfilehash: acbb239a6fb839f12b31f8521e4a07fc6083458e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "130000064"
---
Voici une liste des systèmes d’exploitation pris en charge pour l’opération de copie des données par l’intermédiaire des clients connectés à votre appareil.

| **Système d’exploitation** | **Versions** | **Remarques** |
| --- | --- | --- |
| Windows Server |2016 RS1 et versions ultérieures<br> 2019 RS5 et versions ultérieures | Avec les éditions antérieures de ces systèmes d’exploitation, vous ne pouvez pas utiliser RoboCopy en mode de sauvegarde (`robocopy /B`) pour copier des fichiers qui contiennent des flux Alternate Data Streams (ADS) ou qui utilisent des attributs Extended Attributes (EAs) dans leurs listes de contrôle d’accès (ACL). |
| Windows |7, 8, 10 |   |
| Linux    |         |   |
