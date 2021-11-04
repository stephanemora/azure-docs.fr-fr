---
title: Métadonnées et traçabilité à partir de Teradata
description: Cet article décrit l’extraction de traçabilité des données à partir d’une source Teradata.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 047405fea177cb8ba1365a1329dd6a682f1662fb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428764"
---
# <a name="how-to-get-lineage-from-teradata-into-azure-purview"></a>Comment tracer les données à partir de Teradata dans Azure Purview

Cet article aborde les aspects de la traçabilité des données d’une source Teradata dans Azure Purview. La configuration requise pour afficher la traçabilité des données dans Purview pour Teradata consiste à [analyser Teradata.](../purview/register-scan-teradata-source.md) 

## <a name="lineage-of-teradata-artifacts-in-azure-purview"></a>Traçabilité des artefacts Teradata dans Azure Purview

Les utilisateurs peuvent rechercher les artefacts Teradata par nom, par description ou par d’autres informations détaillées pour voir les résultats pertinents. Sous l’onglet Vue d’ensemble et propriétés des ressources, les détails de base comme la description, les propriétés et d’autres informations sont affichés. Sous l’onglet Traçabilité, les relations de ressources entre les tables Teradata et les procédures stockées, et entre les tables et les vues Teradata sont affichées. 

Par conséquent, nous prenons en charge la traçabilité des transformations de données intervenant dans des procédures stockées et des transformations dans les vues de tables Teradata. La traçabilité dérivée est également disponible au niveau des colonnes.

:::image type="content" source="./media/how-to-lineage-teradata/lineage.png" alt-text="Capture d’écran montrant comment la traçabilité est rendue pour Teradata." lightbox="./media/how-to-lineage-teradata/lineage.png":::

Dans la capture d’écran ci-dessus, **customerView** correspond à une vue Teradata créée à partir d’une table Teradata **test_customer**. De même, **Return_DataSet** est une procédure stockée qui utilise une table Teradata **test_customer**.

## <a name="next-steps"></a>Étapes suivantes

- [Découvrir la traçabilité des données dans Azure Purview](catalog-lineage-user-guide.md)
- Si vous déplacez des données vers Azure depuis Teradata en utilisant ADF, nous pouvons suivre la traçabilité dans le cadre de l’exécution du déplacement des données - [Lier Azure Data Factory pour envoyer la traçabilité automatisée](how-to-link-azure-data-factory.md)
