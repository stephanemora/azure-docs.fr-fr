---
title: Suivi des données lors de la recherche dans la préversion de Sentinel Azure à l’aide de signets de chasse | Microsoft Docs
description: Cet article décrit comment utiliser les signets de chasse Sentinel Azure pour effectuer le suivi des données.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b1a438b9645dbb37d852eb0092355850d816872d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65207472"
---
# <a name="keep-track-of-data-during-hunting"></a>Suivi des données au cours de chasse

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Chasse aux menaces nécessite généralement la vérification des montagnes de données de journal de rechercher des preuves de comportements malveillants. Pendant ce processus, enquêteurs trouve les événements qu’ils souhaitent n’oubliez pas, réexaminer et analyser dans le cadre de la validation des hypothèses potentiels et comprendre l’intégralité d’une compromission.
Signets de chasse vous y aider, en conservant les requêtes que vous avez exécuté dans Analytique de journal, ainsi que les résultats de requête que vous jugez pertinentes. Vous pouvez également enregistrer vos observations contextuelles et référencer vos découvertes en ajoutant des balises et les notes de publication. Données signet sont visibles par vous et vos collègues pour faciliter la collaboration.   

Vous pouvez revenir consulter vos données marqué d’un signet à tout moment sur le **signet** onglet de la **chasse** page. Vous pouvez utiliser le filtrage et rechercher des options pour trouver rapidement des données spécifiques pour votre examen en cours. Vous pouvez également afficher vos données avec signet directement dans le **HuntingBookmark** table dans le journal Analytique. Cela vous permet de filtrer, résumer et joindre des données avec signet avec d’autres sources de données, ce qui facilite rechercher la confirmation de la preuve.

Vous pouvez également visualiser vos données marqué d’un signet, en cliquant sur **examiner**. Cette opération lance l’expérience d’investigation dans lequel vous pouvez afficher, rechercher et communiquer visuellement vos résultats à l’aide d’un diagramme d’entité-graphique interactif et la chronologie.


## <a name="run-a-log-analytics-query-from-azure-sentinel"></a>Exécuter une requête Analytique de journal à partir d’Azure Sentinel

1. Dans le portail Azure Sentinel, cliquez sur **chasse** pour exécuter des requêtes pour le comportement suspect et anormal.

1. Pour exécuter une campagne de chasse, sélectionnez une des requêtes de recherche et sur la révision de gauche, les résultats. 

1. Cliquez sur **afficher les résultats de la requête** dans la requête de chasse **détails** page pour afficher la requête génère Analytique de journal. Voici un exemple de ce que vous voyez si vous avez exécuté une requête d’attaque de bruteforce SSH personnalisée.
  
   ![afficher les résultats](./media/bookmarks/ssh-bruteforce-example.png)

## <a name="add-a-bookmark"></a>Ajouter un signet

1. Dans la liste de résultats de requête Analytique de journal, développez la ligne contenant les informations qui vous intéressent.

4. Sélectionnez les points de suspension (...) à la fin de la ligne, puis **ajouter des signets de chasse**.
5. Sur la droite, dans le **détails** page, mettre à jour le nom et ajouter des balises et les notes de publication pour vous aider à identifier ce qui était intéressant à propos de l’élément.
6. Cliquez sur **enregistrer** pour valider vos modifications. Toutes les données de signet est partagée avec d’autres enquêteurs et est une première étape vers une expérience d’investigation collaborative.

   ![afficher les résultats](./media/bookmarks/add-bookmark-la.png)

 
> [!NOTE]
> Vous pouvez également utiliser des signets avec des requêtes Analytique de journal arbitraires lancées à partir de la page de journaux d’Analytique de journal de Azure Sentinel, ou les requêtes créées sur le passage à la page d’Analytique de journal et ouvert à partir de la page de chasse. Vous ne serez pas en mesure d’ajouter un signet, si vous lancez l’Analytique de journal à partir en dehors d’Azure Sentinel. 

## <a name="view-and-update-bookmarks"></a>Affichage et mise à jour des signets 

1. Dans le portail Azure Sentinel, cliquez sur **chasse**. 
2. Cliquez sur le **signets** onglet au milieu de la page pour afficher la liste de signets.
3. Utilisez les options de zone ou un filtre de recherche pour trouver un signet spécifique.
4. Sélectionnez des signets individuels dans la grille ci-dessous pour afficher les détails de signet dans le volet de détails de droite.
5. Pour mettre à jour les balises et les notes de publication, cliquez sur les zones de texte modifiable et cliquez sur **enregistrer** pour conserver vos modifications.

   ![afficher les résultats](./media/bookmarks/view-update-bookmarks.png)

## <a name="view-bookmarked-data-in-log-analytics"></a>Afficher les données de signet dans le journal Analytique 

Il existe plusieurs options pour afficher vos données avec signet dans le journal Analytique. 

Le moyen le plus simple pour afficher les requêtes de signet, les résultats ou historique est en sélectionnant le signet voulu dans la **signets** table et utilisez les liens fournis dans le volet de détails. Les options sont les suivantes : 
- Cliquez sur **afficher la requête** pour afficher la requête source dans le journal Analytique.  
- Cliquez sur **afficher l’historique de signet** pour voir tous vos favoris les métadonnées, notamment : qui a effectué la mise à jour, les valeurs mises à jour et l’heure de la mise à jour. 

- Vous pouvez également afficher les données brutes de signet pour tous les signets en cliquant sur **signet journaux** au-dessus de la grille de signet. Cette vue montre tous vos signets dans la table de signet de chasse avec des métadonnées associées. Vous pouvez utiliser des requêtes KQL pour filtrer jusqu'à la dernière version du signet spécifique que vous recherchez.  


> [!NOTE]
> Il peut y avoir un délai important (exprimé en minutes) entre la création d’un signet et lorsqu’il est affiché dans le **HuntingBookmark** table. Il est recommandé de créer vos signets tout d’abord, puis analysez-les une fois que les données sont reçues. 

## <a name="delete-a-bookmark"></a>Supprimer un signet
Si vous souhaitez supprimer un signet effectuez les étapes suivantes : 
1.  Ouvrez th **signet de chasse** onglet. 
2.  Sélectionnez le signet cible.
3.  Sélectionnez les points de suspension (...) à la fin de la ligne et sélectionnez **signet de suppression**.
    
La suppression du signet supprime le signet dans la liste dans le **signet** onglet.  L’Analytique de journal « HuntingBookmark » table continuera à contenir des entrées de signet précédent, mais la dernière entrée changera la **SoftDelete** valeur est true, ce qui vous permet de filtrer les anciens signets.  Suppression d’un signet ne supprime pas toutes les entités à partir de l’expérience d’investigation qui sont associés aux autres alertes ou les signets. 


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment exécuter une investigation de chasse à l’aide de signets dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :


- [Recherche de façon proactive les menaces](hunting.md)
- [Utiliser des blocs-notes pour lancer des campagnes de chasse automatisés](notebooks.md)