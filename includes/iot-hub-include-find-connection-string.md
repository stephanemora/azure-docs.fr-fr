---
title: Fichier Include
description: Fichier Include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d7ac457041474f4e774414b1d5e6f9ed09dc856
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146513"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

Une fois votre hub créé, récupérez la chaîne de connexion pour le hub. Celle-ci permet de connecter des appareils et des applications à votre hub. 

1. Cliquez sur votre hub pour afficher le volet IoT Hub avec les paramètres, etc. Cliquer sur **Stratégies d’accès partagé**.
   
2. Dans **Stratégies d’accès partagé**, sélectionnez la stratégie **iothubowner**. 

3. Sous **Clés d’accès partagé**, copiez la **Chaîne de connexion -- clé primaire** qui sera utilisée plus tard.

    ![Montrer comment récupérer la chaîne de connexion](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    Consultez la rubrique [Contrôle d’accès](../articles/iot-hub/iot-hub-devguide-security.md) du « Guide du développeur IoT Hub » pour obtenir plus d’informations.
