---
title: Fichier Include
description: Fichier Include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/14/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ed6b2b47ead64eac664f789aa8eba7887e68e7
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228792"
---
SMB Multichannel pour les partages de fichiers Azure présente actuellement les restrictions suivantes :
- Pris en charge uniquement sur les clients[Windows](../articles/storage/files/storage-how-to-use-files-windows.md) et [Linux](../articles/storage/files/storage-how-to-use-files-linux.md) qui utilisent SMB 3.1.1
- Le nombre maximal de canaux est de quatre, pour plus d’informations consultez [ce lien](../articles/storage/files/storage-troubleshooting-files-performance.md#cause-4-number-of-smb-channels-exceeds-four).
- SMB Direct n’est pas pris en charge.
- Les points de terminaison privés pour les comptes de stockage ne sont pas pris en charge.