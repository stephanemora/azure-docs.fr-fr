---
title: Métadonnées et traçabilité à partir de SAP ECC
description: Cet article décrit l’extraction de la traçabilité des données à partir d’une source SAP ECC.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 4c1e18f3f7484aec5bd99efbc4f9dea5b0e84ef8
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525452"
---
# <a name="how-to-get-lineage-from-sap-ecc-into-azure-purview"></a>Comment obtenir la traçabilité de SAP ECC dans Azure Purview

Cet article aborde les aspects de la traçabilité des données d’une source SAP ECC dans Azure Purview. Le prérequis pour voir la traçabilité des données dans Purview pour SAP ECC est d’[analyser votre SAP ECC.](../purview/register-scan-sapecc-source.md) 

## <a name="lineage-of-sap-ecc-artifacts-in-azure-purview"></a>Traçabilité des artefacts SAP ECC dans Azure Purview

Les utilisateurs peuvent rechercher les artefacts SAP ECC par nom, par description ou par d’autres informations détaillées pour voir des résultats pertinents. Sous l’onglet Vue d’ensemble et propriétés des ressources, les détails de base comme la description, les propriétés et d’autres informations sont affichés. Sous l’onglet Traçabilité, les relations des ressources entre les tables et les vues SAP ECC sont affichées. Par conséquent, les vues SAP ECC comporteront des informations de traçabilité provenant des tables. 

La traçabilité dérivée est également disponible au niveau des colonnes.

:::image type="content" source="./media/how-to-lineage-sapecc/lineage.png" alt-text="Capture d’écran montrant comment la traçabilité est rendue pour SAP ECC." lightbox="./media/how-to-lineage-sapecc/lineage.png":::


## <a name="next-steps"></a>Étapes suivantes

- [Découvrir la traçabilité des données dans Azure Purview](catalog-lineage-user-guide.md)
- Si vous déplacez des données vers Azure depuis SAP ECC en utilisant ADF, nous pouvons suivre la traçabilité dans le cadre de l’exécution du déplacement des données - [Lier Azure Data Factory pour envoyer la traçabilité automatisée](how-to-link-azure-data-factory.md)
