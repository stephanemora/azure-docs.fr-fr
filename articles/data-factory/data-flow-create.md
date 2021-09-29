---
title: Créer un flux de données de mappage
description: Comment créer un mappage de flux de données Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 83b40121de72dc45582ded94580154f5124817b0
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061007"
---
# <a name="create-azure-data-factory-data-flow"></a>Créer un flux de données Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Le mappage de flux de données ADF permet de transformer des données à grande échelle sans aucun codage. Vous pouvez concevoir une tâche de transformation de données dans le concepteur de flux de données en créant une série de transformations. Commencez par un certain nombre de transformations sources, suivies par des étapes de transformation de données. Ensuite, réalisez votre flux de données avec un récepteur qui servira de destination à vos résultats.

Commencez par créer une fabrique de données v2 sur le portail Azure. Après avoir créé votre nouvelle fabrique, sélectionnez « Ouvrir » dans la vignette « Ouvrir Azure Data Factory Studio » pour lancer l’interface utilisateur de Data Factory.

:::image type="content" source="media/data-flow/v2portal.png" alt-text="Capture d’écran montrant le volet Nouvelle fabrique de données avec v2 sélectionné pour la version.":::

Une fois dans l’interface utilisateur de Data Factory, vous pouvez utiliser des exemples de flux de données, disponibles dans la galerie de modèles ADF. Dans Azure Data Factory, sélectionnez la vignette « Modèles de pipeline » dans la section « Découvrir plus » de la page d’accueil, puis sélectionnez la catégorie Data Flow dans la galerie de modèles.

:::image type="content" source="media/data-flow/template.png" alt-text="Capture d’écran montrant l’onglet Flux de données avec l’option Transformer des données à l’aide du flux de données sélectionnée.":::

Il vous est demandé d’entrer les informations d’identification de votre compte de Stockage Blob Azure.

:::image type="content" source="media/data-flow/template2.png" alt-text="Capture d’écran montrant le volet Transformer des données à l’aide du flux de données dans lequel vous pouvez entrer des entrées utilisateur.":::

[Les données utilisées dans ces exemples se trouvent ici](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Téléchargez les exemples de données et stockez les fichiers dans vos comptes de Stockage Blob Azure pour pouvoir exécuter les exemples.

## <a name="create-new-data-flow"></a>Créer un flux de données

Utilisez le bouton « signe plus » Créer une ressource de l’interface utilisateur ADF pour créer des flux de données.

:::image type="content" source="media/data-flow/newresource.png" alt-text="Capture d’écran montrant Flux de données sélectionné dans le menu Ressources de la fabrique.":::

## <a name="next-steps"></a>Étapes suivantes

Créez votre transformation des données avec une [transformation source](data-flow-source.md).
