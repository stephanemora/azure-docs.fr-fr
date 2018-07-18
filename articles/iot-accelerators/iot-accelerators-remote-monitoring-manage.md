---
title: Gérer des appareils dans une solution de surveillance à distance Azure | Microsoft Docs
description: Ce didacticiel vous montre comment gérer les appareils connectés à l’accélérateur de solution de surveillance à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/12/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 63baf6397b2542311525bac740c50b5eacbd35cf
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097425"
---
# <a name="tutorial-configure-and-manage-devices-connected-to-your-monitoring-solution"></a>Didacticiel : Configurer et gérer les appareils connectés à votre solution de surveillance

Dans ce didacticiel, vous utilisez l’accélérateur de solution de surveillance à distance pour configurer et gérer vos appareils IoT connectés. Vous ajoutez un nouvel appareil à l’accélérateur de solution, le configurez et mettez à jour le microprogramme de l’appareil.

Contoso a commandé de nouvelles machines visant à développer l’une de ses usines. En attendant la livraison des nouvelles machines, vous souhaitez exécuter une simulation pour tester le comportement de votre solution. Pour exécuter la simulation, vous ajoutez un nouveau moteur d’appareil simulé à l’accélérateur de solution de surveillance à distance et vérifiez si l’appareil simulé répond correctement aux actions et aux mises à jour de configuration.

Pour fournir un moyen extensible de configurer et de gérer des appareils, l’accélérateur de solution de surveillance à distance utilise des fonctionnalités IoT Hub telles que les [travaux](../iot-hub/iot-hub-devguide-jobs.md) et les [méthodes directes](../iot-hub/iot-hub-devguide-direct-methods.md). Bien que ce didacticiel se serve d’appareils simulés, un développeur d’appareil peut implémenter des méthodes directes dans un [appareil physique connecté à l’accélérateur de solution de surveillance à distance](iot-accelerators-connecting-devices.md).

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

>[!div class="checklist"]
> * Provisionner un appareil simulé.
> * Tester un appareil simulé.
> * Mettre à jour le microprogramme d’un appareil.
> * Reconfigurer un appareil.
> * Organiser vos appareils.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin d’une instance déployée de l’accélérateur de solution de surveillance à distance dans votre abonnement Azure.

Si vous n’avez pas encore déployé l’accélérateur de solution de surveillance à distance, vous devez suivre le tutoriel [Déployer l’accélérateur de solution de surveillance à distance](quickstart-remote-monitoring-deploy.md).

## <a name="add-a-simulated-device"></a>Utiliser un appareil simulé

Accédez à la page **Appareils** dans la solution, puis cliquez sur **+ Nouvel appareil** :

