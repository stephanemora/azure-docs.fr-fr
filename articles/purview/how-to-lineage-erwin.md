---
title: Métadonnées et traçabilité à partir de Erwin
description: Cet article décrit l’extraction de la traçabilité des données depuis une source Erwin.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: d1821ca94d8f5329ca4c08f65438a3d0ba6e4d48
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563348"
---
# <a name="how-to-get-lineage-from-erwin-into-azure-purview"></a>Comment obtenir la traçabilité depuis Erwin dans Azure Purview

Cet article aborde les aspects de la traçabilité des données d’une source Erwin dans Azure Purview. Le prérequis pour voir la traçabilité des données dans Purview pour Erwin est d’[analyser votre instance Erwin.](../purview/register-scan-erwin-source.md) 

## <a name="lineage-of-erwin-artifacts-in-azure-purview"></a>Traçabilité des artefacts Erwin dans Azure Purview

Les utilisateurs peuvent rechercher les artefacts Erwin par nom, par description ou par d’autres informations détaillées pour afficher les résultats pertinents. Sous l’onglet Vue d’ensemble et propriétés des ressources, les détails de base comme la description, les propriétés et d’autres informations sont affichés. Sous l’onglet Traçabilité, les relations des ressources entre les tables et les vues Erwin sont affichées. Par conséquent, les vues Erwin comporteront des informations de traçabilité provenant des tables. 

:::image type="content" source="./media/how-to-lineage-erwin/lineage.png" alt-text="Capture d’écran montrant le rendu de la traçabilité pour Erwin." lightbox="./media/how-to-lineage-erwin/lineage.png":::


## <a name="next-steps"></a>Étapes suivantes

- [Découvrir la traçabilité des données dans Azure Purview](catalog-lineage-user-guide.md)
- [Lier Azure Data Factory pour pousser la traçabilité automatisée](how-to-link-azure-data-factory.md)
