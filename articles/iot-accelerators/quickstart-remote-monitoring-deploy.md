---
title: Essayer une solution cloud IoT de supervision à distance - Azure | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous allez déployer l’accélérateur de solution Monitoring à distance Azure IoT, puis vous connecter pour utiliser le tableau de bord de la solution.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/25/2019
ms.author: dobett
ms.openlocfilehash: 8d007fa7100a9880878e0b748c2c38402e5ea192
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540232"
---
# <a name="quickstart-try-a-cloud-based-remote-monitoring-solution"></a>Démarrage rapide : Essayer une solution de supervision informatique à distance

Ce guide de démarrage rapide vous montre comment déployer l’accélérateur de solution de supervision à distance Azure IoT. Dans cette solution informatique, vous utilisez la page **Tableau de bord** pour visualiser les appareils simulés sur une carte, et la page **Maintenance** pour répondre à une alerte concernant la pression d’un appareil de refroidissement simulé. Vous pouvez utiliser cet accélérateur de solution comme point de départ de votre propre implémentation ou comme outil d’apprentissage.

Le déploiement initial configure l’accélérateur de solution pour une société appelée Contoso. En tant qu’opérateur chez Contoso, vous prenez en charge la gestion d’une sélection de types d’appareil variés, tels que des appareils de refroidissement, déployés dans différents environnements physiques. Un appareil de refroidissement envoie la télémétrie de la température, de l’humidité et de la pression à l’accélérateur de la solution de supervision à distance.

