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
ms.openlocfilehash: 08afdcf91499fdb6f2da6e9ccc82313286f5c964
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43111951"
---
## <a name="create-an-iot-hub"></a>Créer un hub IoT
Créez un IoT Hub pour que votre application de périphérique simulé puisse se connecter. Les étapes suivantes vous montrent comment exécuter cette tâche à l’aide du portail Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

2. Sélectionnez **Créer une ressource** > **Internet des objets** > **IoT Hub**.
   
    ![Barre de lancement du portail Azure](./media/iot-hub-get-started-create-hub/create-iot-hub1.png)

3. Dans le volet **IoT Hub**, entrez les informations suivantes pour votre IoT Hub :

   * **Abonnement** : choisissez l’abonnement que vous souhaitez utiliser pour créer cet IoT Hub.

   * **Groupe de ressources** : créez un groupe de ressources pour héberger l’IoT Hub ou utilisez-en un existant. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../articles/azure-resource-manager/resource-group-portal.md).

   * **Région** : sélectionnez la région correspondant à votre emplacement.

   * **Nom** : créer un nom de votre IoT Hub. Si le nom saisi est disponible, une coche verte s’affiche.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Fenêtre Bases de l’IoT Hub](./media/iot-hub-get-started-create-hub/create-iot-hub2.png)

4. Sélectionnez **Suivant : taille et échelle** pour poursuivre la création de votre IoT Hub. 

5. Choisissez votre **niveau de tarification et de mise à l’échelle**. Pour cet article, sélectionnez le niveau **F1 - Gratuit** s’il est toujours disponible sur votre abonnement. Pour plus d’informations, consultez [Niveau de tarification et de mise à l’échelle](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Fenêtre de taille et de mise à l’échelle de l’IoT Hub](./media/iot-hub-get-started-create-hub/create-iot-hub3.png)

6. Sélectionnez **Revoir + créer**.

7. Passez en revue vos informations d’IoT Hub, puis cliquez sur **Créer**. La création de votre IoT Hub peut prendre plusieurs minutes. Vous pouvez suivre la progression dans le volet **Notifications**.

8. Lorsque votre IoT Hub est prêt, cliquez sur sa vignette dans le portail Azure pour ouvrir la fenêtre de ses propriétés. Maintenant que vous avez créé un IoT Hub, recherchez les informations importantes que vous utilisez pour connecter des appareils et des applications à votre IoT Hub. Cliquer sur **Stratégies d’accès partagé**.
   
9. Dans **Stratégies d’accès partagé**, sélectionnez la stratégie **iothubowner**. Copier l’IoT Hub **Chaîne de connexion---clé primaire** pour une utilisation ultérieure. Consultez la rubrique [Contrôle d’accès](../articles/iot-hub/iot-hub-devguide-security.md) du « Guide du développeur IoT Hub » pour obtenir plus d’informations.
   
    ![Stratégies d’accès partagé](./media/iot-hub-get-started-create-hub/create-iot-hub5.png)