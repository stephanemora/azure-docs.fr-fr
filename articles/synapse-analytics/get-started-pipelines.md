---
title: 'Tutoriel : Commencer l’intégration avec les pipelines'
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
ms.openlocfilehash: 2ea7c3c440fcf95e4512464333efe8461788bceb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219400"
---
# <a name="integrate-with-pipelines"></a>Intégrer avec des pipelines

Dans ce tutoriel, vous allez découvrir comment intégrer des pipelines et des activités en utilisant Synapse Studio. 

## <a name="overview"></a>Vue d’ensemble

Vous pouvez intégrer un large éventail de tâches dans Azure Synapse.

1. Dans Synapse Studio, accédez au hub **Intégrer**.
1. Sélectionnez **+**  > **Pipeline** pour créer un pipeline. Cliquez sur le nouvel objet de pipeline pour ouvrir le Concepteur de pipeline.
1. Sous **Activités**, développez le dossier **Synapse**, puis faites glisser un objet **Notebook** dans le concepteur.
1. Sélectionnez l’onglet **Paramètres** des propriétés de l’activité Notebook. Utilisez la liste déroulante pour sélectionner un notebook dans votre espace de travail Synapse actuel. 
1. Dans le pipeline, sélectionnez **Ajouter un déclencheur** > **Nouveau/modifier**.
1. Dans **Choisir un déclencheur**, sélectionnez **Nouveau**, puis définissez la **Récurrence** sur « Toutes les heures ».
1. Sélectionnez **OK**. 
1. Sélectionnez **Publier tout**. 


## <a name="monitor-pipeline"></a>Surveillance d’un pipeline

1. Une fois le pipeline publié, pour qu’il s’exécute immédiatement, sans attendre l’heure suivante, sélectionnez **Ajouter un déclencheur** > **Déclencher maintenant**.
1. Dans Synapse Studio, accédez au hub **Monitor**, puis sélectionnez **Exécutions de pipeline** pour superviser la progression de l’exécution du pipeline.



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Visualiser des données avec Power BI](get-started-visualize-power-bi.md)
