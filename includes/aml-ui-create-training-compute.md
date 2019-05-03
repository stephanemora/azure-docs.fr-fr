---
title: Fichier Include
description: Fichier Include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028514"
---
Une expérience s’exécute sur une cible de calcul, une ressource de calcul qui est attachée à votre espace de travail.  Une fois que vous créez une cible de calcul, vous pouvez le réutiliser pour les exécutions futures.

1. Sélectionnez **exécuter** en bas pour exécuter l’expérience.

     ![Exécuter une expérience](./media/aml-ui-create-training-compute/run-experiment.png)

1. Lorsque le **le programme d’installation de calcul cibles** boîte de dialogue s’affiche, si votre espace de travail a déjà une ressource de calcul, vous pouvez le sélectionner maintenant.  Sinon, sélectionnez **créer un nouveau**.

    > [!NOTE]
    > L’interface visuelle permettre uniquement exécuter des expériences sur les cibles de Machine Learning Compute. Autres cibles de calcul seront affichera pas.

1. Fournissez un nom pour la ressource de calcul.

1. Sélectionnez **Exécuter**.

    ![Cible de calcul le programme d’installation](./media/aml-ui-create-training-compute/set-compute.png)

    La ressource de calcul est désormais créée. Afficher l’état dans l’angle supérieur droit de l’expérience. 

    > [!NOTE]
    > Il prend environ 5 minutes pour créer une ressource de calcul. Une fois que la ressource est créée, vous pouvez la réutiliser et ignorer ce délai d’attente pour les exécutions futures.
    >
    > La ressource de calcul sera mise à l’échelle sur 0 nœud lorsqu’il est inactif pour réduire vos coûts.  Lorsque vous l’utilisez à nouveau après un certain délai, vous pouvez rencontrer à nouveau environ 5 minutes de temps d’attente alors que son évolution précédent.
