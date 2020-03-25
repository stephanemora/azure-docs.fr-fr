---
title: Configurer des appareils dans la solution de supervision à distance – Azure | Microsoft Docs
description: Ce tutoriel vous montre comment configurer les appareils connectés à l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: e4236952bd41c4955e337813ff6d706263b8ef47
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73890901"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>Tutoriel : Configurer les appareils connectés à votre solution de supervision

Dans ce didacticiel, vous utilisez l’accélérateur de solution de supervision à distance pour configurer et gérer vos appareils IoT connectés. Vous ajoutez un nouvel appareil à l’accélérateur de solution et vous le configurez.

Contoso a commandé de nouvelles machines visant à développer l’une de ses usines. En attendant la livraison des nouvelles machines, vous souhaitez exécuter une simulation pour tester le comportement de votre solution. Pour exécuter la simulation, vous ajoutez un nouvel appareil de moteur simulé à l’accélérateur de solution de supervision à distance, puis vous vérifiez si cet appareil simulé répond correctement aux mises à jour de configuration. Bien que ce tutoriel se serve d’appareils simulés, un développeur d’appareil peut implémenter des méthodes directes dans un [appareil réel connecté à l’accélérateur de solution de supervision à distance](iot-accelerators-connecting-devices.md).

Dans ce tutoriel, vous allez :

>[!div class="checklist"]
> * Provisionner un appareil simulé.
> * Tester un appareil simulé.
> * Reconfigurer un appareil.
> * Organiser vos appareils.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Utiliser un appareil simulé

Accédez à la page **Device Explorer** de la solution, puis cliquez sur **+ Nouvel appareil** :

[![Provisionner un appareil simulé](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

Dans le panneau **Nouvel appareil**, choisissez **Simulé**, laisse le nombre d’appareils à provisionner sur **1**, choisissez le modèle d’appareil **Moteur défectueux**, puis **Appliquer** pour créer l’appareil simulé :

[![Provisionner un appareil de moteur simulé](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Tester l’appareil simulé

Pour vérifier si votre appareil de moteur simulé envoie des données de télémétrie et rapporte des valeurs de propriété, sélectionnez-le dans la liste des appareils de la page **Device Explorer**. Les informations en temps réel sur votre moteur s’affichent dans le panneau **Détail de l’appareil** :

[![Afficher le nouvel appareil de moteur simulé](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

Dans **Détail de l’appareil**, vérifiez que votre nouvel appareil envoie des données de télémétrie. Pour afficher les flux de données de télémétrie de vibration de votre appareil, cliquez sur **Vibration** :

[![Sélectionner un flux de données de télémétrie à afficher](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

Le panneau **Détail de l’appareil** affiche d’autres informations sur l’appareil, telles que les valeurs d’étiquette, les méthodes qu’il prend en charge et les propriétés qu’il indique.

Pour afficher les diagnostics détaillés, faites défiler le panneau **Détails de l’appareil** vers le bas pour afficher la section **Diagnostics**.

## <a name="reconfigure-a-device"></a>Reconfigurer un appareil

Pour vérifier que vous pouvez mettre à jour les propriétés de configuration du moteur, sélectionnez-le dans la liste des appareils de la page **Device Explorer**. Cliquez ensuite sur **Tâches**, puis choisissez **Propriétés**. Le panneau Travaux affiche les valeurs de propriété qui peuvent être mises à jour de l’appareil sélectionné :

[![Reconfigurer un appareil](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Pour mettre à jour l’emplacement du moteur, définissez le nom de la tâche par **UpdateEngineLocation**, définissez la longitude sur **-122.15**, l’emplacement sur **Factory 2**, la latitude sur **47.62** et cliquez sur **Appliquer** :

[![Mettre à jour une valeur de propriété d’appareil](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Pour suivre le statut de la tâche, cliquez sur **Afficher le statut de la tâche** :

[![Mettre à jour une valeur de propriété d’appareil](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Une fois la tâche terminée, accédez à la page **Tableau de bord**. Le moteur de l’appareil est affiché sur la carte à son nouvel emplacement :

[![Afficher l’emplacement du moteur](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Organiser vos appareils

En tant qu’opérateur, pour que vous puissiez plus facilement organiser et gérer vos appareils, vous souhaitez étiqueter ces derniers avec un nom d’équipe. Contoso possède deux équipes différentes pour les activités de service sur le terrain :

* L’équipe Smart Vehicle gère camions et les appareils de prototypage.
* L’équipe Smart Building gère les refroidisseurs, les élévateurs et les moteurs.

Pour afficher tous vos appareils, accédez à la page **Device Explorer**, puis choisissez le filtre **Tous les appareils** :

[![Afficher tous les appareils](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Ajouter des étiquettes

Sélectionnez tous les appareils **Trucks** (Camions) et **Prototyping** (Prototypage). Cliquez ensuite sur **Tâches**.

Dans le panneau **Tâches**, sélectionnez **Balise**, définissez le nom de la tâche par **AddConnectedVehicleTag**, puis ajoutez une balise de texte nommée **FieldService** avec une valeur **ConnectedVehicle**. Cliquez alors sur **Appliquer** :

[![Ajouter une étiquette aux appareils prototypage et camion](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Sur la page de l’appareil, sélectionnez tous les appareils **Chiller** (Refroidisseur), **Elevator** (Élévateur) et **Engine** (Moteur). Cliquez ensuite sur **Tâches**.

Dans le panneau **Tâches**, sélectionnez **Balise**, définissez le nom de la tâche par **AddSmartBuildingTag**, puis ajoutez une balise de texte nommée **FieldService** avec une valeur **SmartBuilding**. Cliquez alors sur **Appliquer** :

[![Ajouter une balise aux appareils Chiller (Refroidisseur), Elevator (Élévateur), et Engine (Moteur)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Création de filtres

Vous pouvez maintenant utiliser les valeurs des étiquettes pour créer des filtres. Dans la page **Device Explorer**, cliquez sur **Gérer les groupes d’appareils** :

[![Gérer les groupes d’appareils](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Créez un filtre de texte qui utilise le nom de balise **FieldService** et la valeur **SmartBuilding** dans la condition. Enregistrez le filtre sous le nom **Smart Building** :

[![Créer le filtre Smart Building](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Créez un filtre de texte qui utilise le nom de balise **FieldService** et la valeur **ConnectedVehicle** dans la condition. Enregistrer le filtre sous le nom **Connected Vehicle**.

[![Créer le filtre Connected Vehicle](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

L’opérateur Contoso peut désormais interroger les appareils selon l’équipe d’opérations :

[![Créer le filtre Connected Vehicle](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a montré comment configurer et gérer les appareils connectés à l’accélérateur de solution de supervision à distance. Pour apprendre à utiliser l’accélérateur de solution pour effectuer une analyse des causes premières sur une alerte inattendue, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [Effectuer une analyse de cause racine sur une alerte](iot-accelerators-remote-monitoring-root-cause-analysis.md)
