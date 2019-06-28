---
title: Présentation de la fonctionnalité de mappage de flux de données d'Azure Data Factory
description: Présentation générale du mappage de flux de données dans Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 051886f98d6d35594336291bbb2defb2a4acdfc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233050"
---
# <a name="what-are-mapping-data-flows"></a>Qu’est-ce que le mappage de flux de données?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Les mappages des flux de données sont des transformations de données conçues de manière graphique dans Azure Data Factory. Les flux de données permettent aux ingénieurs informatiques de développer une logique de transformation graphique des données sans rédiger de code. Les flux de données qui en résultent sont exécutés en tant qu'activités dans les pipelines Azure Data Factory à l'aide de clusters Azure Databricks mis à l'échelle.

Les flux de données Azure Data Factory sont destinés à fournir une expérience entièrement visuelle sans codage. Vos flux de données s'exécuteront sur votre cluster d'exécution pour un traitement de données à l'échelle. Azure Data Factory gère intégralement la traduction du code, l'optimisation du chemin et l'exécution de vos travaux de flux de données.

Commencez par créer les flux de données en Mode débogage afin de pouvoir valider votre logique de transformation de manière interactive. Puis ajoutez une activité Data Flow à votre pipeline pour exécuter et tester votre flux de données lors du débogage du pipeline, ou utilisez « Déclencher maintenant » dans le pipeline pour tester votre flux de données à partir d'une activité de pipeline.

Vous pourrez ensuite planifier et superviser vos activités de flux de données à l'aide des pipelines Azure Data Factory qui exécutent l'activité Data Flow.

Le bouton bascule Mode débogage de l'aire de conception Data Flow permet la création interactive de transformations de données. Le Mode débogage fournit un environnement de préparation des données pour la création de flux de données.
