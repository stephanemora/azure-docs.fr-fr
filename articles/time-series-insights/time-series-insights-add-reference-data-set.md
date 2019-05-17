---
title: Guide d’ajout d’un jeu de données de référence à votre environnement Azure Time Series Insights | Microsoft Docs
description: Cet article décrit comment ajouter un jeu de données de référence pour augmenter le volume de données dans votre environnement Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/13/2019
ms.custom: seodec18
ms.openlocfilehash: c0b9db05effe42880465310f6eb5c38e3e832266
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606910"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Créer un jeu de données de référence pour votre environnement Time Series Insights à l’aide du portail Azure

Cet article décrit comment ajouter un jeu de données de référence à votre environnement Azure Time Series Insights. Les données de référence sont utiles, joignez-les à votre source de données pour augmenter les valeurs.

Un jeu de données de référence est une collection d’éléments qui augmente les événements issus de votre source d’événements. Le moteur d’entrée Time Series Insights associe chaque événement de votre source d’événements à la ligne de données correspondante dans votre jeu de données de référence. Cet événement ajouté est ensuite disponible pour la requête. Cette jointure repose sur les colonnes de clé privée définies dans votre jeu de données de référence.

Les données de référence ne sont pas jointes rétroactivement. Par conséquent, les données d’entrée uniquement actuelles et futures sont mis en correspondance et joint à l’ensemble de date de référence, une fois qu’il a été configuré et téléchargé.

## <a name="video"></a>Vidéo

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>En savoir plus sur le modèle de données de référence de Time Series Insight.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Ajouter un jeu de données de référence

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Recherchez votre environnement Time Series Insights existant. Cliquez sur **Toutes les ressources** dans le menu de gauche du portail Azure. Sélectionnez votre environnement Time Series Insights.

1. Sélectionnez la page **Vue d’ensemble**. Localisez l’**URL de l’explorateur Time Series Insights** et ouvrez le lien.  

   Affichez l’explorateur de votre environnement TSI.

1. Développez le sélecteur d’environnement dans l’explorateur TSI. Choisissez l’environnement actif. Sur la page de l’explorateur, sélectionnez l’icône des données de référence en haut à droite.

   [![Ajouter des données de référence](media/add-reference-data-set/add_reference_data.png)](media/add-reference-data-set/add_reference_data.png#lightbox)

1. Cliquez sur le bouton **+ Ajouter un jeu de données** pour ajouter un nouveau jeu de données.

   [![Ajouter le jeu de données](media/add-reference-data-set/add_data_set.png)](media/add-reference-data-set/add_data_set.png#lightbox)

1. Sur la page **Nouveau jeu de données de référence**, choisissez le format des données :
   - Choisissez **CSV** pour des données délimitées par des virgules. La première ligne est traitée comme une ligne d’en-tête.
   - Choisissez **Tableau JSON** pour des données au format JSON.

   [![Choisissez le format de données.](media/add-reference-data-set/add_data.png)](media/add-reference-data-set/add_data.png#lightbox)

1. Fournissez les données via l’une des deux méthodes suivantes :
   - Collez les données dans l’éditeur de texte. Ensuite, cliquez sur le bouton **Analyser les données de référence**.
   - Cliquez sur le bouton **Choisir un fichier** pour ajouter des données à partir d’un fichier texte local.

   Par exemple, collez des données CSV : [![Données CSV collées](media/add-reference-data-set/csv_data_pasted.png)](media/add-reference-data-set/csv_data_pasted.png#lightbox)

   Par exemple, collez les données d’un tableau JSON : [![Coller des données JSON](media/add-reference-data-set/json_data_pasted.png)](media/add-reference-data-set/json_data_pasted.png#lightbox)

   En cas d’erreur lors de l’analyse des valeurs de données, l’erreur s’affiche en rouge au bas de la page : `CSV parsing error, no rows extracted`.

1. Une fois que les données ont été analysées correctement, une grille de données est affichée. Elle contient les colonnes et les lignes qui représentent les données.  Passez en revue la grille de données pour garantir son exactitude.

   [![Ajouter des données de référence](media/add-reference-data-set/parse_data.png)](media/add-reference-data-set/parse_data.png#lightbox)

1. Passez en revue chaque colonne pour voir le type de données pris par défaut et le modifier si nécessaire.  Sélectionnez le symbole de type de données dans l’en-tête de colonne : **#** pour double (données numériques), **T|F** pour booléen ou **Abc** pour une chaîne.

   [![Choisissez les types de données sur les en-têtes de colonne.](media/add-reference-data-set/choose_datatypes.png)](media/add-reference-data-set/choose_datatypes.png#lightbox)

1. Renommez les en-têtes de colonnes si nécessaire. Le nom de la colonne de clé est nécessaire pour joindre à la propriété correspondante dans votre source d’événements. Assurez-vous que les noms de colonne de clé de données de référence correspondent exactement au nom d’événement pour vos données entrantes, y compris la casse. Les noms des colonnes ne contenant pas de clés sont utilisés pour augmenter les données entrantes avec les valeurs de données de référence correspondantes.

1. Cliquez sur **Ajouter une ligne** ou **Ajouter une colonne** pour ajouter plusieurs valeurs de données de référence, si nécessaire.

1. Saisissez une valeur dans le champ **Filtrer les lignes...**  pour passer en revue des lignes spécifiques en fonction des besoins. Ce filtre est utile pour l’examen des données, mais il n’est pas appliqué lors du chargement des données.

1. Nommez le jeu de données en renseignant le champ **Nom du jeu de données** au-dessus de la grille de données.

    [![Nom du jeu de données.](media/add-reference-data-set/name_reference_dataset.png)](media/add-reference-data-set/name_reference_dataset.png#lightbox)

1. Indiquez la colonne de **clé primaire** du jeu de données en sélectionnant la liste déroulante au-dessus de la grille de données.

    [![Sélectionnez l’ou les colonnes clée.](media/add-reference-data-set/set_primary_key.png)](media/add-reference-data-set/set_primary_key.png#lightbox)

    Si vous le souhaitez, cliquez sur le bouton **+** pour ajouter une colonne de clé secondaire, comme une clé primaire composite. Si vous devez annuler la sélection, choisissez la valeur vide de la liste déroulante pour supprimer la clé secondaire.

1. Pour télécharger les données, cliquez sur le bouton **Télécharger les lignes**.

    [![Upload](media/add-reference-data-set/upload_rows.png)](media/add-reference-data-set/upload_rows.png#lightbox)

    La page confirme la fin du téléchargement et affiche le message **Jeu de données chargé**.

## <a name="next-steps"></a>Étapes suivantes

* [Gérez les données de référence](time-series-insights-manage-reference-data-csharp.md) par programme.

* Pour obtenir des informations de référence d’API complètes, consultez le document [API de données de référence](/rest/api/time-series-insights/ga-reference-data-api).
