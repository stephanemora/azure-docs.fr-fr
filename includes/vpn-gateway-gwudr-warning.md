---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 06/04/2018
ms.date: 12/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 43e381bd26dadbea5ef5bfb002e51465e180a097
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64859063"
---
N’associez pas une table de routage comprenant un itinéraire avec une destination 0.0.0.0/0 au sous-réseau de passerelle. Le fonctionnement correct de la passerelle s’en trouve empêché.
