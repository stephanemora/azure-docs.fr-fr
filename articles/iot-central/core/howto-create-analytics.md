---
title: Analyser les données d’appareil dans votre application Azure IoT Central | Microsoft Docs
description: Analysez les données d’appareil dans votre application Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 08/16/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2ee6c615556946fa3c28212bc3cf1917c4ef7f2f
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122525903"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Guide pratique pour utiliser une analytique permettant d’analyser les données des appareils

Azure IoT Central fournit des fonctionnalités d’analyse enrichies pour analyser les tendances historiques et mettre en corrélation les données de télémétrie de vos appareils. Pour commencer, sélectionnez **Analytics** du volet de gauche.

## <a name="understand-the-analytics-ui"></a>Comprendre l’interface utilisateur Analytics

L’interface utilisateur Analytics présente trois composants principaux :

- **Panneau de configuration de données :** dans le panneau de configuration, sélectionnez le groupe d’appareils pour lequel vous souhaitez analyser les données. Ensuite, sélectionnez la télémétrie que vous souhaitez analyser et sélectionnez la méthode d’agrégation pour chaque télémétrie. Le contrôle **Diviser par** permet de regrouper les données à l’aide des propriétés de l’appareil en tant que dimensions.

- **Contrôle du temps :** utilisez le contrôle du temps pour sélectionner la durée pendant laquelle vous souhaitez analyser les données. Vous pouvez faire glisser l’une des extrémités du curseur de temps pour sélectionner l’intervalle de temps. Le contrôle du temps présente également un curseur **Taille d’intervalle** qui contrôle le compartiment ou la taille d’intervalle utilisée pour agréger les données.

- **Contrôle de graphique :** le contrôle de graphique visualise les données sous forme de graphique en courbes. Vous pouvez activer ou désactiver la visibilité de lignes spécifiques en interagissant avec la légende du graphique.

  :::image type="content" source="media/howto-create-analytics/analytics-ui.png" alt-text="Capture d’écran montrant les trois zones de l’interface utilisateur Analytics.":::

## <a name="query-your-data"></a>Interroger vos données

Choisissez un **groupe d’appareils** pour commencer, puis les données de télémétrie que vous souhaitez analyser. Quand vous avez terminé, sélectionnez **Analyser** pour commencer à visualiser vos données :

- **Groupe d’appareils :** Un [groupe d’appareils](tutorial-use-device-groups.md) est un groupe de vos appareils défini par l’utilisateur. Par exemple, **Tous les réfrigérateurs de Paris** ou **Toutes les éoliennes version 2.0**.

- **Données de télémétrie :** Sélectionnez les données de télémétrie que vous souhaitez analyser et explorer. Vous pouvez sélectionner plusieurs types de données de télémétrie à analyser ensemble. La méthode d’agrégation par défaut est définie sur **Moyenne** pour les types de données numériques et **Nombre** pour les chaînes. Les méthodes d’agrégation pour les types de données numériques sont **Moyenne**, **Maximum**, **Minimum**, **Nombre** et **Somme**. **Nombre** est la seule méthode d’agrégation pour les chaînes.

    > [!NOTE]
    > Les points de données historiques sont affichés uniquement lorsque les conditions de la requête sont vraies. Par exemple, un appareil a été mis à niveau de **Template1** vers **Template2** hier. Aujourd’hui, si vous interrogez des groupes d’appareils qui contiennent des appareils **Template1**, vous voyez les données d’appareil d’hier et d’avant. Si vous interrogez des groupes d’appareils qui contiennent des appareils **Template2**, vous voyez l’appareil et les données à partir du moment où il a été mis à niveau jusqu’à présent.

- **Diviser par :** le contrôle **Diviser par** permet de regrouper les données à l’aide des propriétés de l’appareil en tant que dimensions. Les données de télémétrie et les propriétés de l’appareil sont combinées avec les propriétés cloud lorsque l’appareil envoie des données. Si une propriété cloud ou de l’appareil est mise à jour, vous voyez les données de télémétrie regroupées par différentes valeurs sur le graphique.

    > [!TIP]
    > Pour afficher les données de chaque appareil séparément, sélectionnez **ID de l’appareil** dans le contrôle **Diviser par**.

## <a name="interact-with-your-data"></a>Interagissez avec vos données

Une fois que vous avez interrogé vos données, vous pouvez les visualiser sur le graphique en courbes. Vous pouvez afficher ou masquer les données de télémétrie, modifier la durée ou afficher les données dans une grille.

