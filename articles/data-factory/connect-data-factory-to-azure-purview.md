---
title: Connecter une fabrique de données à Azure Purview
description: En savoir plus sur la connexion d’une fabrique de données à Azure Purview
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 7dc05c88416bb2a23221029bc04c506271a86652
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108345"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Connecter Data Factory à Azure Purview (préversion)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article explique comment connecter une fabrique de données à Azure Purview et comment signaler la traçabilité des données des activités ADF Copy data, Dataflow et Exécuter le Package SSIS.

## <a name="connect-data-factory-to-azure-purview"></a>Connecter une fabrique de données à Azure Purview
Azure Purview est un nouveau service cloud qui permet aux utilisateurs de données de gérer de manière centralisée la gouvernance des données de leur patrimoine de données dans l’ensemble des environnements cloud et locaux. Vous pouvez connecter votre fabrique de données à Azure Purview, et cette connexion vous permet d’utiliser Azure Purview pour capturer des données de traçabilité des activités Copy, Dataflow et Exécuter le Package SSIS. Pour savoir comment inscrire une fabrique de données dans Azure Purview, consultez [Guide pratique pour connecter à Azure Data Factory et Azure Purview](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Rapporter les données de traçabilité à Azure Purview
Lorsque les clients exécutent une activité Copy, Dataflow ou Exécuter le Package SSIS dans Azure Data Factory, ils peuvent obtenir la relation de dépendance et disposer d’une vue d’ensemble générale de l’intégralité du processus du workflow entre les sources de données et la destination.
Pour savoir comment collecter la traçabilité des données à partir d’Azure Data Factory, consultez [Traçabilité des données de Data Factory](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Étapes suivantes
[Guide de l’utilisateur sur la traçabilité Catalog](https://docs.microsoft.com/azure/purview/catalog-lineage-user-guide)

[Tutoriel : Envoyer les données de traçabilité Data Factory à Azure Purview](turorial-push-lineage-to-purview.md)
