---
title: Générer des cartes
description: Décrit comment générer des cartes dans FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 635431fb87e5f164f92ab4b7a1027ee96e9d801a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891027"
---
# <a name="generate-maps"></a>Générer des cartes

Vous pouvez générer les cartes suivantes dans Azure FarmBeats à l’aide de l’imagerie satellite et des entrées de données des capteurs. Les cartes vous permettent de voir l’emplacement géographique de votre ferme et de déterminer l’emplacement approprié de vos appareils.

  -  La carte d’emplacement des capteurs **Sensor Placement** fournit des recommandations sur le nombre de capteurs à utiliser dans votre ferme et vous aide à déterminer leur emplacement.
  - La carte d’indices satellite **Satellite Indices** indique l’indice de végétation et l’indice de teneur en eau pour une ferme.
  - La carte d’humidité du sol **Soil Moisture** indique la distribution de l’humidité du sol en fusionnant les données satellite et les données des capteurs.

## <a name="sensor-placement-maps"></a>Cartes d’emplacement des capteurs

La carte d’emplacement des capteurs de FarmBeats vous aide à placer les capteurs d’humidité du sol. La sortie de la carte offre une liste de coordonnées pour le déploiement des capteurs. Les entrées de ces capteurs sont utilisées avec l’imagerie satellite pour générer la carte thermique d’humidité du sol.  

Cette carte est obtenue par segmentation de la couverture terrestre, observée à différentes dates tout au long de l’année. Même les sols nus et les bâtiments sont inclus dans la couverture terrestre. Vous pouvez supprimer les capteurs à un emplacement où ils ne sont pas nécessaires. Grâce aux renseignements fournis par cette carte, vous pouvez légèrement ajuster le nombre et la position des capteurs selon vos propres connaissances (l’ajout de capteurs n’altérera pas les résultats de la carte thermique d’humidité du sol, mais le retrait de capteurs peut nuire à sa précision).  

## <a name="before-you-begin"></a>Avant de commencer  

Vérifiez les éléments suivants avant de générer une carte d’emplacement des capteurs :

- La surface de la ferme doit être supérieure à une acre.
- Le nombre de scènes Sentinel sans nuage doit être supérieur à six pour la plage de dates sélectionnée.  
- Au moins six scènes Sentinel sans nuage doivent présenter un indice NDVI supérieur ou égal à 0,3.

    > [!NOTE]
    > Sentinel n’autorise que deux threads simultanés par utilisateur. Les travaux sont donc mis en file d’attente, et leur exécution peut prendre plus de temps.

### <a name="dependencies-on-sentinel"></a>Dépendances à Sentinel  

Les dépendances à Sentinel sont les suivantes :

- Nous dépendons des performances de Sentinel pour le téléchargement des images satellite. Vérifiez les [activités](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome) de maintenance l’état des performances de Sentinel.
- Sentinel n’autorise que deux [threads de téléchargement](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) simultanés par utilisateur.
- La génération de cartes de précision sera affectée par [la fréquence de répétitivité et la couverture de Sentinel]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-sensor-placement-map"></a>Créer une carte d’emplacement des capteurs
Cette section décrit en détail les procédures de création de cartes d’emplacement des capteurs.

> [!NOTE]
> Vous pouvez lancer la création d’une carte d’emplacement des capteurs à partir de la page **Maps** ou du menu déroulant **Generate Precision Maps** (Générer des cartes de précision) de la page **Farm Details**.

Procédez comme suit :

1. Dans la page d’accueil, sélectionnez **Maps** dans le menu de navigation de gauche.
2. Sélectionnez **Create Maps**, puis, dans le menu déroulant, sélectionnez **Sensor Placement**.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Sélectionnez **Sensor Placement**.
  La fenêtre Sensor Placement s’affiche.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Sélectionnez une ferme dans le menu déroulant **Farm**.  
   Pour rechercher et sélectionner votre ferme, vous pouvez faire défiler la liste déroulante ou taper le nom de la ferme dans la zone de texte.
