---
title: Fichier Include
description: Fichier Include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050470"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Dans cet article, vous créez un service back-end qui ajoute des propriétés souhaitées à un jumeau d’appareil, puis interroge le registre des identités pour trouver tous les appareils dont les propriétés signalées ont été mises à jour en conséquence. Votre service a besoin de l’autorisation de **connexion du service** pour modifier les propriétés souhaitées d'un jumeau d’appareil, et de l’autorisation de **lecture du registre** pour interroger le registre des identités. Aucune stratégie d'accès partagé par défaut ne contient que ces deux autorisations. Vous devez donc en créer une.
