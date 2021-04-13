---
title: Fichier include
description: Fichier include
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 04/05/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 27f5755ce8b7d204cad6cdc2281d7992bf86615a
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504681"
---
Lorsque vous créez une passerelle de réseau virtuel, vous devez spécifier la référence SKU de passerelle que vous voulez utiliser. Si vous sélectionnez une référence SKU de passerelle supérieure, la quantité de ressources processeur et de bande passante réseau allouées à la passerelle augmente. Par conséquent, la passerelle peut prendre en charge un débit réseau plus élevé sur le réseau virtuel. 

Les passerelles de réseau virtuel ExpressRoute peuvent utiliser les références SKU suivantes :

|     | Passerelle VPN et ExpressRoute coexistants | FastPath | Nombre maximal de connexions au circuit |
| --- | --- | --- | --- |
| **Standard** | Oui | Non | 4 |
| **HighPerformance** | Oui | Non | 4 |
| **UltraPerformance** | Oui | Oui | 16 |