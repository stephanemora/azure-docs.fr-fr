---
title: Métadonnées et traçabilité à partir de SAP S/4HANA
description: Cet article décrit l’extraction de la traçabilité des données à partir d’une source SAP S/4HANA.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 122b7a0b826c43f43b5e43d4ba2cb5e42fd52a16
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422479"
---
# <a name="how-to-get-lineage-from-sap-s4hana-into-azure-purview"></a>Comment obtenir la traçabilité de SAP S/4HANA dans Azure Purview

Cet article aborde les aspects de la traçabilité des données d’une source SAP S/4HANA dans Azure Purview. Le prérequis pour voir la traçabilité des données dans Purview pour SAP S/4HANA est d’[analyser votre SAP S/4HANA.](../purview/register-scan-saps4hana-source.md) 

## <a name="lineage-of-sap-s4hana-artifacts-in-azure-purview"></a>Traçabilité des artefacts SAP S/4HANA dans Azure Purview

Les utilisateurs peuvent rechercher les artefacts SAP S/4HANA par nom, par description ou par d’autres informations détaillées pour voir des résultats pertinents. Sous l’onglet Vue d’ensemble et propriétés des ressources, les détails de base comme la description, les propriétés et d’autres informations sont affichés. Sous l’onglet Traçabilité, les relations des ressources entre les tables et les vues SAP S/4HANA sont montrées. Par conséquent, les vues SAP S/4HANA comportent des informations de traçabilité provenant des tables. 

La traçabilité dérivée est également disponible au niveau des colonnes.

## <a name="next-steps"></a>Étapes suivantes

- [Découvrir la traçabilité des données dans Azure Purview](catalog-lineage-user-guide.md)
- Si vous déplacez des données vers Azure depuis SAP S/4HANA en utilisant ADF, nous pouvons suivre la traçabilité dans le cadre de l’exécution du déplacement des données - [Lier Azure Data Factory pour envoyer la traçabilité automatisée](how-to-link-azure-data-factory.md)
