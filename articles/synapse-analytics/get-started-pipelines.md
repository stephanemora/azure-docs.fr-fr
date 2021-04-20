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
ms.openlocfilehash: 05c33db130bfa3fcc1a4f5d75935294fcc0ba1d7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365465"
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


## <a name="monitor-pipeline-execution"></a>Superviser l’exécution du pipeline

1. Une fois le pipeline publié, pour qu’il s’exécute immédiatement, sans attendre l’heure suivante, sélectionnez **Ajouter un déclencheur** > **Déclencher maintenant**.
1. Dans Synapse Studio, accédez au hub **Superviser**.
1. Sélectionnez **Exécutions de pipeline** pour superviser la progression de l’exécution du pipeline.



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Visualiser des données avec Power BI](get-started-visualize-power-bi.md)
