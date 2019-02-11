---
title: Réentraîner un modèle Azure Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Apprenez à reformer un modèle et à mettre à jour le service web pour utiliser le modèle reformé dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 4fec32ac2d613486ee65416ccdfac70575ea9543
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509573"
---
# <a name="retrain-an-azure-machine-learning-studio-model"></a>Réentraîner un modèle Azure Machine Learning Studio
Dans le cadre du processus de mise en œuvre opérationnelle des modèles d’apprentissage automatique d’Azure Machine Learning, votre modèle est entraîné et enregistré. Vous l’utilisez ensuite pour créer un service web prédictif. Le service web peut ensuite être utilisé dans des sites web, des tableaux de bord et des applications mobiles. 

Les modèles que vous créez à l’aide de Machine Learning ne sont généralement pas statiques. Lorsque de nouvelles données sont disponibles ou lorsque le consommateur de l’API a ses propres données, il faut effectuer à nouveau l’apprentissage du modèle. 

Il peut être très fréquent d’avoir à effectuer à nouveau l’apprentissage du modèle. Avec la fonctionnalité d’API Programmatic Retraining, vous pouvez reformer le modèle par programmation à l’aide des API Retraining et mettre à jour le service web avec le modèle reformé. 

Ce document décrit le processus de nouvel apprentissage et vous montre comment utiliser les API Retraining.

## <a name="why-retrain-defining-the-problem"></a>Les raisons de la reformation : définition du problème
Dans le cadre du processus de formation Machine Learning, un modèle est formé à l’aide d’un jeu de données. Les modèles que vous créez à l’aide de Machine Learning ne sont généralement pas statiques. Lorsque de nouvelles données sont disponibles ou lorsque le consommateur de l’API a ses propres données, il faut effectuer à nouveau l’apprentissage du modèle.

Dans ces scénarios, une API par programme offre un moyen pratique pour vous ou les utilisateurs de vos API de créer un client qui peut, à titre exceptionnel ou de manière régulière, reformer le modèle à l’aide de ses propres données. Il est alors possible d’évaluer les résultats de la reformation et de mettre à jour l’API du service web de sorte qu’elle utilise le modèle reformé.

> [!NOTE]
> Si vous avez une expérience de formation existante et un nouveau service web, vous pouvez consulter la section Reformer un service web prédictif existant au lieu de suivre la procédure pas à pas décrite dans la section suivante.
> 
> 

## <a name="end-to-end-workflow"></a>Workflow de bout en bout
Le processus comprend les composants suivants : une expérience de formation et une expérience prédictive publiée comme un service web. Pour permettre la reformation d’un modèle, l’expérience de formation doit être publiée en tant que service web avec la sortie d’un modèle formé. Cela permet à l’API d’accéder au modèle en vue de la reformation. 

Les étapes suivantes s’appliquent aux services web nouveaux et classiques :

Créez le service web prédictif initial :

* Créez une expérience d'apprentissage
* Créez une expérience prédictive
* Déployez un service web prédictif

Reformez le service web :

* Mettez à jour l’expérience de formation pour permettre la reformation
* Déployez le service web de reformation
* Utilisez le code de service d’exécution de lot pour reformer le modèle

Pour une présentation détaillée des précédentes étapes, voir [Reformation des modèles Machine Learning par programme](retrain-models-programmatically.md).

> [!NOTE] 
> Pour déployer un nouveau service web, vous devez disposer d’autorisations suffisantes dans l’abonnement dans lequel déployer le service web. Pour en savoir plus, consultez la rubrique [Gérer un service web à l’aide du portail des services web Azure Machine Learning](manage-new-webservice.md). 

Si vous avez déployé un service web classique :

* Créer un point de terminaison sur le service web prédictif
* Obtenez l’URL et le code du CORRECTIF
* Utilisez l’URL du CORRECTIF pour faire pointer le nouveau point de terminaison sur le modèle reformé 

Pour une présentation détaillée des précédentes étapes, voir [Reformer un service web classique](retrain-a-classic-web-service.md).

Si vous rencontrez des difficultés pour reformer un service web classique, voir [Dépannage de la reformation d’un service web classique Azure Machine Learning](troubleshooting-retraining-models.md).

Si vous avez déployé un nouveau service web :

* Connectez-vous à votre compte Azure Resource Manager
* Obtenez la définition du service web
* Exportez la définition du service web au format JSON
* Mettez à jour la référence à l’objet blob `ilearner` dans le JSON
* Importez le JSON dans une définition du service web
* Mettez à jour le service web avec la nouvelle définition du service web

Pour une présentation détaillée des étapes précédentes, voir [Reformer un nouveau service web à l’aide des applets de commande PowerShell de gestion de Machine Learning](retrain-new-web-service-using-powershell.md).

Le processus de configuration de la reformation pour un service web classique implique les étapes suivantes :

![Présentation du processus de nouvel apprentissage.][1]

Le processus de configuration de la reformation pour un nouveau service web implique les étapes suivantes :

![Présentation du processus de nouvel apprentissage.][7]

## <a name="other-resources"></a>Autres ressources
* [Reformation et mise à jour de modèles Microsoft Azure Machine Learning avec Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [Créer de nombreux modèles Machine Learning et points de terminaison de service web à partir d’une expérience à l’aide de PowerShell](create-models-and-endpoints-with-powershell.md)
* La vidéo [Modèles de reformation AML utilisant des API](https://www.youtube.com/watch?v=wwjglA8xllg) montre comment reformer des modèles Machine Learning créés dans Azure Machine Learning en utilisant les API de reformation et PowerShell.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

