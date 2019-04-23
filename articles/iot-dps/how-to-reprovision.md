---
title: Guide pratique pour reprovisionner des appareils dans le service Azure IoT Hub Device Provisioning | Microsoft Docs
description: Comment reprovisionner des appareils avec votre instance du service de provisionnement des appareils
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 92680a453d93c8dc0189c6ae376449a8e7a22076
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799131"
---
# <a name="how-to-reprovision-devices"></a>Comment reprovisionner des appareils

Durant le cycle de vie d’une solution IoT, il est fréquent d’avoir à déplacer des appareils d’un hub IoT à un autre. Voici quelques scénarios nécessitant le déplacement d’un appareil :

* **GÉOLOCALISATION**: Lorsqu’un appareil se déplace entre les emplacements, latence du réseau est simplifiée par l’emploi de l’appareil migré vers un hub IoT plus près à chaque emplacement.

* **Multilocation** : Un appareil est utilisé dans la même solution IoT mais, réaffecté ou loué pour un client ou le site du client. Le service fourni à ce nouveau client peut utiliser un autre hub IoT.

* **Modification de la solution**: Un appareil peut être déplacé dans une solution IoT nouveau ou mis à jour. Cette réaffectation peut nécessiter que l’appareil communique avec un nouveau hub IoT qui est connecté à d’autres composants backend. 

* **Mise en quarantaine**: Similaire à une modification de la solution. Un appareil défectueux, compromis ou obsolète est réassigné à un hub IoT où il doit obligatoirement être mis à jour afin de redevenir conforme. Une fois que l’appareil fonctionne correctement, il est migré vers son hub principal d’origine.

