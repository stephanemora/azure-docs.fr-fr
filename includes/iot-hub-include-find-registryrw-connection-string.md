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
ms.openlocfilehash: a752427d1e5873f0a27fd231872e3a13b234d9ed
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402341"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Pour obtenir la chaîne de connexion IoT Hub pour la stratégie **registryReadWrite**, procédez comme suit :

1. Ouvrez votre IoT Hub dans le [portail Azure](https://portal.azure.com).  La méthode la plus simple pour accéder à votre IoT Hub consiste à sélectionner **Groupes de ressources**, le groupe de ressources dans lequel se trouve votre IoT Hub, puis votre IoT Hub dans la liste des ressources.

2. Dans le volet de gauche de votre IoT Hub, sélectionnez **Stratégies d’accès partagé**.

3. Dans la liste des stratégies, sélectionnez la stratégie **registryReadWrite**.

4. Sous **Clés d’accès partagé**, sélectionnez l’icône de copie pour la **chaîne de connexion -- clé primaire** et enregistrez la valeur.

    ![Montrer comment récupérer la chaîne de connexion](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-string.png)

Pour plus d’informations sur les autorisations et les stratégies d’accès partagé IoT Hub, consultez [Contrôle d’accès et autorisations](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