- **Panneau de l’éditeur de temps :** par défaut, vous voyez les données du dernier jour. Vous pouvez faire glisser l’une des extrémités du curseur pour modifier l’intervalle de temps. Vous pouvez également utiliser le contrôle de calendrier pour sélectionner un des intervalles de temps prédéfinis ou sélectionner un intervalle de temps personnalisé. Le contrôle de temps présente également un curseur **Taille d’intervalle** qui contrôle la taille d’intervalle utilisée pour agréger les données.

  :::image type="content" source="media/howto-create-analytics/time-editor-panel.png" alt-text="Capture d’écran qui montre le panneau de l’éditeur de temps.":::

  - **Outil de curseur de plage de dates internes** : utilisez les deux contrôles de point de terminaison pour mettre en surbrillance l’intervalle de temps souhaité. La plage de dates interne est limitée par le contrôle de curseur de plage de dates externe.
  
  - **Contrôle de curseur de plage de dates externe** : utilisez les contrôles de point de terminaison pour sélectionner la plage de dates externe, qui est disponible pour votre contrôle de plage de dates interne.

  - **Boutons pour augmenter ou diminuer la plage de dates** : augmentez ou réduisez votre intervalle de temps en sélectionnant l’un des boutons pour l’intervalle souhaité.

  - **Curseur de taille d’intervalle** : utilisez le curseur pour effectuer un zoom avant/arrière sur les intervalles pour la même plage de dates. Ce contrôle fournit un contrôle plus précis du déplacement entre les tranches de temps volumineuses. Vous pouvez l’utiliser pour afficher des vues granulaires de vos données en haute résolution, à jusqu’à la milliseconde près. Le point de départ par défaut du curseur vous donne une vue optimale des données de votre sélection. Cette vue équilibre la résolution, la vitesse des requêtes et la granularité.
  
  - **Sélecteur de plage de dates** : utilisez ce contrôle pour sélectionner les plages de dates et d’heures souhaitées. Vous pouvez également utiliser le contrôle pour basculer entre les différents fuseaux horaires. Une fois que vous avez apporté des modifications, pour les appliquer à votre espace de travail actuel, sélectionnez **Enregistrer**.

  > [!TIP]
  > La taille de l’intervalle est déterminée dynamiquement en fonction de l’intervalle de temps sélectionné. Des délais plus petits vous permettent d’agréger les données en intervalles très granulaires allant jusqu’à quelques secondes.

- **Légende du graphique :** la légende du graphique affiche les données de télémétrie sélectionnées sur le graphique. Pointez sur un élément de la légende pour l’activer sur le graphique. Lorsque vous utilisez **Diviser par**, les données de télémétrie sont regroupées par valeurs de la dimension sélectionnée. Vous pouvez activer ou désactiver la visibilité de chaque type de données de télémétrie ou cliquer sur le nom du groupe pour activer ou désactiver la visibilité du groupe.  

- **Contrôle du format de l’axe Y :** le mode de l’axe Y parcourt les options d’affichage de l’axe Y disponibles. Ce contrôle est disponible uniquement lorsque vous visualisez plusieurs types de données de télémétrie. Les trois modes sont les suivants :

  - **Empilé :** un graphique pour chaque type de données de télémétrie est empilé et chaque graphique a son propre axe Y. Ce mode est la valeur par défaut.
  - **Partagé :** un graphique pour chaque type de données de télémétrie est tracé sur le même axe Y.
  - **Chevaucher :** utilisez ce mode pour empiler plusieurs lignes sur le même axe Y, celui-ci étant modifié en fonction de la ligne sélectionnée.

  :::image type="content" source="media/howto-create-analytics/y-axis-control.png" alt-text="Capture d’écran mettant en évidence le contrôle de l’axe Y.":::

- **Contrôle de zoom :** le contrôle de zoom vous permet d’approfondir vos données. Si vous trouvez une période sur laquelle vous souhaitez vous concentrer dans votre jeu de résultats, utilisez le pointeur de la souris pour mettre en surbrillance la zone. Cliquez ensuite avec le bouton droit sur la zone sélectionnée et sélectionnez **Zoom**.

  :::image type="content" source="media/howto-create-analytics/zoom.png" alt-text="Capture d’écran qui montre l’utilisation du contrôle de zoom.":::

Sélectionnez les points de suspension pour accéder à plus de contrôles de graphique :

- **Afficher la grille :** affichez vos résultats sous la forme d’un tableau qui vous permet d’afficher la valeur de chaque point de données.

- **Télécharger en tant que CSV :** exportez vos résultats sous la forme d’un fichier de valeurs séparées par des virgules (CSV). Ce fichier CSV contient des données pour chaque appareil. Les résultats sont exportés en utilisant l’intervalle et le délai spécifiés.

- **Supprimer un marqueur :** le contrôle **Suppression de marqueur** vous permet d’ancrer certains points de données sur le graphique. Il est utile lorsque vous tentez de comparer des données pour plusieurs lignes sur différentes périodes.

  :::image type="content" source="media/howto-create-analytics/additional-chart-controls.png" alt-text="Capture d’écran montrant comment accéder aux contrôles de graphique supplémentaires.":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à visualiser vos données avec les fonctionnalités d’analyse intégrées, l’étape suivante suggérée est d’apprendre à [Exporter des données IOT vers des destinations cloud à l’aide de l’exportation de données](howto-export-data.md).
