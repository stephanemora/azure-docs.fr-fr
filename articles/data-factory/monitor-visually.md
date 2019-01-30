---
title: Surveiller visuellement des fabriques de données Azure | Microsoft Docs
description: Découvrez comment surveiller visuellement des fabriques de données Azure
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: shlo
ms.openlocfilehash: df684860cd3d1b6a002a300682ca4c6398461ba6
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2019
ms.locfileid: "54409960"
---
# <a name="visually-monitor-azure-data-factories"></a>Surveiller visuellement des fabriques de données Azure
Azure Data Factory est un service d’intégration de données basé sur le cloud qui vous permet de créer des flux de travail orientés données dans le cloud pour orchestrer et automatiser le déplacement et la transformation des données. Grâce à Azure Data Factory, vous pouvez créer et planifier des flux de travail orientés données (appelés pipelines) capables d’ingérer des données provenant de différents magasins de données, de traiter/transformer les données à l’aide de services de calcul comme Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics et Azure Machine Learning, et de publier des données de sortie dans des magasins de données tels qu’Azure SQL Data Warehouse pour que des applications décisionnelles (BI) puissent les utiliser.

Dans ce guide de démarrage rapide, vous allez découvrir comment superviser visuellement des pipelines Data Factory sans écrire une seule ligne de code.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="monitor-data-factory-pipelines"></a>Superviser des pipelines Data Factory

