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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176949"
---
N’associez pas une table de routage comprenant un itinéraire avec une destination 0.0.0.0/0 au sous-réseau de passerelle. Le fonctionnement correct de la passerelle s’en trouve empêché.