5. Si vous souhaitez générer une carte pour l’année passée, sélectionnez l’option **Recommended**.
6. Si vous souhaitez générer une carte pour une plage de dates personnalisée, sélectionnez **Select Date Range** et indiquez les dates de début et de fin de la plage pour laquelle vous souhaitez générer la carte d’emplacement des capteurs.
7. Sélectionnez **Generate Maps** (Générer les cartes).
 Un message de confirmation contenant des informations détaillées sur le travail s’affiche.

  Pour plus d’informations sur l’état du travail, consultez la section **Voir les travaux**. Si le travail a l’état *Failed* (Échec), un message d’erreur détaillé s’affiche dans l’info-bulle correspondante. Dans ce cas, répétez les étapes décrites ci-dessus, puis réessayez.

  Si le problème persiste, consultez la section [Résolution des problèmes](troubleshoot-project-farmbeats.md) ou contactez le [forum Azure FarmBeats](https://aka.ms/FarmBeatsMSDN) en fournissant les journaux appropriés pour obtenir de l’aide.

### <a name="view-and-download-sensor-placement-map"></a>Voir et télécharger une carte d’emplacement des capteurs

Procédez comme suit :

1. Dans la page d’accueil, sélectionnez **Maps** dans le menu de navigation de gauche.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Sélectionnez **Filter** dans le menu de navigation de gauche de la fenêtre.
  La fenêtre **Filter** présente des critères de recherche.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Sélectionnez le type, la date et le nom dans les menus déroulants **Type**, **Date** et **Name**, puis sélectionnez **Apply** pour rechercher la carte à afficher.
  La date de création du travail est indiquée au format type_nomferme_AAAA-MM-JJ.
4. Faites défiler la liste des cartes disponibles à l’aide des barres de navigation en bas de la page.
5. Sélectionnez la carte à afficher. Une fenêtre indépendante affiche l’aperçu de la carte sélectionnée.
6. Sélectionnez **Download** pour télécharger le fichier GeoJSON contenant les coordonnées des capteurs.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Carte d’indices satellite

Les sections suivantes décrivent les procédures à suivre pour créer et afficher une carte d’indices satellite.

> [!NOTE]
> Vous pouvez lancer la création d’une carte d’indices satellite à partir de la page **Maps** ou du menu déroulant **Generate Precision Maps** (Générer des cartes de précision) de la page **Farm Details**.

FarmBeats vous offre la possibilité de générer des cartes d’indice de végétation par différence normalisée (NDVI, Normalized Difference Vegetation Index), d’indice de végétation amélioré (EVI, Enhanced Vegetation Index) et d’indice de teneur en eau par différence normalisée (NDWI, Normalized Difference Water Index) pour les fermes. Ces indices vous permettent d’évaluer la croissance actuelle et antérieure des cultures et les teneurs en eau représentatives du sol.


> [!NOTE]
> Une image Sentinel est requise pour les jours sur lesquels porte la génération de la carte.


## <a name="create-satellite-indices-map"></a>Créer une carte d’indices satellite

Procédez comme suit :

1. Dans la page d’accueil, sélectionnez **Maps** dans le menu de navigation de gauche.
2. Sélectionnez **Create Maps**, puis, dans le menu déroulant, sélectionnez **Satellite Indices**.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Sélectionnez **Satellite Indices**.
  La fenêtre Satellite Indices s’affiche.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Sélectionnez une ferme dans le menu déroulant.  
   Pour rechercher et sélectionner votre ferme, vous pouvez faire défiler la liste déroulante ou taper le nom de la ferme.   
5. Si vous souhaitez générer une carte pour la semaine passée, sélectionnez l’option **This Week**.
6. Si vous souhaitez générer une carte pour une plage de dates personnalisée, sélectionnez **Select Date Range** et indiquez les dates de début et de fin de la plage pour laquelle vous souhaitez générer la carte d’indices satellite.
7. Sélectionnez **Generate Maps** (Générer les cartes).
    Un message de confirmation contenant des informations détaillées sur le travail s’affiche.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Pour plus d’informations sur l’état du travail, consultez la section **Voir les travaux**. Si le travail a l’état *Failed* (Échec), un message d’erreur détaillé s’affiche dans l’info-bulle correspondante. Dans ce cas, répétez les étapes décrites ci-dessus, puis réessayez.

    Si le problème persiste, consultez la section [Résolution des problèmes](troubleshoot-project-farmbeats.md) ou contactez le [forum Azure FarmBeats](https://aka.ms/FarmBeatsMSDN) en fournissant les journaux appropriés pour obtenir de l’aide.

### <a name="view-and-download-map"></a>Afficher et télécharger une carte

Procédez comme suit :

1. Dans la page d’accueil, sélectionnez **Maps** dans le menu de navigation de gauche.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Sélectionnez **Filter** dans le menu de navigation de gauche de la fenêtre. La fenêtre **Filter** affiche des critères de recherche.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Sélectionnez le type, la date et le nom dans les menus déroulants **Type**, **Date** et **Name**, puis sélectionnez **Apply** pour rechercher la carte à afficher.
  La date de création du travail est indiquée au format type_nomferme_AAAA-MM-JJ.

4. Faites défiler la liste des cartes disponibles à l’aide des barres de navigation en bas de la page.
5. Pour chaque combinaison de nom et de date (champs **Farm Name** et **Date**), les trois cartes suivantes sont disponibles :
    - NDVI
    - EVI
    - NDWI
6. Sélectionnez la carte à afficher. Une fenêtre indépendante affiche l’aperçu de la carte sélectionnée.
7. Sélectionnez le menu déroulant **Download** pour choisir le format de téléchargement. La carte est téléchargée et stockée dans le dossier local de votre ordinateur.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="get-soil-moisture-heatmap"></a>Obtenir une carte thermique d’humidité du sol

L’humidité du sol fait référence à l’eau maintenue dans les espaces entre les particules de sol. La carte thermique d’humidité du sol vous permet de comprendre les données d’humidité du sol dans vos fermes à l’aide d’images haute résolution, quelle que soit la profondeur. Pour générer une carte thermique d’humidité du sol précise et exploitable, vous devez déployer des capteurs du même fournisseur de façon uniforme. Les méthodes de mesure de l’humidité du sol et l’étalonnage diffèrent selon les fournisseurs. La carte thermique est générée pour une profondeur spécifique à l’aide des capteurs déployés à cette profondeur.

### <a name="before-you-begin"></a>Avant de commencer  

Vérifiez les éléments suivants avant de générer une carte thermique d’humidité du sol :

- Au moins trois capteurs d’humidité du sol doivent être déployés. Microsoft recommande de ne pas créer de carte d’humidité du sol avant de déployer les capteurs et de les associer à la ferme.  
- La génération d’une carte d’humidité du sol dépend de la couverture du chemin d’accès, de la couverture nuageuse et de l’ombre des nuages de Sentinel. Au moins une scène Sentinel sans nuage doit être disponible pour les 120 jours précédant le jour de demande de la carte d’humidité du sol.
- Au moins la moitié des capteurs déployés sur la ferme doit être en ligne et assurer le streaming des données vers le hub de données.
- Les capteurs fournissant les mesures pour la génération de la carte thermique doivent provenir du même fournisseur.


## <a name="create-soil-moisture-heatmap"></a>Créer une carte thermique d’humidité du sol

Procédez comme suit :

1. Dans la page d’accueil, sélectionnez **Maps** dans le menu de navigation de gauche pour afficher la page Maps.
2. Sélectionnez **Create Maps**, puis, dans le menu déroulant, sélectionnez **Soil Moisture**.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Sélectionnez **Soil Moisture**.
    La fenêtre Soil Moisture s’affiche.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Sélectionnez une ferme dans le menu déroulant **Farm**.  
   Pour rechercher et sélectionner votre ferme, vous pouvez faire défiler la liste déroulante ou taper le nom de la ferme dans le menu déroulant Select Farm.
5. Dans le menu déroulant **Select Soil Moisture Sensor Measure**, sélectionnez la mesure (profondeur) des capteurs d’humidité du sol pour laquelle vous souhaitez générer la carte.
Pour trouver la mesure de capteur, sous **Sensors** (Capteurs), sélectionnez un capteur d’humidité du sol. Ensuite, dans la section **Sensor Properties**, utilisez la valeur appropriée sous **Measure Name**.
6. Si vous souhaitez générer une carte pour le jour ou la semaine en cours, sélectionnez respectivement **Today** ou **This Week**.
7. Si vous souhaitez générer une carte pour une plage de dates personnalisée, sélectionnez **Select Date Range** et indiquez les dates de début et de fin de la plage pour laquelle vous souhaitez générer la carte d’humidité du sol.
8. Sélectionnez **Generate Maps** (Générer les cartes).
 Un message de confirmation contenant des informations détaillées sur le travail s’affiche.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Pour plus d’informations sur l’état du travail, consultez la section **Voir les travaux**. Si le travail a l’état *Failed* (Échec), un message d’erreur détaillé s’affiche dans l’info-bulle correspondante. Dans ce cas, répétez les étapes décrites ci-dessus, puis réessayez.

    Si le problème persiste, consultez la section [Résolution des problèmes](troubleshoot-project-farmbeats.md) ou contactez le [forum Azure FarmBeats](https://aka.ms/FarmBeatsMSDN) en fournissant les journaux appropriés pour obtenir de l’aide.

### <a name="view-and-download-map"></a>Afficher et télécharger une carte

Procédez comme suit :

1. Dans la page d’accueil, sélectionnez **Maps** dans le menu de navigation de gauche.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Sélectionnez **Filter** dans le menu de navigation de gauche de la fenêtre. La fenêtre **Filter**, qui vous permet de rechercher des cartes, s’affiche.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Sélectionnez le type, la date et le nom dans les menus déroulants **Type**, **Date** et **Name**, puis sélectionnez **Apply** pour rechercher la carte à afficher. La date de création du travail est indiquée au format type_nomferme_AAAA-MM-JJ.
4. Sélectionnez l’icône de **tri** en regard des en-têtes du tableau pour effectuer un tri sur les critères Farm, Date, Created On, Job ID et Job Type.
5. Faites défiler la liste des cartes disponibles à l’aide des boutons de navigation en bas de la page.
6. Sélectionnez la carte à afficher. Une fenêtre indépendante affiche l’aperçu de la carte sélectionnée.
7. Sélectionnez le menu déroulant **Download** pour choisir le format de téléchargement. La carte est téléchargée et stockée dans le dossier spécifié.

    ![Projet FarmBeats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
