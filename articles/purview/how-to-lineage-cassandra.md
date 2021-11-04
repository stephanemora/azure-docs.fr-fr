---
title: Métadonnées et traçabilité à partir de Cassandra
description: Cet article décrit l’extraction de traçabilité des données à partir d’une source Cassandra.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: a46649187403c5825f580da167f9fe77dc9ba210
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453323"
---
# <a name="how-to-get-lineage-from-cassandra-into-azure-purview"></a>Comment tracer les données à partir de Cassandra dans Azure Purview

Cet article aborde les aspects de la traçabilité des données d’une source Cassandra dans Azure Purview. La condition préalable pour afficher la traçabilité des données dans Purview pour Cassandra consiste à [analyser votre serveur Cassandra.](../purview/register-scan-cassandra-source.md) 

## <a name="lineage-of-cassandra-artifacts-in-azure-purview"></a>Traçabilité des artefacts Cassandra dans Azure Purview

Les utilisateurs peuvent rechercher les artefacts Cassandra par nom, par description ou par d’autres informations détaillées pour voir les résultats pertinents. Sous l’onglet Vue d’ensemble et propriétés des ressources, les détails de base comme la description, les propriétés et d’autres informations sont affichés. Sous l’onglet Traçabilité, les relations des ressources entre les tables et les vues matérialisées de Cassandra sont affichées. Par conséquent, les vues de Cassandra contiennent des informations de traçabilité provenant des tables. 

La traçabilité dérivée est également disponible au niveau des colonnes.

:::image type="content" source="./media/how-to-lineage-cassandra/lineage.png" alt-text="Capture d’écran montrant le rendu de la traçabilité pour Cassandra." lightbox="./media/how-to-lineage-cassandra/lineage.png":::


## <a name="next-steps"></a>Étapes suivantes

- [Découvrir la traçabilité des données dans Azure Purview](catalog-lineage-user-guide.md)
- [Lier Azure Data Factory pour pousser la traçabilité automatisée](how-to-link-azure-data-factory.md)
