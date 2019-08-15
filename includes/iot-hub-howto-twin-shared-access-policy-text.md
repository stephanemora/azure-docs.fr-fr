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
ms.openlocfilehash: a843821a7720ddcb17f8a369d012facd7ba81e35
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912356"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Dans cet article, vous créez un service principal qui ajoute les propriétés souhaitées à un jumeau d’appareil, puis interroge le registre des identités pour trouver tous les appareils dont les propriétés déclarées ont été mises à jour en conséquence. Votre service a besoin de l’autorisation de **connexion du service** pour modifier les propriétés souhaitées d'un jumeau d’appareil, et de l’autorisation de **lecture du registre** pour interroger le registre des identités. Aucune stratégie d'accès partagé par défaut ne contient que ces deux autorisations. Vous devez donc en créer une.
