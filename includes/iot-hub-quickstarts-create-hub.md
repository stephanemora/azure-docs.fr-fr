---
title: Fichier Include
description: Fichier Include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 63acf0297a694ff442d56e67d52fd9b4e49f812d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008584"
---
La première étape consiste à utiliser le portail Azure pour créer un hub IoT dans votre abonnement. Le hub IoT vous permet de traiter de gros volumes de données de télémétrie dans le cloud en provenance de nombreux appareils. Ensuite, le hub active un ou plusieurs services principaux en cours d’exécution dans le cloud pour lire et traiter ces données de télémétrie.

1. Connectez-vous au [Portail Azure](http://portal.azure.com).

1. Sélectionnez **Créer une ressource** > **Internet des objets** > **IoT Hub**.

    ![Sélectionnez cette option pour installer IoT Hub][1]

1. Dans le volet **IoT Hub**, entrez les informations suivantes pour votre IoT Hub :

   * **Abonnement** : choisissez l’abonnement que vous souhaitez utiliser pour créer cet IoT Hub.
   * **Groupe de ressources** : créez un groupe de ressources pour contenir l’IoT Hub ou utilisez-en un qui existe déjà. En plaçant toutes les ressources associées dans un même groupe, comme **TestResources**, vous pouvez les gérer toutes ensemble. Par exemple, si vous supprimez le groupe de ressources, vous supprimez également toutes les ressources contenues dans ce groupe. Pour plus d’informations, consultez l’article [Use resource groups to manage your Azure resources][lnk-resource-groups] (Utiliser des groupes de ressources pour gérer vos ressources Azure).
   * **Région** : sélectionnez l’emplacement le plus proche de vos périphériques.
   * **Nom** : créez un nom unique pour votre IoT Hub. Si le nom saisi est disponible, une coche verte s’affiche.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Fenêtre Bases de l’IoT Hub][2]

2. Sélectionnez **Suivant : taille et échelle** pour poursuivre la création de votre IoT Hub. 

3. Choisissez votre **niveau de tarification et de mise à l’échelle**. Pour cet article, sélectionnez le niveau **F1 - Gratuit** s’il est toujours disponible sur votre abonnement. Pour plus d’informations, consultez [Niveau de tarification et de mise à l’échelle][lnk-pricing].

   ![Fenêtre de taille et de mise à l’échelle de l’IoT Hub][3]

4. Sélectionnez **Revoir + créer**.

1. Passez en revue vos informations d’IoT Hub, puis cliquez sur **Créer**. La création de votre IoT Hub peut prendre plusieurs minutes. Vous pouvez suivre la progression dans le volet **Notifications**.


<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md