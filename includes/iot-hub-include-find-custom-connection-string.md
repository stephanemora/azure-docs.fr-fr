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
ms.openlocfilehash: 55dc712d323c43849fa3de23c83e29fc348a0f1e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403978"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Pour créer une stratégie d'accès partagé qui accorde des autorisations de **connexion de service** et de **lecture du registre**, et obtenir une chaîne de connexion pour cette stratégie, procédez comme suit :

1. Ouvrez votre IoT Hub dans le [portail Azure](https://portal.azure.com). La méthode la plus simple pour accéder à votre IoT Hub consiste à sélectionner **Groupes de ressources**, le groupe de ressources dans lequel se trouve votre IoT Hub, puis votre IoT Hub dans la liste des ressources.

2. Dans le volet de gauche de votre IoT Hub, sélectionnez **Stratégies d’accès partagé**.

3. Dans le menu supérieur au-dessus de la liste des stratégies, sélectionnez **Ajouter**.

4. Dans le volet **Ajouter une stratégie d'accès partagé**, entrez un nom descriptif pour votre stratégie, par exemple : *serviceAndRegistryRead*. Sous **Autorisations**, sélectionnez **Lecture du registre** et **Connexion du service**, puis sélectionnez **Créer**.

    ![Montrer comment ajouter une nouvelle stratégie d'accès partagé](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

5. Dans le volet **Stratégies d'accès partagé**, sélectionnez votre nouvelle stratégie dans la liste des stratégies.

6. Sous **Clés d’accès partagé**, sélectionnez l’icône de copie pour la **chaîne de connexion -- clé primaire** et enregistrez la valeur.

    ![Montrer comment récupérer la chaîne de connexion](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Pour plus d’informations sur les autorisations et les stratégies d’accès partagé IoT Hub, consultez [Contrôle d’accès et autorisations](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
