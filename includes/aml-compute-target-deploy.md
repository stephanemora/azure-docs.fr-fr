---
title: Fichier include
description: Fichier include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/11/2020
ms.openlocfilehash: c03550b8f0b6c146caa5fb33f06eba6671fa313b
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107950264"
---
La cible de calcul que vous utilisez pour héberger votre modèle aura une incidence sur le coût et la disponibilité de votre point de terminaison déployé. Utilisez ce tableau pour choisir une cible de calcul appropriée.

| Cible de calcul | Utilisé pour | Prise en charge GPU | Prise en charge FPGA | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Service&nbsp;web&nbsp;local](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Test/débogage | &nbsp; | &nbsp; | Pour les tests et la résolution des problèmes limités. L’accélération matérielle dépend de l’utilisation de bibliothèques dans le système local.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Inférence en temps réel |  [Oui](../articles/machine-learning/how-to-deploy-with-triton.md) (déploiement de services web) | [Oui](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Pour les déploiements de production à grande échelle. Fournit un temps de réponse et une mise à l’échelle automatique rapides du service déployé. La mise à l’échelle automatique du cluster n’est pas prise en charge via le Kit de développement logiciel (SDK) Azure Machine Learning. Pour modifier les nœuds du cluster AKS, utilisez l’interface utilisateur de votre cluster AKS dans le portail Azure. <br/><br/> Pris en charge dans le concepteur. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Test ou développement | &nbsp;  | &nbsp; | Pour les charges de travail à faible échelle basées sur le processeur qui nécessitent moins de 48 Go de RAM. <br/><br/> Pris en charge dans le concepteur. |
| [Clusters de calcul Azure Machine Learning](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | Inférence&nbsp;Batch | [Oui](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) (pipeline d’apprentissage automatique) | &nbsp;  | Exécutez le scoring par lots sur un calcul sans serveur. Prend en charge des machines virtuelles normales et basse priorité. Aucune prise en charge de l’inférence en temps réel.|

> [!NOTE]
> Les cibles de calcul telles que les cibles locales et les clusters de calcul Azure Machine Learning prennent en charge le GPU pour la formation et l’expérimentation, mais l’utilisation d’un GPU pour l’inférence _lors d’un déploiement en tant que service web_ est prise en charge uniquement sur AKS.
>
> L’utilisation d’un GPU pour l’inférence _lors du scoring avec un pipeline d’apprentissage automatique_ est prise en charge uniquement sur un calcul Azure Machine Learning.
> 
> Lors du choix d’une référence (SKU) de cluster, effectuez un scale-up, puis un scale-out. Commencez avec une machine disposant de 150 % de la RAM dont votre modèle a besoin, profilez le résultat et recherchez une machine présentant le niveau de performance dont vous avez besoin. Une fois celle-ci trouvée, augmentez le nombre de machine en fonction de vos besoins en matière d’inférence simultanée.

> [!NOTE]
> * Des instances de conteneur conviennent uniquement pour des modèles d’une taille inférieure à 1 Go.
> * Pour le développement et le test de modèles plus volumineux, utilisez des clusters AKS à nœud unique.