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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177680"
---
Une expérience s’exécute sur une cible de calcul, une ressource de calcul qui est attachée à votre espace de travail.  Une fois que vous avez créé une cible de calcul, vous pouvez la réutiliser pour d’autres exécutions ultérieures.

1. Sélectionnez **Run** (Exécuter) au bas pour exécuter l’expérience.

     ![Exécuter une expérience](./media/aml-ui-create-training-compute/run-experiment.png)

1. Quand la boîte de dialogue **Configurer des cibles de calcul** s’affiche, si votre espace de travail contient déjà une ressource de calcul, sélectionnez-la.  Sinon, sélectionnez **Créer**.

    > [!NOTE]
    > L’interface visuelle peut uniquement exécuter des expériences sur des cibles Capacité de calcul Machine Learning. Les autres cibles de calcul ne sont pas affichées.

1. Fournissez un nom pour la ressource de calcul.

1. Sélectionnez **Exécuter**.

    ![Configurer la cible de calcul](./media/aml-ui-create-training-compute/set-compute.png)

    La ressource de calcul va maintenant être créée. Consultez l’état en haut à droite de l’expérience. 

    > [!NOTE]
    > La création d’une ressource de calcul prend cinq minutes environ. Une fois que la ressource a été créée, vous pouvez la réutiliser pour les exécutions ultérieures, ce qui vous évite ce temps d’attente.
    >
    > Afin de réduire vos coûts, la ressource de calcul est automatiquement mise à l’échelle à 0 nœud quand elle est inactive.  Quand vous la réutilisez après un temps d’inactivité, vous pouvez encore avoir à attendre environ cinq minutes pendant le scale-up de la ressource de calcul.
