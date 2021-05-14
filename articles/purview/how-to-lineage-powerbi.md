---
title: Métadonnées et traçabilité à partir de Power BI
description: Cet article décrit l’extraction de traçabilité des données à partir d’une source Power BI.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: b7143f7ccb0cb4c91cbb86cadb3cfa78c5c9bec3
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107912498"
---
# <a name="how-to-get-lineage-from-power-bi-into-azure-purview"></a>Comment tracer les données à partir de Power BI dans Azure Purview

Cet article aborde les aspects de la traçabilité des données d’une source Power BI dans Azure Purview. La configuration requise pour afficher la traçabilité des données dans Purview pour Power BI consiste à [analyser Power BI.](../purview/register-scan-power-bi-tenant.md) 

## <a name="common-scenarios"></a>Scénarios courants

1. Une fois la source Power BI analysée, les consommateurs de données peuvent effectuer une analyse de la cause racine d’un rapport ou d’un tableau de bord à partir de Purview. Pour toute incohérence de données dans un rapport, les utilisateurs peuvent facilement identifier les jeux de données en amont et contacter leurs propriétaires si nécessaire.

2. Les producteurs de données peuvent voir les rapports ou les tableaux de bord en aval qui consomment leur jeu de données. Avant d’apporter des modifications à leurs jeux de données, les propriétaires de données peuvent prendre des décisions avisées.

2. Les utilisateurs peuvent effectuer une recherche par nom, état d’approbation, étiquette de sensibilité, propriétaire, description et autres aspects métier pour retourner les artefacts Power BI pertinents.

## <a name="power-bi-artifacts-in-azure-purview"></a>Artefacts Power BI dans Azure Purview

Une fois l’[analyse de Power BI](../purview/register-scan-power-bi-tenant.md) terminée, les artefacts Power BI suivants sont inventoriés dans Purview

* Capacité
* Workspaces
* Dataflow
* Dataset 
* Rapport
* tableau de bord

Les artefacts de l’espace de travail affichent la trace Dataflow -> Jeu de données -> Rapport -> Tableau de bord

:::image type="content" source="./media/how-to-lineage-powerbi/powerbi-overview.png" alt-text="Capture d’écran montrant comment l’onglet Vue d’ensemble est rendu pour les ressources Power BI." lightbox="./media/how-to-lineage-powerbi/powerbi-overview.png":::

>[!Note]
> * La traçabilité des données des colonnes et les transformations dans les jeux de données PowerBI ne sont actuellement pas prises en charge
> * Des informations limitées sont actuellement affichées pour les sources de données à partir desquelles le jeu de données PowerBI ou dataflow PowerBI est créé. Par exemple : pour la source SQL Server d’un jeu de données PowerBI, seul le nom du serveur est capturé. 

## <a name="lineage-of-power-bi-artifacts-in-azure-purview"></a>Traçabilité des artefacts Power BI dans Azure Purview

Les utilisateurs peuvent rechercher un artefact Power BI par nom, description ou d’autres détails pour voir les résultats pertinents. Sous l’onglet Aperçu et propriétés des ressources, les détails de base tels que la description, la classification et d’autres informations sont affichés. Sous l’onglet Traçabilité, les relations de ressource sont affichées avec les dépendances en amont et en aval.

:::image type="content" source="./media/how-to-lineage-powerbi/powerbi-lineage.png" alt-text="Capture d’écran montrant comment la traçabilité est rendue pour Power BI." lightbox="./media/how-to-lineage-powerbi/powerbi-lineage.png":::

## <a name="next-steps"></a>Étapes suivantes

- [Découvrir la traçabilité des données dans Azure Purview](catalog-lineage-user-guide.md)
- [Lier Azure Data Factory pour pousser la traçabilité automatisée](how-to-link-azure-data-factory.md)
