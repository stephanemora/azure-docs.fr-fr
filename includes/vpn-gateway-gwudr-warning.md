---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 43e381bd26dadbea5ef5bfb002e51465e180a097
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159309"
---
N’associez pas une table de routage comprenant un itinéraire avec une destination 0.0.0.0/0 au sous-réseau de passerelle. Le fonctionnement correct de la passerelle s’en trouve empêché.