Ce guide de démarrage rapide déploie une version de **base** de l’accélérateur de solution qui réduit les coûts, à des fins de test et de démonstration. Pour plus d’informations sur les différentes versions que vous pouvez déployer, consultez [Déploiements de base et standard](iot-accelerators-remote-monitoring-deploy-cli.md#basic-and-standard-deployments).

Pour suivre ce guide de démarrage rapide, vous devez avoir un abonnement Azure actif.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="deploy-the-solution"></a>Déployer la solution

Quand vous déployez l’accélérateur de solution dans votre abonnement Azure, vous devez définir des options de configuration.

Connectez-vous à [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) avec les informations d’identification de votre compte Azure.

Cliquez sur la vignette **Supervision à distance**. Dans la page **Supervision à distance**, cliquez sur **Essayer maintenant** :

![Choisir Monitoring à distance](./media/quickstart-remote-monitoring-deploy/remotemonitoring.png)

Choisissez **Microservices C#** comme **Options de déploiement**. Les implémentations Java et C# ont les mêmes fonctionnalités.

Entrez un **Nom de la solution** unique pour votre accélérateur de solution de supervision à distance. Pour ce guide de démarrage rapide, nous appelons le nôtre **contoso-rm**.

Sélectionnez l’**Abonnement** et la **Région** à utiliser pour déployer l’accélérateur de solution. En général, vous choisissez la région la plus proche. Pour ce guide de démarrage rapide, nous utilisons **USA Est**.
Vous pouvez choisir **Visual Studio Enterprise** à la condition d’être [administrateur ou utilisateur général](iot-accelerators-permissions.md).

Pour commencer le déploiement, cliquez sur **Créer**. L’exécution de ce processus prend au moins cinq minutes :

![Détails de la solution de supervision à distance](./media/quickstart-remote-monitoring-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Se connecter à la solution

Lorsque le déploiement vers votre abonnement Azure est terminé, vous pouvez observer une coche verte ainsi que la mention **Ready** sur la vignette de la solution. À présent, vous pouvez vous connecter au tableau de bord de l’accélérateur de solution de supervision à distance.

Dans la page **Solutions approvisionnées**, cliquez sur votre nouvel accélérateur de solution de supervision à distance :

![Choisir la nouvelle solution](./media/quickstart-remote-monitoring-deploy/choosenew.png)

Vous pouvez consulter les informations relatives à votre accélérateur de solution de supervision à distance dans le panneau qui s’affiche. Choisissez **Accéder à votre accélérateur de solution** pour voir votre accélérateur de solution de supervision à distance :

![Panneau de solutions](./media/quickstart-remote-monitoring-deploy/solutionpanel.png)

Cliquez sur **Accepter** pour accepter la demande d’autorisation. Le tableau de bord de la solution de supervision à distance s’affiche dans votre navigateur :

[![Tableau de bord des solutions](./media/quickstart-remote-monitoring-deploy/solutiondashboard-inline.png)](./media/quickstart-remote-monitoring-deploy/solutiondashboard-expanded.png#lightbox)

## <a name="view-your-devices"></a>Voir vos appareils

Le tableau de bord de la solution affiche les informations suivantes sur les appareils simulés de Contoso :

* Le panneau **Statistiques de l’appareil** affiche un récapitulatif des alertes et le nombre total d’appareils. Dans le déploiement par défaut, Contoso compte 10 appareils simulés de types différents.

* Le panneau **Emplacements de l’appareil** montre où se trouvent physiquement vos appareils. La couleur de l’épingle montre quand l’appareil présente des alertes.

* Le panneau **Alertes** affiche les détails des alertes de vos appareils.

* Le panneau **Télémétrie** affiche la télémétrie de vos appareils. Vous pouvez voir les différents flux de télémétrie en cliquant sur les types de télémétrie en haut.

* Le panneau **Analytique** montre des informations combinées sur les alertes de vos appareils.

## <a name="respond-to-an-alert"></a>Répondre à une alerte

En tant qu’opérateur chez Contoso, vous pouvez surveiller vos appareils à partir du tableau de bord de la solution. Le panneau **Statistiques de l’appareil** affiche un nombre d’alertes critiques, tandis que le panneau **Alertes** montre que la plupart d’entre elles proviennent d’un appareil de refroidissement. Pour les appareils de refroidissement de Contoso, une pression interne de plus de 250 PSI indique que l’appareil ne fonctionne pas correctement.

### <a name="identify-the-issue"></a>Identifier le problème

Dans la page **Tableau de bord**, dans le panneau **Alertes**, vous pouvez voir l’alerte **Chiller pressure too high** (Pression de refroidissement trop élevée). L’appareil de refroidissement est marqué d’une épingle rouge sur la carte (vous devrez peut-être faire un panoramique et zoomer sur la carte) :

[![Tableau de bord montrant l’alerte de pression et l’appareil sur la carte](./media/quickstart-remote-monitoring-deploy/dashboardalarm-inline.png)](./media/quickstart-remote-monitoring-deploy/dashboardalarm-expanded.png#lightbox)

Dans le panneau **Alertes**, cliquez sur **...** dans la colonne **Explorer** à côté de la règle **Chiller pressure too high** (Pression de refroidissement trop élevée). Cette action vous conduit à la page **Maintenance**, où vous pouvez voir les détails de la règle qui a déclenché l’alerte.

La page de maintenance **Chiller pressure too high** montre les détails de la règle qui a déclenché les alertes. La page liste également l’heure des alertes et l’appareil qui les a déclenchées :

[![Page de maintenance montrant la liste des alertes qui ont été déclenchées](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-expanded.png#lightbox)

Vous avez maintenant identifié le problème qui a déclenché l’alerte, ainsi que l’appareil associé. En tant qu’opérateur, vous devez ensuite reconnaître l’alerte et résoudre le problème.

### <a name="fix-the-issue"></a>Résoudre le problème

Pour indiquer aux autres opérateurs que vous travaillez à la résolution de l’alerte, sélectionnez-la et définissez **État d’alerte** sur **Reconnu** :

[![Sélectionner et reconnaître l’alerte](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-expanded.png#lightbox)

La valeur dans la colonne d’état devient **Reconnu**.

Pour intervenir sur l’appareil de refroidissement, faites défiler vers le bas jusqu’à **Informations connexes**, sélectionnez l’appareil de refroidissement dans la liste **Appareils avec alerte**, puis choisissez **Travaux** :

[![Sélectionner l’appareil et planifier une action](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-expanded.png#lightbox)

Dans le panneau **Travaux**, sélectionnez la **méthode Run**, puis la méthode **EmergencyValveRelease**. Ajouter le nom du travail **ChillerPressureRelease**, puis cliquez sur **Appliquer**. Ces paramètres créent, pour vous, un travail qui s’exécute immédiatement.

Pour voir l’état du travail, revenez à la page **Maintenance** et consultez la liste des travaux dans la vue **Travaux**. Vous devrez peut-être attendre quelques secondes avant de pouvoir voir que le travail a été exécuté :

[![État des travaux dans la vue Travaux](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-expanded.png#lightbox)

### <a name="check-the-pressure-is-back-to-normal"></a>Vérifier que la pression est revenue à la normale

Pour voir la télémétrie de la pression de l’appareil de refroidissement, accédez à la page **Tableau de bord**, sélectionnez **Pression** dans le panneau de la télémétrie, puis confirmez que la pression de **chiller-02.0** est revenue à la normale :

[![Pression revenue à la normale](./media/quickstart-remote-monitoring-deploy/pressurenormal-inline.png)](./media/quickstart-remote-monitoring-deploy/pressurenormal-expanded.png#lightbox)

Pour fermer l’incident, accédez à la page **Maintenance**, sélectionnez l’alerte et définissez l’état sur **Fermé** :

[![Sélectionner et fermer l’alerte](./media/quickstart-remote-monitoring-deploy/maintenanceclose-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceclose-expanded.png#lightbox)

La valeur dans la colonne d’état devient **Fermé**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de passer aux tutoriels, laissez l’accélérateur de solution de supervision à distance déployé.

Si vous n’avez plus besoin de l’accélérateur de solution, supprimez-le de la page [Solutions approvisionnées](https://www.azureiotsolutions.com/Accelerators#dashboard) en le sélectionnant et en cliquant sur **Supprimer la solution** :

![Capture d’écran montrant la page « Solutions approvisionnées », avec l’accélérateur de solution en surbrillance.](media/quickstart-remote-monitoring-deploy/deletesolution.png)


![Supprimer la solution](media/quickstart-remote-monitoring-deploy/deletesolution-page.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé l’accélérateur de solution de supervision à distance et effectué une tâche de supervision en utilisant des appareils simulés dans le déploiement de Contoso par défaut.

Pour en savoir plus sur l’accélérateur de solution à l’aide d’appareils simulés, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [Tutoriel : Surveiller vos appareils IoT](iot-accelerators-remote-monitoring-monitor.md)