---
title: Suivi des données lors du repérage dans la préversion d’Azure Sentinel à l’aide de signets de repérage | Microsoft Docs
description: Cet article décrit comment utiliser les signets de repérage d’Azure Sentinel pour effectuer le suivi des données.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: aec04c4b9fd56b79a92c2774a48fd55f2f6a9d7a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620209"
---
# <a name="keep-track-of-data-during-hunting"></a>Suivi des données lors du repérage

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Le repérage des menaces nécessite généralement la vérification de montagnes de données de journal à la recherche de preuves de comportements malveillants. Pendant ce processus, les enquêteurs détectent des événements qu’ils souhaitent mémoriser, réexaminer et analyser afin de valider des hypothèses potentielles et de comprendre d’une compromission dans sa globalité.
Les signets de repérage vous y aident, en conservant les requêtes que vous avez exécutées dans Log Analytics, ainsi que les résultats de requête que vous jugez pertinents. Vous pouvez également enregistrer vos observations contextuelles et référencer vos découvertes en ajoutant des balises et des notes. Les données avec signet sont visibles par vous et vos collègues pour faciliter la collaboration.   

Vous pouvez revenir consulter vos données avec signet à tout moment dans l’onglet **Signet** de la page **Repérage**. Vous pouvez utiliser les options de filtrage et de recherche pour trouver rapidement des données spécifiques dans le cadre de l’examen en cours. Vous pouvez également afficher vos données avec signet directement dans la table **HuntingBookmark** de Log Analytics. Cela vous permet de filtrer les données avec signet, les synthétiser et les joindre à d’autres sources de données, ce qui facilite la recherche de preuves pour la confirmation.

Vous pouvez également visualiser vos données avec signet en cliquant sur **Examiner**. Cette opération lance l’expérience d’examen dans laquelle vous pouvez afficher, examiner et communiquer visuellement vos résultats à l’aide d’un diagramme d’entité-graphique interactif et d’une chronologie.


## <a name="run-a-log-analytics-query-from-azure-sentinel"></a>Exécuter une requête Log Analytics à partir d’Azure Sentinel

1. Dans le portail Azure Sentinel, cliquez sur **Repérage** pour exécuter des requêtes liées à un comportement suspect et anormal.

1. Pour exécuter une campagne de repérage, sélectionnez une des requêtes de repérage et examinez les résultats à gauche. 

1. Cliquez sur **View query results (Afficher les résultats de la requête)** sur la page **Détails** de la requête de repérage pour consulter les résultats de la requête dans Log Analytics. Voici un exemple de ce que vous voyez si vous avez exécuté une requête d’attaque par force brute SSH personnalisée.
  
   ![afficher les résultats](./media/bookmarks/ssh-bruteforce-example.png)

## <a name="add-a-bookmark"></a>Ajouter un signet

1. Dans la liste des résultats de requête de Log Analytics, développez la ligne contenant les informations qui vous intéressent.

4. Cliquez sur les points de suspension (...) à la fin de la ligne, puis sélectionnez **Add hunting bookmarks (Ajouter des signets de repérage)** .
5. À droite, sur la page **Détails**, mettez à jour le nom et ajouter des balises et des notes pour identifier facilement les informations intéressantes à propos de l’élément.
6. Cliquez sur **Enregistrer** pour valider les modifications. Toutes les données avec signet sont partagées avec d’autres enquêteurs et constituent une première étape vers une expérience d’examen collaborative.

   ![afficher les résultats](./media/bookmarks/add-bookmark-la.png)

 
> [!NOTE]
> Vous pouvez également utiliser des signets avec des requêtes Log Analytics arbitraires lancées à partir de la page de journaux Log Analytics d’Azure Sentinel, ou avec des requêtes créées à la volée sur la page de Log Analytics et ouvertes à partir de la page Repérage. Vous ne serez pas en mesure d’ajouter un signet si vous lancez Log Analytics hors d’Azure Sentinel. 

## <a name="view-and-update-bookmarks"></a>Afficher et mettre à jour des signets 

1. Dans le portail Azure Sentinel, cliquez sur **Repérage**. 
2. Cliquez sur l’onglet **Signets** au milieu de la page pour afficher la liste des signets.
3. Utilisez les options de filtre ou de la zone de recherche pour trouver un signet spécifique.
4. Sélectionnez des signets individuels dans la grille située au-dessous pour afficher les détails de ce signet dans le volet de détails de droite.
5. Pour mettre à jour les balises et les notes, cliquez sur les zones de texte modifiables, puis sur **Enregistrer** pour conserver les modifications.

   ![afficher les résultats](./media/bookmarks/view-update-bookmarks.png)

## <a name="view-bookmarked-data-in-log-analytics"></a>Afficher les données avec signet dans Log Analytics 

Il existe plusieurs options pour afficher vos données avec signet dans Log Analytics. 

Le moyen le plus simple pour afficher les requêtes de signet, les résultats ou l’historique consiste à sélectionner le signet voulu dans la table **Signets** et à utiliser les liens fournis dans le volet de détails. Options disponibles : 
- Cliquez sur **Afficher la requête** pour afficher la requête source dans Log Analytics.  
- Cliquez sur **View bookmark history (Afficher l’historique du signet)** pour voir toutes les métadonnées du signet, notamment l’auteur de la mise à jour, les valeurs mises à jour et l’heure de la mise à jour. 

- Vous pouvez également afficher les données avec signet brutes pour tous les signets en cliquant sur **Bookmark logs (Journaux de signets)** au-dessus de la grille des signets. Cette vue affiche tous vos signets dans la table de signets de repérage, avec les métadonnées associées. Vous pouvez utiliser des requêtes KQL pour filtrer jusqu’à la dernière version du signet spécifique que vous recherchez.  


> [!NOTE]
> Il peut y avoir un délai important (mesuré en minutes) entre la création d’un signet et le moment où il apparaît dans la table **HuntingBookmark**. Il est recommandé de créer d’abord vos signets, puis de les analyser une fois les données ingérées. 

## <a name="delete-a-bookmark"></a>Supprimer un signet
Si vous souhaitez supprimer un signet, procédez comme suit : 
1.  Ouvrez l’onglet **Hunting bookmark (Signet de repérage)** . 
2.  Sélectionnez le signet cible.
3.  Cliquez sur les points de suspension (...) à la fin de la ligne, puis sélectionnez **Supprimer le signet**.
    
La suppression du signet supprime le signet de la liste dans l’onglet **Signet**.  La table « HuntingBookmark » de Log Analytics contient toujours les précédentes entrées de signet, mais la valeur de la dernière entrée pour **SoftDelete** devient true, ce qui vous permet d’exclure les anciens signets.  La suppression d’un signet ne supprime pas les entités de l’expérience d’examen qui sont associées à d’autres alertes ou signets. 


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à exécuter un examen de repérage à l’aide de signets dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :


- [Proactively hunt for threats](hunting.md) (Rechercher des menaces de façon proactive)
- [Use notebooks to run automated hunting campaigns](notebooks.md) (Utiliser des blocs-notes pour exécuter des campagnes de repérage automatisées)
