---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95561291"
---
Les routes définies par l’utilisateur avec une destination 0.0.0.0/0 et les groupes de sécurité réseau sur le sous-réseau de passerelle **ne sont pas pris en charge**. La création de passerelles avec cette configuration est bloquée. Les passerelles nécessitent l’accès aux contrôleurs de gestion pour fonctionner correctement. [Propagation de route BGP](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol) doit être défini sur « Activé » sur le sous-réseau de passerelle pour garantir la disponibilité de la passerelle. Si cette valeur est définie sur Désactivé, la passerelle ne fonctionne pas.