Pour plus d’une présentation plus détaillée de la préparation, consultez [IoT Hub Device réapprovisionnement concepts](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Configurer la stratégie d’allocation de l’inscription

La stratégie d’allocation détermine comment les appareils associés à l’inscription sont alloués ou affectés à un hub IoT une fois reprovisionnés.

Les étapes suivantes configurent la stratégie d’allocation pour l’inscription d’un appareil :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre instance du service Device Provisioning.

2. Cliquez sur **Gérer les inscriptions**, puis sur le groupe d’inscriptions ou sur l’inscription individuelle que vous voulez configurer pour le reprovisionnement. 

3. Sous **Sélectionner le mode d’affectation des appareils aux hubs**, sélectionnez une des stratégies d’allocation suivantes :

    * **Latence la plus faible** : Cette stratégie affecte les appareils au IoT Hub lié qui entraîneront des communications la plus faible latence entre l’appareil et IoT Hub. Cette option permet à l’appareil communiquer avec le hub IoT le plus proche de l’emplacement. 
    
    * **Distribution uniformément pondérée**: Cette stratégie distribue les appareils sur les Hubs IoT liés en fonction du poids d’allocation affecté à chaque hub IoT lié. Cette stratégie vous permet d’équilibrer la charge des appareils sur un groupe de hubs liés en fonction des pondérations d’allocation définies sur ces hubs. Si vous provisionnez des appareils sur un seul hub IoT, nous vous recommandons ce paramètre. Il s’agit du paramètre par défaut. 
    
    * **Configuration statique**: Cette stratégie exige un IoT Hub souhaité être répertoriées dans l’entrée d’inscription pour un appareil à provisionner. Cette stratégie vous permet de désigner un hub IoT spécifique auquel vous voulez affecter les appareils.

4. Sous **Sélectionner les hubs IoT auxquels ce groupe peut être affecté**, sélectionnez les hubs IoT liés que vous voulez inclure avec votre stratégie d’allocation. Si vous le souhaitez, ajoutez un nouveau hub IoT lié en utilisant le bouton **Lier un nouveau hub IoT**.

    Avec la stratégie d’allocation **Latence la plus faible**, les hubs que vous sélectionnez sont inclus dans l’évaluation de la latence pour déterminer le hub le plus proche pour l’affectation des appareils.

    Avec la stratégie d’allocation **Distribution uniformément pondérée**, la charge des appareils est équilibrée entre les hubs que vous sélectionnez, en fonction de leurs pondérations d’allocation configurées et de leur charge actuelle d’appareils.

    Avec la stratégie d’allocation **Configuration statique**, sélectionnez le hub IoT auquel vous voulez affecter les appareils.

4. Cliquez sur **Enregistrer** ou passez à la section suivante pour définir la stratégie de reprovisionnement.

    ![Sélectionner la stratégie d’allocation de l’inscription](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Définir la stratégie de reprovisionnement

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre instance du service Device Provisioning.

2. Cliquez sur **Gérer les inscriptions**, puis sur le groupe d’inscriptions ou sur l’inscription individuelle que vous voulez configurer pour le reprovisionnement.

3. Sous **Sélectionner comment vous voulez que les données des appareils soient gérées lors du reprovisionnement sur un autre hub IoT**, choisissez une des stratégies de reprovisionnement suivantes :

    * **Remettre et migrer les données**: Cette stratégie prend des mesures lorsque les périphériques associés à l’entrée d’inscription soumettent une nouvelle demande d’approvisionnement. Selon la configuration de l’entrée d’inscription, l’appareil peut être réassigné à un autre hub IoT. Si l’appareil est déplacé vers un autre hub IoT, il est désinscrit du hub IoT initial. Toutes les données d’état de l’appareil provenant de ce hub IoT initial sont alors migrées vers le nouveau hub IoT. Durant la migration, l’état de l’appareil indique **Affectation**.

    * **Reconfigurer et réinitialiser à la configuration initiale**: Cette stratégie prend des mesures lorsque les périphériques associés à l’entrée d’inscription soumettent une nouvelle demande d’approvisionnement. Selon la configuration de l’entrée d’inscription, l’appareil peut être réassigné à un autre hub IoT. Si l’appareil est déplacé vers un autre hub IoT, il est désinscrit du hub IoT initial. Les données de configuration initiale que l’instance du service Device Provisioning a reçues au moment du provisionnement de l’appareil sont transmises au nouveau hub IoT. Durant la migration, l’état de l’appareil indique **Affectation**.

4. Cliquez sur **Enregistrer** pour activer le reprovisionnement de l’appareil en fonction de vos modifications.

    ![Sélectionner la stratégie d’allocation de l’inscription](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Envoyer une demande de provisionnement depuis l’appareil

Pour que les appareils puissent être reprovisionnés en fonction des modifications de configuration apportées dans les sections précédentes, ces appareils doivent demander un reprovisionnement. 

La fréquence à laquelle un appareil envoie une demande de provisionnement varie selon le scénario. Il est cependant conseillé de programmer vos appareils pour qu’ils envoient une demande de provisionnement à une instance du service de provisionnement au redémarrage, et pour qu’ils prennent en charge une [méthode](../iot-hub/iot-hub-devguide-direct-methods.md) permettant de déclencher manuellement le provisionnement à la demande. Le provisionnement peut également être déclenché en définissant une [propriété souhaitée](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

La stratégie de reprovisionnement sur une entrée d’inscription définit de quelle manière l’instance du service Device Provisioning gère ces demandes de provisionnement, et détermine si les données d’état de l’appareil doivent être migrées au moment du reprovisionnement. Les mêmes stratégies sont disponibles pour les inscriptions individuelles et les groupes d’inscriptions :

Pour obtenir un exemple de code qui envoie des demandes de provisionnement depuis un appareil lors d’une séquence de démarrage, consultez [Provisionnement automatique d’un appareil simulé](quick-create-simulated-device.md).


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus Reprovisioning, consultez [réapprovisionnement des concepts d’appareil IoT Hub](concepts-device-reprovision.md) 
- Pour en savoir plus Deprovisioning, consultez [Guide pratique pour déprovisionner des appareils qui ont été provisionnés automatiquement](how-to-unprovision-devices.md) 











