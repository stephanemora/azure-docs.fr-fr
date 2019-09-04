---
title: Présentation de la fonctionnalité de mappage de flux de données d'Azure Data Factory
description: Présentation générale du mappage de flux de données dans Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 6f4c124c59584c8538d85ac61650661ae559a77b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123935"
---
# <a name="what-are-mapping-data-flows"></a>Qu’est-ce que le mappage de flux de données?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Les mappages des flux de données sont des transformations de données conçues de manière graphique dans Azure Data Factory. Les flux de données permettent aux ingénieurs informatiques de développer une logique de transformation graphique des données sans rédiger de code. Les flux de données qui en résultent sont exécutés en tant qu'activités dans les pipelines Azure Data Factory à l'aide de clusters Azure Databricks mis à l'échelle.

Les flux de données Azure Data Factory sont destinés à fournir une expérience entièrement visuelle sans codage. Vos flux de données s'exécuteront sur votre cluster d'exécution pour un traitement de données à l'échelle. Azure Data Factory gère intégralement la traduction du code, l'optimisation du chemin et l'exécution de vos travaux de flux de données.

Commencez par créer les flux de données en Mode débogage afin de pouvoir valider votre logique de transformation de manière interactive. Puis ajoutez une activité Data Flow à votre pipeline pour exécuter et tester votre flux de données lors du débogage du pipeline, ou utilisez « Déclencher maintenant » dans le pipeline pour tester votre flux de données à partir d'une activité de pipeline.

Vous pourrez ensuite planifier et superviser vos activités de flux de données à l'aide des pipelines Azure Data Factory qui exécutent l'activité Data Flow.

Le bouton bascule Mode débogage de l'aire de conception Data Flow permet la création interactive de transformations de données. Le Mode débogage fournit un environnement de préparation et d'aperçu des données pour la création de flux de données.

## <a name="begin-building-your-data-flow-logical-graph"></a>Commencer à créer votre graphique logique de flux de données

Commencez à créer des flux de données à l’aide du signe + sous Ressources de fabrique pour créer un flux de données.

![nouveau flux de données](media/data-flow/newdataflow2.png "nouveau flux de données")

Commencez par configurer votre transformation source, puis ajoutez la transformation de données à chaque étape suivante à l’aide du signe +. À mesure que vous créez votre graphique logique, vous pouvez basculer entre les modes graphique et configuration à l’aide du bouton « Afficher le graphique » et « Masquer le graphique ».

![Afficher le graphique](media/data-flow/showg.png "Afficher le graphique")

## <a name="configure-transformation-logic"></a>Configurer la logique de transformation

![Masquer le graphique](media/data-flow/hideg.png "Masquer le graphique")

Masquer votre graphique vous permet de parcourir les nœuds de transformation latéralement.

![Naviguer](media/data-flow/showhide.png "Naviguer")

## <a name="next-steps"></a>Étapes suivantes

* [Commencer par une transformation de la source](data-flow-source.md)
