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
ms.openlocfilehash: 9a29406b92f7d2e2ce8171974efb5a264e112d1d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724900"
---
1. Connectez-vous au [portail Azure][lnk-portal].
1. Sélectionnez **Créer une ressource** > **Internet des objets** > **IoT Hub**.
   
    ![Capture d’écran de l’accès à IoT Hub dans le portail Azure][1]

1. Dans le volet **IoT Hub**, entrez les informations suivantes pour votre IoT Hub :

   * **Abonnement** : choisissez l’abonnement que vous souhaitez utiliser pour créer cet IoT Hub.

   * **Groupe de ressources** : créez un groupe de ressources pour héberger l’IoT Hub ou utilisez-en un existant. Pour plus d’informations, consultez l’article [Use resource groups to manage your Azure resources][lnk-resource-groups] (Utiliser des groupes de ressources pour gérer vos ressources Azure).

   * **Région** : sélectionnez la région correspondant à votre emplacement.

   * **Nom** : créer un nom de votre IoT Hub. Si le nom saisi est disponible, une coche verte s’affiche.

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