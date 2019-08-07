---
title: Fichier Include
description: Fichier Include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: f97ab0a0bb91af90daaf60c5c2c85f0ede56a5e7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403976"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Dans cet article, vous créez un service principal qui ajoute les propriétés souhaitées à un jumeau d’appareil, puis interroge le registre des identités pour trouver tous les appareils dont les propriétés déclarées ont été mises à jour en conséquence. Votre service a besoin de l’autorisation de **connexion du service** pour modifier les propriétés souhaitées d'un jumeau d’appareil, et de l’autorisation de **lecture du registre** pour interroger le registre des identités. Aucune stratégie d'accès partagé par défaut ne contient que ces deux autorisations. Vous devez donc en créer une.
