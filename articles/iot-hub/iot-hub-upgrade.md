---
title: Mettre à niveau Azure IoT Hub | Microsoft Docs
description: Modifier le niveau de tarification et de mise à l’échelle pour IoT Hub afin d’obtenir davantage de fonctionnalités de gestion de la messagerie et de l’appareil.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: 1f60b7d30c073c49d5e0a7d35e7263c2181ed744
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903062"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Procédure de mise à niveau de votre IoT Hub

À mesure que votre solution IoT croît, Azure IoT Hub est prêt à vous aider à monter en puissance (« scale up »). Azure IoT Hub offre deux niveaux, De base (B) et Standard (S), pour prendre en charge les clients qui souhaitent utiliser différentes fonctionnalités. Chaque niveau comporte trois tailles (1, 2 et 3) qui déterminent le nombre de messages qui peuvent être envoyés chaque jour. 

Lorsque vous avez plus d’appareils et avez besoin de davantage de fonctionnalités, il existe trois façons d’ajuster votre IoT Hub selon vos besoins :

* Ajoutez des unités au IoT Hub. Par exemple, chaque unité supplémentaire dans un IoT Hub B1 permet d’envoyer 400 000 messages supplémentaires par jour. 
* Modifiez la taille du IoT Hub. Par exemple, migrez du niveau B1 au niveau B2 pour augmenter le nombre de messages que chaque unité peut prendre en charge par jour.
* Passez à un niveau supérieur. Par exemple, passez du niveau B1 au niveau S1 pour la même capacité de messagerie, mais avec les fonctionnalités avancées disponibles dans le niveau Standard.

Ces modifications peuvent se produire sans interrompre les opérations existantes.

Si vous souhaitez passer à une version antérieure de votre IoT Hub, vous pouvez supprimer des unités et réduire la taille de l’IoT Hub. Toutefois, vous ne pouvez pas rétrograder à un niveau inférieur. Par exemple, vous pouvez passer du niveau S2 au niveau S1, mais pas du niveau S2 au niveau B1. 

Ces exemples sont destinés à vous aider à comprendre comment ajuster votre IoT Hub aux modifications de votre solution. Pour plus d’informations sur les fonctionnalités de chaque niveau, consultez toujours la [Tarification Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/). 

## <a name="upgrade-your-existing-iot-hub"></a>Mettre à niveau votre IoT Hub existant 

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à votre IoT Hub. 
2. Sélectionnez **Tarification et mise à l'échelle**. 

   ![Tarification et mise à l'échelle](./media/iot-hub-upgrade/pricing-scale.png)

3. Pour modifier le niveau de votre hub, sélectionnez **Tarification et niveau de mise à l’échelle**. Choisissez un nouveau niveau, puis cliquez sur **Sélectionner**.

   ![Tarification et mise à l'échelle](./media/iot-hub-upgrade/select-tier.png)

4. Pour modifier le nombre d’unités de votre hub, entrez une nouvelle valeur sous **Unités IoT Hub**. 
5. Cliquez sur **Enregistrer** pour enregistrer vos modifications. 

Votre IoT Hub est maintenant ajusté et vos configurations restent inchangées. Notez que la limite de partition pour le niveau De base d’IoT Hub est 8. Cette limite ne change pas quand vous migrez du niveau De base vers le niveau Standard.

## <a name="next-steps"></a>Étapes suivantes

Obtenez plus de détails sur la façon de [choisir le bon niveau IoT Hub](iot-hub-scaling.md). 

