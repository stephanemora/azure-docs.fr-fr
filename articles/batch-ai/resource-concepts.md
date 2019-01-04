---
title: À propos des ressources Batch AI - Azure | Microsoft Docs
description: Vue d’ensemble des espaces de travail, clusters, serveurs de fichiers, expériences et travaux du service Batch AI dans Microsoft Azure.
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 4454448809411c6390bc1f65c33b4abe76094ded
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407743"
---
# <a name="overview-of-resources-in-batch-ai"></a>Vue d’ensemble des ressources de Batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Lorsque vous commencez à utiliser le service Batch AI, vous devez connaître les ressources Batch AI disponibles. Comme pour d’autres services Azure, vous créez des ressources Batch AI dans un ou plusieurs *groupes de ressources* Azure. Créez un ou plusieurs *espaces de travail* Batch AI dans un groupe de ressources. Chaque espace de travail contient un mélange de *clusters*, *serveurs de fichiers* et *expériences* Batch AI. Une expérience Batch AI encapsule un groupe de *travaux*.

L’image suivante montre un exemple de hiérarchie de ressources pour Batch AI. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

Les sections suivantes approfondissent les ressources Batch AI.

## <a name="workspace"></a>Espace de travail

Un espace de travail dans Batch AI est une collection de niveau supérieur du reste des ressources Batch AI. Les espaces de travail aident à distinguer le travail appartenant aux différents groupes ou projets. Par exemple, vous pouvez créer un espace de travail de développement et un espace de travail de test.

## <a name="cluster"></a>Cluster

Un cluster dans Batch AI contient les ressources de calcul nécessaires pour exécuter les travaux. Tous les nœuds dans un cluster ont une taille de machine virtuelle et une image de système d’exploitation identiques. Batch AI offre plusieurs options pour la création de clusters adaptés à différents besoins. En règle générale, vous configurez un cluster distinct pour chaque catégorie de puissance de traitement nécessaire pour accomplir un projet. Adaptez l’échelle des clusters Batch AI en fonction de la demande et du budget. Pour plus d’informations, voir [Travailler avec des clusters Batch AI](clusters.md).

## <a name="file-server"></a>Serveur de fichiers

Vous pouvez créer un serveur de fichiers dans Batch AI pour stocker des données, des scripts de formation et des journaux de sortie. Un serveur de fichiers Batch AI est un système NFS managé à un seul nœud, qui peut être monté automatiquement sur des nœuds de cluster pour fournir un emplacement de stockage central accessible pour les travaux. Dans la plupart des cas, un seul serveur de fichiers s’avère nécessaire dans un espace de travail, et vous pouvez répartir les données de vos travaux d’entraînement dans différents répertoires. Si un système NFS ne convient pas pour vos charges de travail, Batch AI prend en charge d’autres options de stockage, dont [Stockage Azure](use-azure-storage.md) ou des solutions personnalisées telles qu’un système de fichiers Gluster ou Lustre.

## <a name="experiment"></a>Expérience

Une expérience regroupe une collection de tâches connexes que vous pouvez interroger et gérer ensemble. Chaque espace de travail peut avoir plusieurs expériences, chacune d’elles tentant de résoudre un problème spécifique.

## <a name="job"></a>Travail

Un travail est une tâche ou un script à exécuter, par exemple, pour former un modèle d’apprentissage profond. Chaque travail exécute un script spécifique sur un cluster dans l’espace de travail (le script peut être stocké sur un serveur de fichiers Batch AI ou une autre solution de stockage). Chaque tâche Batch AI est associée à un type de framework : TensorFlow, Horovod, CNTK, Caffe, Caffe2, pyTorch, Chainer, MPI personnalisé ou personnalisé. Pour chaque infrastructure, le service Batch AI configure l’infrastructure requise et gère les processus de travail. Chaque expérience peut comporter plusieurs travaux similaires, à l’exception de modifications de quelques paramètres.

## <a name="next-steps"></a>Étapes suivantes

* Exécutez votre premier [travail de formation Batch AI](quickstart-tensorflow-training-cli.md).

* Consultez des exemples de [recettes de formation](https://github.com/Azure/BatchAI/tree/master/recipes) pour différentes infrastructures.