---
title: Surveiller visuellement Azure Data Factory
description: Découvrez comment superviser visuellement des fabriques de données Azure
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 85b1d6b532ba11819947558226291e62af6b5119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75690928"
---
# <a name="visually-monitor-azure-data-factory"></a>Surveiller visuellement Azure Data Factory

Une fois que vous avez créé et publié un pipeline dans Azure Data Factory, vous pouvez l’associer à un déclencheur ou lancer manuellement une exécution ad hoc. Vous pouvez surveiller l’ensemble de vos exécutions de pipeline en mode natif dans l’expérience utilisateur d’Azure Data Factory. Pour ouvrir l’expérience de surveillance, sélectionnez la vignette **Surveiller et gérer** dans le panneau de fabrique de données du [Portail Azure](https://portal.azure.com/). Si vous êtes déjà dans l’expérience utilisateur ADF, cliquez sur l’icône **Surveiller** dans la barre latérale gauche.

Toutes les exécutions de fabrique de données sont affichées dans le fuseau horaire local du navigateur. Si vous changez le fuseau horaire, tous les champs d’heure et de date sont alignés sur le fuseau horaire sélectionné.

## <a name="monitor-pipeline-runs"></a>Surveiller des exécutions de pipelines

L’affichage de la surveillance par défaut est la liste des exécutions de pipeline dans la période sélectionnée. Les colonnes suivantes s’affichent :

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom du pipeline | Nom du pipeline |
| Actions | Icônes qui vous permettent d’afficher les détails de l’activité, d’annuler ou de réexécuter le pipeline |
| Démarrage de l’exécution | Date et heure de début de l’exécution de pipeline (MM/JJ/AAAA, HH:MM:SS AM/PM) |
| Duration | Durée d’exécution (HH:MM:SS) |
| Déclenché par | Nom du déclencheur qui a démarré le pipeline |
| Statut | **Échec**, **Réussite**, **En cours**, **Annulée** ou **En file d'attente** |
| Annotations | Balises filtrables associées à un pipeline  |
| Paramètres | Paramètres d’exécution de pipeline (paires nom/valeur) |
| Error | En cas d’échec du pipeline, l’erreur d’exécution |
| ID d’exécution | ID de l’exécution de pipeline |

![Liste pour la supervision des exécutions de pipelines](media/monitor-visually/pipeline-runs.png)

Vous devez cliquer manuellement sur le bouton **Actualiser** pour actualiser la liste des exécutions de pipelines et d’activités. L’actualisation automatique n’est pas prise en charge actuellement.

![Bouton Actualiser](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Surveiller des exécutions d’activités

Pour voir les exécutions d’activités pour chaque exécution de pipeline, sélectionnez l’icône **Afficher les exécutions d’activité** sous la colonne **Actions**. La liste montre les exécutions d’activités correspondant à chaque exécution de pipeline.

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom de l’activité | Nom de l’activité dans le pipeline |
| Type d’activité | Type de l’activité, par exemple **Copy**, **ExecuteDataFlow** ou **AzureMLExecutePipeline** |
| Actions | Icônes qui vous permettent de voir des informations d’entrée JSON, des informations de sortie JSON ou des expériences de surveillance détaillées sur les activités | 
| Démarrage de l’exécution | Date et heure de début de l’exécution de l’activité (MM/JJ/AAAA, HH:MM:SS AM/PM) |
| Duration | Durée d’exécution (HH:MM:SS) |
| Statut | **Échec**, **Réussite**, **En cours** ou **Annulée** |
| Runtime d’intégration | Runtime d’intégration sur lequel l’activité a été exécutée |
| Propriétés de l’utilisateur | Propriétés définies par l’utilisateur de l’activité |
| Error | En cas d’échec de l’activité, l’erreur d’exécution |
| ID d’exécution | ID de l’exécution d’activité |

![Liste pour la supervision des exécutions d’activités](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Promouvoir les propriétés de l’utilisateur à superviser

Promouvez n’importe quelle propriété de l’activité de pipeline en tant que propriété d’utilisateur pour la surveiller. Ainsi, vous pouvez promouvoir les propriétés **Source** et **Destination** de l’activité de copie dans votre pipeline en tant que propriétés d’utilisateur. Sélectionnez également **Générer automatiquement** afin de générer les propriétés d’utilisateur **Source** et **Destination** pour une activité de copie.

![Créer des propriétés d’utilisateur](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Vous pouvez promouvoir jusqu’à cinq propriétés de l’activité de pipeline en tant que propriétés d’utilisateur.

Une fois les propriétés d’utilisateur créées, vous pouvez les superviser dans les listes de supervision. Si la source de l’activité de copie est un nom de table, vous pouvez superviser le nom de table source en tant que colonne dans la liste des exécutions d’activités.

![Liste des exécutions d’activités sans propriétés d’utilisateur](media/monitor-visually/monitor-user-properties-image2.png)

![Ajouter des colonnes à la liste des exécutions d’activités pour les propriétés d’utilisateur](media/monitor-visually/monitor-user-properties-image3.png)

![Liste des exécutions d’activités avec colonnes pour les propriétés d’utilisateur](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Configurer l’affichage de liste

### <a name="order-and-filter"></a>Ordonner et filtrer

Activez/désactivez si les exécutions du pipeline seront décroissantes ou croissantes en fonction de l’heure de début de l’exécution. Filtrez les exécutions de pipelines en utilisant les colonnes suivantes :

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom du pipeline | Filtrez en fonction du nom du pipeline. |
| Démarrage de l’exécution |  Déterminez l’intervalle de temps des exécutions de pipeline affichées. Il existe des options de filtre rapide comme **Dernières 24 heures**, **La semaine dernière**, **Les 30 derniers jours**, et vous pouvez aussi sélectionner une heure et une date personnalisées. |
| État de l’exécution | Filtrez les exécutions par état : **Réussite**, **Échec**, **En file d’attente**, **Annulée** ou **En cours**. |
| Annotations | Filtrer en fonction des balises appliquées à chaque pipeline |
| Exécutions | Filtrer si vous souhaitez voir des nouvelles exécutions de pipelines |

![Options de filtrage](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Ajouter ou supprimer des colonnes
Cliquez avec le bouton droit sur l’en-tête de liste et sélectionnez les colonnes que vous souhaitez afficher.

![Options de colonne](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Ajuster les largeurs des colonnes
Augmentez et diminuez les largeurs de colonne dans la liste en plaçant le pointeur sur l’en-tête de colonne.

## <a name="rerun-activities-inside-a-pipeline"></a>Réexécuter des activités à l’intérieur d’un pipeline

Vous pouvez réexécuter des activités à l’intérieur d’un pipeline. Sélectionnez **Afficher les exécutions d’activités**, puis sélectionnez l’activité dans votre pipeline à partir de laquelle vous souhaitez réexécuter votre pipeline.

![Afficher les exécutions d’activités](media/monitor-visually/rerun-activities-image1.png)

![Sélectionner une exécution d’activité](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Réexécuter à partir d’une activité ayant échoué

Si une activité échoue, expire ou est annulée, vous pouvez réexécuter le pipeline à partir de cette activité ayant échoué en sélectionnant **Réexécuter à partir d’une activité ayant échoué**.

![Réexécuter une activité ayant échoué](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Voir l’historique des réexécutions

Vous pouvez voir l’historique des réexécutions de toutes les exécutions du pipeline en mode Liste.

![Afficher l’historique](media/monitor-visually/rerun-history-image1.png)

Vous pouvez également voir l’historique des réexécutions d’une exécution de pipeline spécifique.

![Voir l’historique d’une exécution de pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Vues Gantt

Utilisez des vues Gantt pour visualiser rapidement vos pipelines et vos exécutions d’activités.

![Exemple de diagramme de Gantt](media/monitor-visually/gantt1.png)

Vous pouvez afficher la vue Gantt par pipeline ou effectuer un regroupement en fonction d’annotations ou de balises que vous avez créées sur vos pipelines.

![Annotations du diagramme de Gantt](media/monitor-visually/gantt2.png)

La longueur de la barre indique la durée du pipeline. Vous pouvez également sélectionner la barre pour voir plus de détails.

![Durée du diagramme de Gantt](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Visites guidées
Cliquez sur l’icône **Informations** en bas à gauche. Puis, cliquez sur **Visite guidée** pour obtenir des instructions détaillées sur la supervision des exécutions de pipelines et d’activités.

![Visites guidées](media/monitor-visually/guided-tours.png)

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
