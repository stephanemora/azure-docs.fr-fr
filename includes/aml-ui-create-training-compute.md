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
ms.openlocfilehash: eb84dc1b5bf3f756e484ef27aaa998ab6b94cc51
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891639"
---
Une expérience s’exécute sur une cible de calcul, une ressource de calcul qui est attachée à votre espace de travail.  Une fois que vous avez créé une cible de calcul, vous pouvez la réutiliser pour d’autres exécutions ultérieures.

1. Sélectionnez **Run** (Exécuter) au bas pour exécuter l’expérience.

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