Surveillez les exécutions d’activités et de pipelines avec une interface d’affichage de liste simple. Toutes les exécutions sont affichées dans le fuseau horaire du navigateur local. Vous pouvez changer le fuseau horaire. Dans ce cas, tous les champs d’heure et de date sont alignés sur le fuseau horaire sélectionné.  

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
2. Connectez-vous au [portail Azure](https://portal.azure.com/).
3. Accédez au panneau de fabrique de données créé dans le portail Azure, puis cliquez sur la vignette « Superviser et gérer » pour lancer l’expérience de supervision visuelle de Data Factory.

## <a name="monitor-pipeline-runs"></a>Surveiller des exécutions de pipelines
Liste montrant chaque exécution de pipeline pour vos pipelines Data Factory v2. Les colonnes affichées sont les suivantes :

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom du pipeline | Nom du pipeline. |
| Actions | Action unique disponible pour afficher les exécutions d’activités. |
| Démarrage de l’exécution | Heure et date de début de l’exécution de pipeline (MM/JJ/AAAA, HH:MM:SS AM/PM) |
| Duration | Durée d’exécution (HH:MM:SS) |
| Déclenché par | Déclenchement manuel, déclenchement planifié |
| Statut | Échec, Réussite, En cours |
| parameters | Paramètres d’exécution de pipeline (paires nom/valeur) |
| Error | Erreur d’exécution de pipeline (le cas échéant) |
| ID d’exécution | ID de l’exécution de pipeline |

![Surveiller des exécutions de pipelines](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Surveiller des exécutions d’activités
Liste montrant les exécutions d’activités correspondant à chaque exécution de pipeline. Cliquez sur l’icône **Exécution de l’activité** sous la colonne **Actions** pour afficher les exécutions d’activités pour chaque exécution de pipeline. Les colonnes affichées sont les suivantes :

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom de l’activité | Nom de l’activité dans le pipeline. |
| Type d’activité | Type de l’activité, telle que Copie, HDInsightHive, HDInsightSpark, et ainsi de suite. |
| Démarrage de l’exécution | Heure et date de début de l’exécution d’activité (MM/JJ/AAAA, HH:MM:SS AM/PM) |
| Duration | Durée d’exécution (HH:MM:SS) |
| Statut | Échec, Réussite, En cours |
| Entrée | Tableau JSON décrivant les entrées de l’activité |
| Sortie | Tableau JSON décrivant les sorties de l’activité |
| Error | Erreur d’exécution d’activité (le cas échéant) |

![Surveiller des exécutions d’activités](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Vous devez cliquer sur l’icône **Actualiser** en haut pour actualiser la liste des exécutions de pipelines et d’activités. L’actualisation automatique n’est pas prise en charge actuellement.

![Actualiser](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Sélectionner une fabrique de données à surveiller
Placez le curseur sur l’icône **Data Factory** en haut à gauche. Cliquez sur l’icône « Flèche » pour afficher la liste des fabriques de données et des abonnements Azure que vous pouvez surveiller.

![Sélectionner une fabrique de données](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Configurer l’affichage de liste

### <a name="apply-rich-ordering-and-filtering"></a>Appliquer un classement et un filtrage enrichis

Vous pouvez classer les exécutions de pipelines par ordre croissant ou décroissant de Démarrage de l’exécution, et filtrer les exécutions de pipelines par les colonnes suivantes :

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom du pipeline | Nom du pipeline. Il existe des options de filtre rapide comme « Dernières 24 heures », « La semaine dernière », « Les 30 derniers jours », et vous pouvez aussi sélectionner une heure et une date personnalisées. |
| Démarrage de l’exécution | Heure et date de début de l’exécution de pipeline. |
| État de l’exécution | Filtrer les exécutions par état (Réussite, Échec ou En cours) |

![Filtrer](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Ajouter ou supprimer des colonnes
Cliquez avec le bouton droit sur l’en-tête de liste et sélectionnez les colonnes que vous souhaitez afficher.

![Colonnes](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Ajuster les largeurs des colonnes
Augmentez et diminuez les largeurs de colonne dans la liste en plaçant le curseur sur l’en-tête de colonne.

## <a name="promote-user-properties-to-monitor"></a>Promouvoir les propriétés de l’utilisateur à superviser

Vous pouvez promouvoir n’importe quelle propriété de l’activité de pipeline en tant que propriété d’utilisateur pour pouvoir la surveiller. Ainsi, vous pouvez promouvoir les propriétés **Source** et **Destination** de l’activité Copy dans votre pipeline en tant que propriétés d’utilisateur. Vous pouvez également sélectionner **Générer automatiquement** afin de générer les propriétés d’utilisateur **Source** et **Destination** pour une activité Copy.

![Créer des propriétés d’utilisateur](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Vous pouvez promouvoir jusqu’à 5 propriétés de l’activité de pipeline en tant que propriétés d’utilisateur.

Une fois les propriétés d’utilisateur créées, vous pouvez les suivre dans les listes de surveillance. Si la source de l’activité Copy est un nom de table, vous pouvez suivre ce nom de table source en tant que colonne dans la liste des exécutions d’activités.

![Liste des exécutions d’activités sans propriétés d’utilisateur](media/monitor-visually/monitor-user-properties-image2.png)

![Ajouter des colonnes à la liste des exécutions d’activités pour les propriétés d’utilisateur](media/monitor-visually/monitor-user-properties-image3.png)

![Liste des exécutions d’activités avec colonnes pour les propriétés d’utilisateur](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Réexécuter des activités à l’intérieur d’un pipeline

Vous pouvez maintenant réexécuter des activités à l’intérieur d’un pipeline. Cliquez sur **Afficher les exécutions d’activités** et sélectionnez l’activité dans votre pipeline à partir de laquelle vous souhaitez réexécuter votre pipeline.

![Afficher les exécutions d’activités](media/monitor-visually/rerun-activities-image1.png)

![Sélectionner une exécution d’activité](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Voir l’historique des réexécutions

Vous pouvez voir l’historique des réexécutions de toutes les exécutions du pipeline en mode Liste.

![Afficher l’historique](media/monitor-visually/rerun-history-image1.png)

Vous pouvez également voir l’historique des réexécutions d’une exécution de pipeline spécifique.

![Voir l’historique d’une exécution de pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="guided-tours"></a>Visites guidées
Cliquez sur l’icône « Informations » dans l’angle inférieur gauche, puis cliquez sur « Visites guidées » pour obtenir des instructions détaillées sur la façon de surveiller vos exécutions d’activités et de pipelines.

![Visites guidées](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Commentaires
Cliquez sur l’icône « Commentaires » pour nous envoyer vos commentaires sur les différentes fonctionnalités ou les problèmes auxquels vous êtes confrontés.

![Commentaires](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Alertes

Vous pouvez déclencher des alertes sur les métriques prises en charge dans Data Factory. Sélectionnez **Superviser -> Alertes et métriques**  dans la page de supervision de Data Factory pour commencer.

![](media/monitor-visually/alerts01.png)

Pour voir une présentation de sept minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Créer des alertes

1.  Cliquez sur **Nouvelle règle d’alerte**  pour créer une alerte.

    ![](media/monitor-visually/alerts02.png)

1.  Spécifiez le nom de la règle et sélectionnez la **Gravité** de l’alerte.

    ![](media/monitor-visually/alerts03.png)

1.  Sélectionnez les critères d’alerte.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Configurez la logique d’alerte. Vous pouvez créer une alerte pour la métrique sélectionnée pour tous les pipelines et activités correspondantes. Vous pouvez également sélectionner un type d’activité, nom d’activité, nom de pipeline ou type d’échec particulier.

    ![](media/monitor-visually/alerts06.png)

1.  Configurez les notifications **E-mail/SMS/Push/Voix** pour l’alerte. Créez ou choisissez un **Groupe d’actions** existant pour les notifications d’alerte.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  Créez la règle d’alerte

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez l’article [Superviser et gérer les pipelines par programmation](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) pour en savoir plus sur la supervision et la gestion des pipelines.
