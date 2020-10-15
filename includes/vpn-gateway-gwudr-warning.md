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
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81275558"
---
Les routes définies par l’utilisateur avec une destination 0.0.0.0/0 et les groupes de sécurité réseau sur le sous-réseau de passerelle **ne sont pas pris en charge**. La création de passerelles avec cette configuration est bloquée. Les passerelles nécessitent l’accès aux contrôleurs de gestion pour fonctionner correctement. [Propagation de route BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) doit être défini sur « Activé » sur le sous-réseau de passerelle pour garantir la disponibilité de la passerelle. Si cette valeur est définie sur Désactivé, la passerelle ne fonctionne pas.
