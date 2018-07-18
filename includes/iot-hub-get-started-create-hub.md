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
ms.openlocfilehash: d586ca18953b12045fbbaa4a656d78a7192eb88e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2018
ms.locfileid: "34371233"
---
## <a name="create-an-iot-hub"></a>Créer un hub IoT
Créez un IoT Hub pour que votre application de périphérique simulé puisse se connecter. Les étapes suivantes vous montrent comment exécuter cette tâche à l’aide du portail Azure.

1. Connectez-vous au [portail Azure][lnk-portal].

1. Sélectionnez **Créer une ressource** > **Internet des objets** > **IoT Hub**.
   
    ![Barre de lancement du portail Azure][1]

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

1. Lorsque votre IoT Hub est prêt, cliquez sur sa vignette dans le portail Azure pour ouvrir la fenêtre de ses propriétés. Maintenant que vous avez créé un IoT Hub, recherchez les informations importantes que vous utilisez pour connecter des appareils et des applications à votre IoT Hub. Cliquer sur **Stratégies d’accès partagé**.
   
1. Dans **Stratégies d’accès partagé**, sélectionnez la stratégie **iothubowner**. Copier l’IoT Hub **Chaîne de connexion---clé primaire** pour une utilisation ultérieure. Consultez la rubrique [Access Control][lnk-access-control] du « Guide du développeur IoT Hub » pour plus d’informations.
   
    ![Stratégies d’accès partagé][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
