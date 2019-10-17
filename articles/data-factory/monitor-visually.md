---
title: Surveiller visuellement des fabriques de données Azure | Microsoft Docs
description: Découvrez comment superviser visuellement des fabriques de données Azure
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: f7c27bde3806684045bc43f8ff99eefb14c8d04a
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029144"
---
# <a name="visually-monitor-azure-data-factories"></a>Surveiller visuellement des fabriques de données Azure
Azure Data Factory est un service d’intégration de données basé sur le cloud. Vous pouvez l’utiliser pour créer des workflows basés sur les données dans le cloud en vue d’orchestrer et automatiser le déplacement et la transformation des données. À l’aide d’Azure Data Factory, vous pouvez :

- Créer et planifier des flux de travail pilotés par les données (appelés pipelines) qui peuvent ingérer des données provenant de différents magasins de données.
- Traiter et transformer les données à l’aide de services de calcul tels qu’Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics et Azure Machine Learning.
- Publier des données de sortie vers des magasins de données tels que Azure SQL Data Warehouse pour que des applications décisionnelles (BI) puissent les utiliser.

Dans ce guide de démarrage rapide, vous allez découvrir comment superviser visuellement des pipelines Data Factory sans écrire une seule ligne de code.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="monitor-data-factory-pipelines"></a>Superviser des pipelines Data Factory

Supervisez les exécutions d’activités et de pipelines avec une interface de vue de liste simple. Toutes les exécutions sont affichées dans le fuseau horaire local du navigateur. Si vous changez le fuseau horaire, tous les champs d’heure et de date sont alignés sur le fuseau horaire sélectionné.  

