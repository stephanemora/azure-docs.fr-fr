---
title: À propos des environnements Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dans cet article, découvrez les avantages des environnements Machine Learning, qui permettent des définitions de dépendance d'apprentissage automatique reproductibles, auditables et portables sur une variété de cibles de calcul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 8906299cc9e2c000dab2ac9d2a345d9aaf238260
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045847"
---
# <a name="what-are-azure-machine-learning-environments"></a>Présentation des environnements Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Les environnements Azure Machine Learning spécifient les packages, variables d’environnement et paramètres logiciels Python autour de vos scripts d’apprentissage et de scoring. Ils spécifient également les temps d’exécution (Python, Spark ou Docker). Il s’agit d’entités gérées et avec version dans votre espace de travail Machine Learning qui permettent des workflows Machine Learning reproductibles, pouvant être audités et portables sur une variété de cibles de calcul.

Vous pouvez utiliser un objet `Environment` sur votre calcul local pour :
* Développer votre script d’apprentissage.
* Réutiliser le même environnement sur la capacité de calcul Azure Machine Learning pour l’apprentissage du modèle à l’échelle.
* Déployer votre modèle avec le même environnement.

Le diagramme suivant montre comment vous pouvez utiliser un seul objet `Environment` à la fois dans votre configuration d’exécution pour l’apprentissage et votre configuration d’inférence et de déploiement pour les déploiements de services web.

![Diagramme d’environnement dans un workflow Machine Learning](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Types d’environnements

Les environnements se répartissent globalement en trois catégories : *organisés*, *gérés par l’utilisateur* et *gérés par le système*.

Les environnements organisés sont fournis par Azure Machine Learning et sont disponibles dans votre espace de travail par défaut. Ils contiennent des collections de packages et paramètres Python destinés à vous aider à prendre en main diverses infrastructures de Machine Learning. 

Dans un environnement géré par l’utilisateur, vous êtes responsable de la configuration de votre environnement et de l’installation de chaque package dont votre script d’apprentissage a besoin sur la cible de calcul. Conda ne vérifie pas votre environnement et n’installe rien à votre place. Si vous définissez votre propre environnement, vous devez répertorier `azureml-defaults` avec la version `>= 1.0.45` comme dépendance PIP. Ce package contient les fonctionnalités qui sont nécessaires pour héberger le modèle en tant que service web.

Vous utilisez des environnements gérés par le système quand vous souhaitez que [Conda](https://conda.io/docs/) gère automatiquement l’environnement Python et les dépendances de script. Le service adopte ce type d’environnement par défaut, à cause de son utilité sur les cibles de calcul distantes qui ne sont pas configurables manuellement.

## <a name="create-and-manage-environments"></a>Créer et gérer des environnements

Vous pouvez créer des environnements en :

* Définissant de nouveaux objets `Environment`, soit en utilisant un environnement organisé, soit en définissant vos propres dépendances.
* utilisant des objets `Environment` existants à partir de votre espace de travail. Cette approche permet de garantir la cohérence et la reproductibilité de vos dépendances.
* Importation à partir d’une définition d’environnement Anaconda existante.
* Utilisation de l’interface CLI Azure Machine Learning

Pour des exemples de code spécifiques, consultez la section « Créer un environnement » de [Réutiliser des environnement pour l’apprentissage et le déploiement](how-to-use-environments.md#create-an-environment). Les environnements sont également facilement gérés via votre espace de travail. Ils doivent inclure la fonctionnalité suivante :

* Les environnements sont automatiquement inscrits à votre espace de travail lorsque vous soumettez une expérience. Ils peuvent également être inscrits manuellement.
* Vous pouvez récupérer des environnements à partir de votre espace de travail à utiliser pour l’apprentissage ou le déploiement, ou bien pour apporter des modifications à la définition de l’environnement.
* Avec le contrôle de version, vous pouvez voir les modifications apportées à vos environnements dans le temps, ce qui garantit la reproductibilité.
* Vous pouvez créer automatiquement des images Docker à partir de vos environnements.

Pour des exemples de code, consultez la section « Gérer des environnements » de [Réutiliser des environnement pour l’apprentissage et le déploiement](how-to-use-environments.md#manage-environments).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer et utiliser des environnements](how-to-use-environments.md) dans Azure Machine Learning.
* Consultez la documentation de référence du kit de développement logiciel (SDK) Python pour la [classe d’environnement](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Consultez la documentation de référence du kit de développement logiciel (SDK) R pour les [environnements](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