[![Provisionner un appareil simulé](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

Dans le panneau **Nouvel appareil**, choisissez **Simulé**, laisse le nombre d’appareils à provisionner sur **1**, choisissez le modèle d’appareil **Moteur défectueux**, puis **Appliquer** pour créer l’appareil simulé :

[![Provisionner un appareil de moteur simulé](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>Tester l’appareil simulé

Pour vérifier si votre appareil simulé envoie des données de télémétrie et rapporte des valeurs de propriété, sélectionnez-le dans la liste des appareils sur la page **Appareils**. Les informations en temps réel sur votre appareil s’affichent dans le panneau **Détail de l’appareil** :

[![Afficher le nouvel appareil de moteur simulé](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

Dans **Détail de l’appareil**, vérifiez que votre nouvel appareil envoie des données de télémétrie. Pour afficher les différents flux de données de télémétrie de vibration de votre appareil, cliquez sur **Vibration** :

[![Sélectionner un flux de données de télémétrie à afficher](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

Le panneau **Détail de l’appareil** affiche d’autres informations sur l’appareil, telles que les valeurs d’étiquette, les méthodes qu’il prend en charge et les propriétés qu’il indique.

Pour afficher les diagnostics détaillés, faites défiler jusqu’à **Diagnostics** :

[![Afficher les diagnostics du périphérique](./media/iot-accelerators-remote-monitoring-manage/devicediagnostics-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicediagnostics-expanded.png#lightbox)

## <a name="act-on-a-device"></a>Agir sur un appareil

Pour vérifier si le moteur simulé répond correctement aux actions initiées depuis l’accélérateur de solution, exécutez la méthode **FirmwareUpdate**. Pour agir sur un appareil en exécutant une méthode, sélectionnez l’appareil dans la liste des appareils, puis cliquez sur **Tâches**. Vous pouvez sélectionner autant d’appareils que vous le souhaitez. Dans le panneau **Tâches**, sélectionnez **Exécuter une méthode**. Le modèle d’appareil **Moteur** spécifie trois méthodes : **FirmwareUpdate**, **FillTank** et **EmptyTank** :

[![Méthodes de moteur](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-expanded.png#lightbox)

Choisissez **FirmwareUpdate**, définissez le nom de la tâche par **UpdateEngineFirmware**, définissez la version du microprogramme sur **2.0.0**, définissez l’URI du microprogramme sur **http://contoso.com/engine.bin**, puis cliquez sur **Appliquer** :

[![Planifier la méthode mise à jour du microprogramme](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-expanded.png#lightbox)

Pour suivre le statut de la tâche, cliquez sur **Afficher le statut de la tâche** :

[![Surveiller la tâche de mise à jour du microprogramme](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-expanded.png#lightbox)

Une fois la tâche terminée, retournez à la page **Appareils**. La nouvelle version du microprogramme s’affiche pour le moteur de l’appareil.

Si vous sélectionnez plusieurs appareils de types différents sur la page **Appareils**, vous pouvez quand même créer une tâche pour exécuter une méthode sur ces appareils. Le panneau **Tâches** n’affiche que les méthodes qui sont communes à tous les appareils sélectionnés.

## <a name="reconfigure-a-device"></a>Reconfigurer un appareil

Pour vérifier que vous pouvez mettre à jour les propriétés de configuration du moteur, sélectionnez-le dans la liste des appareils sur la page **Appareils**. Cliquez ensuite sur **Tâches**, et choisissez **Reconfigurer**. Le panneau Travaux affiche les valeurs de propriété qui peuvent être mises à jour de l’appareil sélectionné :

[![Reconfigurer un appareil](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

Pour mettre à jour l’emplacement du moteur, définissez le nom de la tâche par **UpdateEngineLocation**, définissez la longitude sur **-122.15**, l’emplacement sur **Factory 2**, la latitude sur **47.62** et cliquez sur **Appliquer** :

[![Mettre à jour une valeur de propriété d’appareil](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

Pour suivre le statut de la tâche, cliquez sur **Afficher le statut de la tâche** :

[![Mettre à jour une valeur de propriété d’appareil](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Une fois la tâche terminée, accédez à la page **Tableau de bord**. Le moteur de l’appareil est affiché sur la carte à son nouvel emplacement :

[![Afficher l’emplacement du moteur](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Organiser vos appareils

En tant qu’opérateur, pour que vous puissiez plus facilement organiser et gérer vos appareils, vous souhaitez étiqueter ces derniers avec le nom d’équipe approprié. Contoso possède deux équipes différentes pour les activités de service sur le terrain :

* L’équipe Smart Vehicle gère camions et les appareils de prototypage.
* L’équipe Smart Building gère les refroidisseurs, les élévateurs et les moteurs.

Pour afficher tous vos appareils, accédez à la page **Appareils** et choisissez le filtre **Tous les appareils** :

[![Afficher tous les appareils](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Ajouter des étiquettes

Sélectionnez tous les appareils **Trucks** (Camions) et **Prototyping** (Prototypage). Cliquez ensuite sur **Tâches** :

[![Sélectionner les appareils prototypage et camion](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect-expanded.png#lightbox)

Sélectionnez **Balise**, définissez le nom de la tâche par **AddConnectedVehicleTag**, puis ajoutez une balise de texte nommée **FieldService** avec une valeur **ConnectedVehicle**. Cliquez alors sur **Appliquer** :

[![Ajouter une étiquette aux appareils prototypage et camion](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Sur la page de l’appareil, sélectionnez tous les appareils **Chiller** (Refroidisseur), **Elevator** (Élévateur) et **Engine** (Moteur). Cliquez ensuite sur **Tâches** :

[![Sélectionner les appareils Chiller (Refroidisseur), Elevator (Élévateur), et Engine (Moteur)](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect2-expanded.png#lightbox)

Sélectionnez **Balise**, définissez le nom de la tâche par **AddSmartBuildingTag**, puis ajoutez une balise de texte nommée **FieldService** avec une valeur **SmartBuilding**. Cliquez alors sur **Appliquer** :

[![Ajouter une balise aux appareils Chiller (Refroidisseur), Elevator (Élévateur), et Engine (Moteur)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Création de filtres

Vous pouvez maintenant utiliser les valeurs des étiquettes pour créer des filtres. Dans la page **Appareils**, cliquez sur **Gérer les groupes d’appareils** :

[![Gérer les groupes d’appareils](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Créez un filtre de texte qui utilise le nom de balise **FieldService** et la valeur **SmartBuilding** dans la condition. Enregistrez le filtre sous le nom **Smart Building** :

[![Créer le filtre Smart Building](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Créez un filtre de texte qui utilise le nom de balise **FieldService** et la valeur **ConnectedVehicle** dans la condition. Enregistrer le filtre sous le nom **Connected Vehicle**.

[![Créer le filtre Connected Vehicle](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

L’opérateur Contoso peut désormais interroger les appareils selon l’équipe d’opérations :

[![Créer le filtre Connected Vehicle](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de passer à l’étape suivante du tutoriel, laissez l’accélérateur de solution de surveillance à distance déployé. Pour réduire les coûts d’exécution de l’accélérateur de solution pendant que vous ne l’utilisez pas, vous pouvez arrêter les appareils simulés dans le panneau des paramètres :

[![Suspendre les données de télémétrie](./media/iot-accelerators-remote-monitoring-manage/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/togglesimulation-expanded.png#lightbox)

Vous pouvez redémarrer les appareils simulés lorsque vous êtes prêt à commencer le tutoriel suivant.

Si vous n’avez plus besoin l’accélérateur de solution, supprimez-le à partir de la page [Solutions approvisionnées](https://www.azureiotsolutions.com/Accelerators#dashboard) :

![Supprimer la solution](media/iot-accelerators-remote-monitoring-manage/deletesolution.png)

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a montré comment configurer et gérer les appareils connectés à l’accélérateur de solution de surveillance à distance. Pour découvrir comment utiliser l’accélérateur de solution pour identifier et résoudre les problèmes relatifs à vos appareils connectés, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [Utiliser des alertes d’appareil pour identifier et résoudre les problèmes liés aux appareils connectés à votre solution de surveillance](iot-accelerators-remote-monitoring-maintain.md)
