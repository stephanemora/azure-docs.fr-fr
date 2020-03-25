---
title: Effectuer une analyse de cause racine sur une alerte - Azure | Microsoft Docs
description: Dans ce tutoriel, vous découvrez comment effectuer une analyse de cause racine sur une alerte avec Azure Time Series Insights.
author: Philmea
ms.author: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: c3cb940583677d813873c07fbfa679fdcc1dff59
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565466"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>Tutoriel : Effectuer une analyse de cause racine sur une alerte

Dans ce tutoriel, vous découvrez comment utiliser l’accélérateur de solution de supervision à distance pour diagnostiquer la cause racine d’une alerte. Vous voyez qu’une alerte a été déclenchée dans le tableau de bord de la solution de supervision à distance, puis vous utilisez l’Explorateur Azure Time Series Insights pour en rechercher la cause racine.

Le tutoriel utilise deux appareils simulés sur des camions de livraison, qui envoient des données de télémétrie sur l’emplacement, l’altitude, la vitesse et la température de la cargaison. Les camions sont gérés par une entreprise appelée Contoso et sont connectés à l’accélérateur de solution de supervision à distance. En tant qu’opérateur de Contoso, vous devez comprendre pourquoi un de vos camions (delivery-truck-02) a signalé une alerte de température basse.

Dans ce tutoriel, vous allez :

>[!div class="checklist"]
> * Filtrer les appareils dans le tableau de bord
> * Afficher les données de télémétrie en temps réel
> * Explorer des données avec l’Explorateur Time Series Insights
> * Effectuer une analyse de cause racine
> * Créer une règle en fonction de ce que vous avez appris

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Choisir les appareils à afficher

Pour sélectionner les appareils connectés à afficher dans la page **Tableau de bord**, utilisez des filtres. Pour afficher uniquement les appareils de type **Truck** (camion), choisissez le filtre prédéfini **Trucks** (Camions) dans la liste déroulante :

[![Filtrer les camions sur le tableau de bord](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

Quand vous appliquez un filtre, seuls les appareils qui répondent aux conditions du filtre sont affichés sur la carte et dans le panneau de télémétrie du **Tableau de bord**. Vous pouvez constater que deux camions sont connectés à l’accélérateur de solution, notamment **truck-02**.

## <a name="view-real-time-telemetry"></a>Afficher les données de télémétrie en temps réel

L’accélérateur de solution trace les données de télémétrie en temps réel dans le graphique de la page **Tableau de bord**. Par défaut, le graphique montre la télémétrie de l’altitude, qui varie dans le temps :

[![Tracé de la télémétrie de l’altitude des camions](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

Pour voir les données de télémétrie de température des camions, cliquez sur **Temperature** (Température) dans le **panneau Télémétrie**. Vous pouvez voir les variations de température des deux camions au cours des dernières 15 minutes. Vous pouvez également voir qu’une alerte de température basse a été déclenchée pour delivery-truck-02 dans le volet des alertes.

[![Tableau de bord Surveillance à distance avec une alerte de température basse](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>Exploration des données

Pour identifier la cause de l’alarme de température basse, ouvrez les données de télémétrie du camion de livraison dans l’Explorateur Time Series Insights. Cliquez sur un des liens **Explorer dans Time Series Insights** dans le tableau de bord :

[![Tableau de bord Surveillance à distance avec des liens Time Series Insights mis en surbrillance](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

Quand l’Explorateur démarre, vous voyez tous vos appareils listés :

[![Vue initiale de l’Explorateur Time Series Insights](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Filtrez les appareils en tapant **delivery-truck** dans la zone de filtre, puis sélectionnez **Température** comme **Mesure** dans le volet de gauche :

[![Température du camion dans l’Explorateur Time Series Insights](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

Vous voyez la même vue que celle que vous avez vue dans le tableau de bord Supervision à distance. Vous pouvez désormais faire un zoom plus près de l’intervalle de temps dans lequel l’alerte a été déclenchée :

[![Zoom dans l’Explorateur Time Series Insights](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

Vous pouvez également ajouter d’autres flux de données de télémétrie provenant des camions. Cliquez sur le bouton **Ajouter** dans le coin supérieur gauche. Un nouveau volet apparaît :

[![Explorateur Time Series Insights avec un nouveau volet](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

Dans le nouveau volet, remplacez le nom de la nouvelle étiquette par **Devices** (Appareils) pour la faire correspondre à la précédente. Sélectionnez **altitude** comme **Mesure** et **iothub-connection-device-id** comme valeur pour **Diviser par** pour ajouter les données de télémétrie d’altitude dans votre vue :

[![Explorateur Time Series Insights avec la température et l’altitude](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>Diagnostiquer l’alerte

Quand vous regardez les flux dans la vue actuelle, vous pouvez voir que les profils d’altitude des deux camions sont différents. Ainsi, la chute de température dans **delivery-truck-02** se produit quand le camion atteint une altitude élevée. Vous êtes surpris par cette découverte, car il était prévu que les camions prennent la même route.

Pour vérifier votre soupçon que les camions ont suivi des trajets différents, ajoutez un autre volet au panneau latéral en utilisant le bouton **Ajouter**. Dans le nouveau volet, remplacez le nom de la nouvelle étiquette par **Devices** (Appareils) pour la faire correspondre à la précédente. Sélectionnez **Longitude** comme **Mesure** et **iothub-connection-device-id** comme valeur pour **Diviser par** pour ajouter les données de télémétrie de la longitude dans votre vue. Vous pouvez voir que les camions ont suivi des trajets différents en examinant la différence entre les flux de **longitude** :

[![Explorateur Time Series Insights avec la température, l’altitude et la longitude](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Créer une nouvelle règle

Même si les trajets des camions sont généralement optimisés à l’avance, vous vous rendez compte que le trafic, la météo et d’autres événements imprévisibles peuvent entraîner des retards. Vous laissez donc les chauffeurs des camions décider au dernier moment du trajet qui leur paraît être le meilleur. Cependant, comme la température de votre cargaison à l’intérieur du véhicule est critique, vous devez créer une règle supplémentaire dans votre solution de supervision à distance. Cette règle vous permet de recevoir un avertissement si l’altitude moyenne, sur un intervalle de 1 minute, dépasse 350 pieds (environ 106 mètres) :

[![Onglet des règles de surveillance à distance - Définir une règle d’altitude](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

Pour découvrir comment créer et modifier des règles, consultez le tutoriel précédent sur la [détection des problèmes des appareils](iot-accelerators-remote-monitoring-automate.md).

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a montré comment utiliser l’Explorateur Time Series Insights avec l’accélérateur de solution de supervision à distance pour diagnostiquer la cause racine d’une alerte. Pour découvrir comment utiliser l’accélérateur de solution pour identifier et résoudre les problèmes relatifs à vos appareils connectés, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [Utiliser des alertes d’appareil pour identifier et résoudre les problèmes liés aux appareils connectés à votre solution de supervision](iot-accelerators-remote-monitoring-maintain.md)
