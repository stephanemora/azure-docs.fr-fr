---
title: Réentraîner un modèle Machine Learning Studio - Azure | Microsoft Docs
description: Apprenez à réentraîner un modèle et à mettre à jour le service web pour utiliser le modèle réentraîné dans Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 1f2505eda232c518ccb24b32fad91fb36dd11b01
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52317923"
---
# <a name="retrain-an-azure-machine-learning-studio-model"></a>Réentraîner un modèle Azure Machine Learning Studio
Dans le cadre du processus de mise en œuvre opérationnelle des modèles d’apprentissage automatique d’Azure Machine Learning, votre modèle est entraîné et enregistré. Vous l’utilisez ensuite pour créer un service web prédictif. Le service web peut ensuite être utilisé dans des sites web, des tableaux de bord et des applications mobiles. 

Les modèles que vous créez à l’aide de Machine Learning ne sont généralement pas statiques. Lorsque de nouvelles données sont disponibles ou lorsque le consommateur de l’API a ses propres données, il faut effectuer à nouveau l’apprentissage du modèle. 

Il peut être très fréquent d’avoir à effectuer à nouveau l’apprentissage du modèle. Avec la fonctionnalité d’API Programmatic Retraining, vous pouvez réentraîner le modèle par programmation à l’aide des API Retraining et mettre à jour le service web avec le modèle réentraîné. 

Ce document décrit le processus de réentraînement et vous montre comment utiliser les API Retraining.

## <a name="why-retrain-defining-the-problem"></a>Les raisons du réentraînement : définition du problème
Dans le cadre du processus d’entraînement du Machine Learning, un modèle est entraîné à l’aide d’un jeu de données. Les modèles que vous créez à l’aide de Machine Learning ne sont généralement pas statiques. Lorsque de nouvelles données sont disponibles ou lorsque le consommateur de l’API a ses propres données, il faut réentraîner le modèle.

Dans ces scénarios, une API programmatique offre un moyen pratique pour vous ou les utilisateurs de vos API de créer un client qui peut, à titre exceptionnel ou de manière régulière, réentraîner le modèle à l’aide de ses propres données. Il est alors possible d’évaluer les résultats du réentraînement et de mettre à jour l’API du service web de sorte qu’elle utilise le modèle réentraîné.

> [!NOTE]
> Si vous avez une expérience d’entraînement existante et un nouveau service web, vous pouvez consulter la section Réentraîner un service web prédictif existant au lieu de suivre la procédure pas à pas décrite dans la section suivante.
> 
> 

## <a name="end-to-end-workflow"></a>Workflow de bout en bout
Le processus implique les éléments suivants : une expérience d’entraînement et une expérience prédictive publiées en tant que service web. Pour permettre le réentraînement d’un modèle, l’expérience d’entraînement doit être publiée en tant que service web avec la sortie d’un modèle entraîné. Cela permet à l’API d’accéder au modèle en vue du réentraînement. 

Les étapes suivantes s’appliquent aux services web nouveaux et classiques :

Créez le service web prédictif initial :

* Créez une expérience d’entraînement
* Créez une expérience prédictive
* Déployez un service web prédictif

Reformez le service web :

* Mettez à jour l’expérience d’entraînement pour permettre le réentraînement
* Déployez le service web de réentraînement
* Utilisez le code de service d’exécution de lot pour réentraîner le modèle

Pour une présentation détaillée des précédentes étapes, consultez [Réentraîner des modèles Machine Learning programmatiquement](retrain-models-programmatically.md).

> [!NOTE] 
> Pour déployer un nouveau service web, vous devez disposer d’autorisations suffisantes dans l’abonnement dans lequel déployer le service web. Pour en savoir plus, consultez la rubrique [Gérer un service web à l’aide du portail des services web Azure Machine Learning](manage-new-webservice.md). 

Si vous avez déployé un service web classique :

* Créer un point de terminaison sur le service web prédictif
* Obtenez l’URL et le code du CORRECTIF
* Utilisez l’URL du CORRECTIF pour faire pointer le nouveau point de terminaison sur le modèle réentraîné 

Pour une présentation détaillée des précédentes étapes, consultez [Réentraîner un service web classique](retrain-a-classic-web-service.md).

Si vous rencontrez des difficultés pour réentraîner un service web classique, consulter [Dépannage du réentraînement d’un service web classique Azure Machine Learning](troubleshooting-retraining-models.md).

Si vous avez déployé un nouveau service web :

* Connectez-vous à votre compte Azure Resource Manager
* Obtenez la définition du service web
* Exportez la définition du service web au format JSON
* Mettez à jour la référence à l’objet blob `ilearner` dans le JSON
* Importez le JSON dans une définition du service web
* Mettez à jour le service web avec la nouvelle définition du service web

Pour une présentation détaillée des étapes précédentes, voir [Réentraîner un nouveau service web à l’aide des applets de commande PowerShell de gestion de Machine Learning](retrain-new-web-service-using-powershell.md).

Le processus de configuration du réentraînement d’un service web classique implique les étapes suivantes :

![Présentation du processus de réentraînement][1]

Le processus de configuration du réentraînement d’un nouveau service web implique les étapes suivantes :

![Présentation du processus de réentraînement][7]

## <a name="other-resources"></a>Autres ressources
* [Reformation et mise à jour de modèles Microsoft Azure Machine Learning avec Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [Créer de nombreux modèles Machine Learning et points de terminaison de service web à partir d’une expérience à l’aide de PowerShell](create-models-and-endpoints-with-powershell.md)
* La vidéo [Modèles de réentraînement AML utilisant des API](https://www.youtube.com/watch?v=wwjglA8xllg) montre comment réentraîner des modèles Machine Learning créés dans Azure Machine Learning en utilisant les API Retraining et PowerShell.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

