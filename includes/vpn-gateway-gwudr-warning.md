---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71838162"
---
Les routes définies par l’utilisateur avec une destination 0.0.0.0/0 et des groupes de sécurité réseau sur le sous-réseau de passerelle **ne sont pas pris en charge**. La création de passerelles avec cette configuration est bloquée. Les passerelles nécessitent l’accès aux contrôleurs de gestion pour fonctionner correctement.
