---
title: 'Tutoriel : Commencer l’orchestration avec les pipelines'
description: Dans ce tutoriel, vous allez découvrir comment orchestrer des pipelines et des activités en utilisant Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 72eea7c46dd005cd16ae5b8f0022c1174dd28f27
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667480"
---
# <a name="orchestrate-with-pipelines"></a>Orchestrer avec des pipelines

Dans ce tutoriel, vous allez découvrir comment orchestrer des pipelines et des activités en utilisant Synapse Studio. 

## <a name="overview"></a>Vue d’ensemble

Vous pouvez orchestrer un large éventail de tâches dans Azure Synapse.

1. Dans Synapse Studio, accédez au hub **Orchestrer**.
1. Sélectionnez **+**  > **Pipeline** pour créer un pipeline.
1. Accédez au hub **Développer**, puis sélectionnez l’un des notebooks que vous avez créés précédemment.
1. Faites glisser ce notebook dans le pipeline.
1. Dans le pipeline, sélectionnez **Ajouter un déclencheur** > **Nouveau/modifier**.
1. Dans **Choisissez un déclencheur**, sélectionnez **Nouveau** et, pour la **périodicité**, définissez le déclencheur pour qu’il s’exécute toutes les heures.
1. Sélectionnez **OK**.
1. Sélectionnez **Publier tout**. Le pipeline s’exécute toutes les heures.
1. Pour lancer l’exécution du pipeline immédiatement, sans attendre l’heure suivante, sélectionnez **Ajouter un déclencheur** > **Nouveau/modifier**.



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Visualiser des données avec Power BI](get-started-visualize-power-bi.md)
                                 
