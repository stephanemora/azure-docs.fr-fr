---
title: Fichier Include
description: Fichier Include
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: 1ee90e0c99234497b072bbee0b92d76129baea48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929636"
---
Un pipeline s’exécute sur une cible de calcul, qui est une ressource de calcul attachée à votre espace de travail. Une fois que vous avez créé une cible de calcul, vous pouvez la réutiliser pour d’autres exécutions ultérieures.

1. Sélectionnez **Exécuter** en haut du canevas pour exécuter le pipeline.

1. Quand le volet **Paramètres** s’affiche, choisissez **Sélectionner une cible de calcul**.

    Si vous avez déjà une cible de calcul, vous pouvez la sélectionner pour exécuter ce pipeline.

    > [!NOTE]
    > Le concepteur ne peut exécuter des expériences que sur des cibles Capacité de calcul Azure Machine Learning. Les autres cibles de calcul ne sont pas affichées.

1. Entrez un nom pour la ressource de calcul.

1. Sélectionnez **Enregistrer**.

    ![Configurer la cible de calcul](./media/aml-ui-create-training-compute/set-compute.png)

1. Sélectionnez **Exécuter**.

1. Dans la boîte de dialogue **Set up pipeline run** (Configurer l’exécution du pipeline), sélectionnez **+ Nouvelle expérience** pour **Expérience**.

    > [!NOTE]
    > Les expériences regroupent les exécutions de pipeline similaires. Si vous exécutez un pipeline plusieurs fois, vous pouvez sélectionner la même expérience pour les exécutions successives.

    1. Entrez un nom descriptif pour le **Nom d’expérience**.

    1. Sélectionnez **Exécuter**.
    
    Vous pouvez voir l’état et les détails de l’exécution en haut à droite du canevas.

    > [!NOTE]
    > La création d’une ressource de calcul prend environ cinq minutes. Une fois que la ressource a été créée, vous pouvez la réutiliser pour les exécutions ultérieures, ce qui vous évite ce temps d’attente.
    >
    > Afin de réduire vos coûts, la ressource de calcul est automatiquement mise à l’échelle à zéro nœud quand elle est inactive. Quand vous la réutilisez après un temps d’inactivité, vous pourriez encore avoir à attendre environ cinq minutes pendant le scale-up de la ressource de calcul.
