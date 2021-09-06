---
title: Métadonnées et traçabilité à partir de BigQuery
description: Cet article décrit l’extraction de traçabilité des données à partir d’une source BigQuery.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 9373544ee3eab185438aafd7dbbdf661b385074e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525385"
---
# <a name="how-to-get-lineage-from-bigquery-into-azure-purview"></a>Comment tracer les données à partir de BigQuery dans Azure Purview

Cet article aborde les aspects de la traçabilité des données d’une source BigQuery dans Azure Purview. La configuration requise pour afficher la traçabilité des données dans Purview pour BigQuery consiste à [analyser votre projet BigQuery.](../purview/register-scan-google-bigquery-source.md) 

## <a name="lineage-of-bigquery-artifacts-in-azure-purview"></a>Traçabilité des artefacts BigQuery dans Azure Purview

Les utilisateurs peuvent rechercher les artefacts BigQuery par nom, par description ou par d’autres informations détaillées pour voir des résultats pertinents. Sous l’onglet Vue d’ensemble et propriétés des ressources, les détails de base comme la description, les propriétés et d’autres informations sont affichés. Sous l’onglet Traçabilité, les relations des ressources entre les tables et les vues BigQuery sont affichées. Par conséquent, les vues BigQuery comporteront des informations de traçabilité provenant des tables. 

La traçabilité dérivée est également disponible au niveau des colonnes.

:::image type="content" source="./media/how-to-lineage-google-bigquery/lineage.png" alt-text="Capture d’écran montrant comment la traçabilité est rendue pour BigQuery." lightbox="./media/how-to-lineage-google-bigquery/lineage.png":::


## <a name="next-steps"></a>Étapes suivantes

- [Découvrir la traçabilité des données dans Azure Purview](catalog-lineage-user-guide.md)
- [Lier Azure Data Factory pour pousser la traçabilité automatisée](how-to-link-azure-data-factory.md)
