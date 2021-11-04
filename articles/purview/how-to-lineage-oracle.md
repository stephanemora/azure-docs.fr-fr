---
title: Métadonnées et traçabilité d’Oracle
description: Cet article décrit l’extraction des données de traçabilité depuis une source Oracle.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: 4f790487e910c279723c38e725e6b0ee1549e0bc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425061"
---
# <a name="how-to-get-lineage-from-oracle-into-azure-purview"></a>Comment obtenir la traçabilité depuis Oracle dans Azure Purview

Cet article aborde les aspects de la traçabilité des données d’une source Oracle dans Azure Purview. Le prérequis pour voir la traçabilité des données dans Purview pour Oracle est d’[analyser votre instance Oracle.](../purview/register-scan-oracle-source.md) 

## <a name="lineage-of-oracle-artifacts-in-azure-purview"></a>Traçabilité des artefacts Oracle dans Azure Purview

Les utilisateurs peuvent rechercher les artefacts Oracle par nom, par description ou par d’autres informations détaillées pour voir des résultats pertinents. Sous l’onglet Aperçu et propriétés des ressources, les détails de base tels que la description, la classification et d’autres informations sont affichés. Sous l’onglet Traçabilité, les relations des ressources entre les tables Oracle et les procédures stockées, les vues et les fonctions sont montrées. 

Par conséquent, les vues Oracle actuelles comportent des informations de traçabilité provenant des tables, tandis que les fonctions et les procédures stockées produisent une traçabilité entre les tables/vues avec parameter_dataset et stored_procedure_result_set. La traçabilité dérivée est également disponible au niveau des colonnes.

:::image type="content" source="./media/how-to-lineage-oracle/lineage.png" alt-text="Capture d’écran montrant comment la traçabilité est rendue pour Oracle." lightbox="./media/how-to-lineage-oracle/lineage.png":::


## <a name="next-steps"></a>Étapes suivantes

- [Découvrir la traçabilité des données dans Azure Purview](catalog-lineage-user-guide.md)
- [Lier Azure Data Factory pour pousser la traçabilité automatisée](how-to-link-azure-data-factory.md)
