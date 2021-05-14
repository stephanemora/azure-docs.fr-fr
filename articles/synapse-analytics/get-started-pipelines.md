---
title: 'Tutoriel : Commencer l’intégration avec des pipelines'
description: Dans ce tutoriel, vous allez découvrir comment intégrer des pipelines et des activités en utilisant Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 19bff62883341947eb5290118494b8244c5476ac
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518250"
---
# <a name="integrate-with-pipelines"></a>Intégrer avec des pipelines

Dans ce tutoriel, vous allez découvrir comment intégrer des pipelines et des activités en utilisant Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Créer un pipeline et ajouter une activité de notebook

1. Dans Synapse Studio, accédez au hub **Intégrer**.
1. Sélectionnez **+**  > **Pipeline** pour créer un pipeline. Cliquez sur le nouvel objet de pipeline pour ouvrir le Concepteur de pipeline.
1. Sous **Activités**, développez le dossier **Synapse**, puis faites glisser un objet **Notebook** dans le concepteur.
1. Sélectionnez l’onglet **Paramètres** des propriétés de l’activité Notebook. Utilisez la liste déroulante pour sélectionner un notebook dans votre espace de travail Synapse actuel.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Planifier l’exécution du pipeline toutes les heures

1. Dans le pipeline, sélectionnez **Ajouter un déclencheur** > **Nouveau/modifier**.
1. Dans **Choisir un déclencheur**, sélectionnez **Nouveau**, puis définissez la **Récurrence** sur « Toutes les heures ».
1. Sélectionnez **OK**. 
1. Sélectionnez **Publier tout**. 

## <a name="forcing-a-pipeline-to-run-immediately"></a>Forcer l’exécution immédiate d’un pipeline

Une fois le pipeline publié, vous pouvez l’exécuter immédiatement sans attendre une heure.

1. Ouvrez le pipeline.
1. Cliquez sur **Ajouter un déclencheur** > **Déclencher maintenant**.

## <a name="monitor-pipeline-execution"></a>Superviser l’exécution du pipeline

1. Accédez au hub **Superviser**.
1. Sélectionnez **Exécutions de pipeline** pour superviser la progression de l’exécution du pipeline.
1. Dans cette vue, vous pouvez basculer entre la **Liste** tabulaire et l’affichage d’un diagramme de **Gantt**. 
1. Cliquez sur un nom de pipeline pour voir l’état des activités dans ce pipeline.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Visualiser des données avec Power BI](get-started-visualize-power-bi.md)