1. Démarrez Microsoft Edge ou Google Chrome. Actuellement, l’interface utilisateur de Data Factory est prise en charge uniquement par ces deux navigateurs web.
2. Connectez-vous au [portail Azure](https://portal.azure.com/).
3. Accédez au panneau de la fabrique de données créée dans le portail Azure. Sélectionnez la vignette **Superviser et gérer** pour commencer l’expérience de supervision visuelle de Data Factory.

## <a name="monitor-pipeline-runs"></a>Surveiller des exécutions de pipelines
La liste montre chaque exécution de pipeline pour vos pipelines Data Factory. Elle inclut les colonnes suivantes :

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom du pipeline | Nom du pipeline |
| Actions | Action unique disponible pour voir les exécutions d’activités |
| Démarrage de l’exécution | Date et heure de début de l’exécution de pipeline (MM/JJ/AAAA, HH:MM:SS AM/PM) |
| Duration | Durée d’exécution (HH:MM:SS) |
| Déclenché par | Déclenchement manuel ou déclenchement planifié |
| Statut | **Échec**, **Réussite** ou **En cours** |
| parameters | Paramètres d’exécution de pipeline (paires nom/valeur) |
| Error | Erreur d’exécution de pipeline (le cas échéant) |
| ID d’exécution | ID de l’exécution de pipeline |

![Liste pour la supervision des exécutions de pipelines](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Surveiller des exécutions d’activités
La liste montre les exécutions d’activités correspondant à chaque exécution de pipeline. Pour voir les exécutions d’activités pour chaque exécution de pipeline, cliquez sur l’icône **Exécutions de l’activité** sous la colonne **Actions**. La liste inclut les colonnes suivantes :

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom de l’activité | Nom de l’activité dans le pipeline |
| Type d’activité | Type de l’activité, par exemple **Copie**, **HDInsightSpark** ou **HDInsightHive** |
| Démarrage de l’exécution | Date et heure de début de l’exécution de l’activité (MM/JJ/AAAA, HH:MM:SS AM/PM) |
| Duration | Durée d’exécution (HH:MM:SS) |
| Statut | **Échec**, **Réussite** ou **En cours** |
| Entrée | Tableau JSON décrivant les entrées de l’activité |
| Output | Tableau JSON décrivant les sorties de l’activité |
| Error | Erreur d’exécution d’activité (le cas échéant) |

![Liste pour la supervision des exécutions d’activités](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Vous devez cliquer sur le bouton **Actualiser** en haut pour actualiser la liste des exécutions de pipelines et d’activités. L’actualisation automatique n’est pas prise en charge actuellement.

![Bouton Actualiser](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Sélectionner une fabrique de données à surveiller
Placez le pointeur de la souris sur l’icône **Data Factory** en haut à gauche. Cliquez sur l’icône en forme de flèche pour afficher la liste des fabriques de données et des abonnements Azure que vous pouvez superviser.

![Sélectionner la fabrique de données](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Configurer l’affichage de liste

### <a name="apply-rich-ordering-and-filtering"></a>Appliquer un classement et un filtrage enrichis

Classez les exécutions de pipelines selon l’heure de début de l’exécution dans l’ordre chronologique ou chronologique inverse. Filtrez les exécutions de pipelines en utilisant les colonnes suivantes :

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom du pipeline | Nom du pipeline. Les options disponibles comprennent des filtres rapides, par exemple **Dernières 24 heures**, **La semaine dernière** et **Les 30 derniers jours**. Vous pouvez également sélectionner une heure et une date personnalisées. |
| Démarrage de l’exécution | Date et heure de début de l’exécution du pipeline. |
| État de l’exécution | Filtrez les exécutions par état : **Réussite**, **Échec** ou **En cours**. |

![Options de filtrage](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Ajouter ou supprimer des colonnes
Cliquez avec le bouton droit sur l’en-tête de liste et sélectionnez les colonnes que vous souhaitez afficher.

![Options de colonne](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Ajuster les largeurs des colonnes
Augmentez et diminuez les largeurs de colonne dans la liste en plaçant le pointeur sur l’en-tête de colonne.

## <a name="promote-user-properties-to-monitor"></a>Promouvoir les propriétés de l’utilisateur à superviser

Vous pouvez promouvoir n’importe quelle propriété de l’activité de pipeline en tant que propriété d’utilisateur pour pouvoir la surveiller. Ainsi, vous pouvez promouvoir les propriétés **Source** et **Destination** de l’activité de copie dans votre pipeline en tant que propriétés d’utilisateur. Vous pouvez également sélectionner **Générer automatiquement** afin de générer les propriétés d’utilisateur **Source** et **Destination** pour une activité de copie.

![Créer des propriétés d’utilisateur](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Vous pouvez promouvoir jusqu’à cinq propriétés de l’activité de pipeline en tant que propriétés d’utilisateur.

Une fois les propriétés d’utilisateur créées, vous pouvez les superviser dans les listes de supervision. Si la source de l’activité de copie est un nom de table, vous pouvez superviser le nom de table source en tant que colonne dans la liste des exécutions d’activités.

![Liste des exécutions d’activités sans propriétés d’utilisateur](media/monitor-visually/monitor-user-properties-image2.png)

![Ajouter des colonnes à la liste des exécutions d’activités pour les propriétés d’utilisateur](media/monitor-visually/monitor-user-properties-image3.png)

![Liste des exécutions d’activités avec colonnes pour les propriétés d’utilisateur](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Réexécuter des activités à l’intérieur d’un pipeline

Vous pouvez maintenant réexécuter des activités à l’intérieur d’un pipeline. Sélectionnez **Afficher les exécutions d’activités**, puis sélectionnez l’activité dans votre pipeline à partir de laquelle vous souhaitez réexécuter votre pipeline.

![Afficher les exécutions d’activités](media/monitor-visually/rerun-activities-image1.png)

![Sélectionner une exécution d’activité](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Voir l’historique des réexécutions

Vous pouvez voir l’historique des réexécutions de toutes les exécutions du pipeline en mode Liste.

![Afficher l’historique](media/monitor-visually/rerun-history-image1.png)

Vous pouvez également voir l’historique des réexécutions d’une exécution de pipeline spécifique.

![Voir l’historique d’une exécution de pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Vues Gantt

Utilisez des vues Gantt pour visualiser rapidement vos pipelines et vos exécutions d’activités. Vous pouvez afficher la vue Gantt par pipeline ou effectuer un regroupement en fonction d’annotations ou de balises que vous avez créées sur vos pipelines.

![Exemple de diagramme de Gantt](media/monitor-visually/gantt1.png)

![Annotations du diagramme de Gantt](media/monitor-visually/gantt2.png)

La longueur de la barre indique la durée du pipeline. Vous pouvez également sélectionner la barre pour voir plus de détails.

![Durée du diagramme de Gantt](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Visites guidées
Cliquez sur l’icône **Informations** en bas à gauche. Puis, cliquez sur **Visite guidée** pour obtenir des instructions détaillées sur la supervision des exécutions de pipelines et d’activités.

![Visites guidées](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Commentaires
Cliquez sur l’icône **Commentaires** pour nous envoyer vos commentaires sur les différentes fonctionnalités ou les problèmes auxquels vous êtes confrontés.

![Commentaires](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Alertes

Vous pouvez déclencher des alertes sur les métriques prises en charge dans Data Factory. Sélectionnez **Superviser** > **Alertes et métriques** dans la page de supervision de Data Factory pour commencer.

![Page de supervision de Data Factory](media/monitor-visually/alerts01.png)

Pour voir une présentation de sept minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Créez des alertes

1.  Sélectionnez **Nouvelle règle d’alerte** pour créer une alerte.

    ![Bouton Nouvelle règle d’alerte](media/monitor-visually/alerts02.png)

1.  Spécifiez le nom de la règle et sélectionnez la gravité de l’alerte.

    ![Zones pour le nom de la règle et la gravité](media/monitor-visually/alerts03.png)

1.  Sélectionnez les critères d’alerte.

    ![Zone pour les critères cibles](media/monitor-visually/alerts04.png)

    ![Liste des critères](media/monitor-visually/alerts05.png)

1.  Configurez la logique d’alerte. Vous pouvez créer une alerte pour la métrique sélectionnée pour tous les pipelines et activités correspondantes. Vous pouvez également sélectionner un type d’activité, un nom d’activité, un nom de pipeline ou un type d’échec particulier.

    ![Options de configuration de la logique d’alerte](media/monitor-visually/alerts06.png)

1.  Configurez les notifications vocales, push, par e-mail et par SMS pour l’alerte. Choisissez ou créez un groupe d’actions pour les notifications d’alerte.

    ![Options de configuration des notifications](media/monitor-visually/alerts07.png)

    ![Options d’ajout de notification](media/monitor-visually/alerts08.png)

1.  Créez la règle d’alerte

    ![Options de création de règle d’alerte](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la supervision et la gestion des pipelines, consultez l’article [Surveiller et gérer les pipelines par programmation](https://docs.microsoft.com/azure/data-factory/monitor-programmatically).
