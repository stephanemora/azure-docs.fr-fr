---
title: Découvrir Bicep sur Microsoft Learn
description: Fournit une vue d’ensemble des unités disponibles sur Microsoft Learn pour Bicep.
ms.topic: conceptual
ms.date: 08/08/2021
ms.openlocfilehash: a0459e44c4abd69810bcc70974ea2e160adf5a5e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524758"
---
# <a name="bicep-on-microsoft-learn"></a>Bicep sur Microsoft Learn

Pour obtenir une aide étape par étape sur l’utilisation de Bicep pour déployer votre infrastructure sur Azure, Microsoft Learn propose plusieurs modules d’apprentissage.

## <a name="introductory-path"></a>Parcours d’introduction

Le parcours d’apprentissage [Déployer et gérer des ressources dans Azure en utilisant Bicep](/learn/paths/bicep-deploy/) constitue le meilleur point de départ. Il présente le concept d’infrastructure en tant que code. Le parcours vous guide à travers les étapes de création de fichiers Bicep de plus en plus complexes.

Ce parcours contient les modules suivants.

| Module Learn | Description |
| ------------ | ----------- |
| [Présentation de l’infrastructure en tant que code à l’aide de Bicep](/learn/modules/introduction-to-infrastructure-as-code-using-bicep/) | Ce module décrit les avantages liés à l’utilisation de l’infrastructure en tant que code, d’Azure Resource Manager et de Bicep pour mettre à l’échelle rapidement et en toute confiance vos déploiements dans le cloud. Il vous aide à déterminer les types de déploiements pour lesquels Bicep est un bon outil de déploiement. |
| [Créer votre premier modèle Bicep](/learn/modules/build-first-bicep-template/) | Dans ce module, vous définissez des ressources Azure dans un modèle Bicep. Vous améliorez la cohérence et la fiabilité de vos déploiements, réduisez l’effort manuel requis et mettez à l’échelle vos déploiements dans différents environnements. Votre modèle sera flexible et réutilisable grâce à l’utilisation de paramètres, de variables, d’expressions et de modules. |
| [Créer des modèles Bicep réutilisables en utilisant des paramètres](/learn/modules/build-reusable-bicep-templates-parameters/) | Ce module décrit la manière dont vous pouvez utiliser les paramètres Bicep pour fournir des informations à votre modèle lors de chaque déploiement. Vous en apprendrez davantage sur les décorateurs de paramètres, qui rendent vos paramètres faciles à comprendre et à utiliser. Vous découvrirez également les différentes façons de fournir des valeurs de paramètres et de les protéger lorsque vous travaillez avec des informations sécurisées. |
| [Créer des modèles Bicep flexibles en utilisant des conditions et des boucles](/learn/modules/build-flexible-bicep-templates-conditions-loops/) | Apprenez à utiliser des conditions pour déployer des ressources uniquement lorsque des contraintes spécifiques sont en place. Apprenez également à utiliser des boucles pour déployer plusieurs ressources possédant des propriétés similaires. |
| [Déployer des ressources d’extension et des ressources enfants à l’aide de Bicep](/learn/modules/child-extension-bicep-templates/) | Ce module explique comment déployer diverses ressources Azure dans votre code Bicep. Découvrez les ressources d’extension et enfants, ainsi que la manière de les définir et de les utiliser dans Bicep. Utilisez Bicep pour travailler avec les ressources que vous avez créées en dehors d’un module ou d’un modèle Bicep. |
| [Déployer des ressources sur des abonnements, des groupes d’administration et des locataires en utilisant Bicep](/learn/modules/deploy-resources-scopes-bicep/) | Déployez des ressources Azure au niveau de l’étendue de l’abonnement, du groupe d’administration et du locataire. Découvrez ce que sont ces ressources, pourquoi vous les utilisez et comment créer du code Bicep pour les déployer. Apprenez également à créer un ensemble unique de fichiers Bicep que vous pouvez déployer sur plusieurs étendues en une seule opération. |
| [Étendre des modèles en utilisant des scripts de déploiement](/learn/modules/extend-resource-manager-template-deployment-scripts/) | Découvrez comment ajouter des étapes personnalisées à votre fichier Bicep ou à votre modèle Azure Resource Manager (modèle ARM) à l’aide de scripts de déploiement. |

## <a name="other-modules"></a>Autres modules

En plus du parcours précédent, les modules suivants contiennent du contenu Bicep.

| Module Learn | Description |
| ------------ | ----------- |
| [Gérer les modifications apportées à votre code Bicep à l’aide de Git](/learn/modules/manage-changes-bicep-code-git/) | Découvrez comment utiliser Git pour prendre en charge votre workflow de développement Bicep en effectuant le suivi des modifications que vous apportez au fur et à mesure que vous travaillez. Vous découvrirez comment valider des fichiers, voir l’historique des fichiers que vous avez modifiés et utiliser des branches pour développer plusieurs versions de votre code en même temps. Vous allez également apprendre à utiliser GitHub ou Azure Repos pour publier un référentiel afin que vous puissiez collaborer avec les membres de l’équipe. |
| [Publier des bibliothèques de code d’infrastructure réutilisable à l’aide de specs de modèle](/learn/modules/arm-template-specs/) | Les specs de modèle vous permettent de réutiliser et de partager vos modèles ARM au sein de votre organisation. Découvrez comment créer et publier des specs de modèle et comment les déployer. Vous apprendrez également à gérer les specs de modèle, notamment comment contrôler l’accès et comment les mettre à jour en toute sécurité à l’aide de versions. |
| [Créer un aperçu des modifications apportées aux déploiements Azure en utilisant une simulation](/learn/modules/arm-template-whatif/) | Ce module vous apprend à prévisualiser vos modifications avec l’opération de simulation. Grâce à la simulation, vous pouvez vous assurer que votre fichier Bicep effectue uniquement les modifications auxquelles vous vous attendez. |
| [Structurer votre code Bicep pour la collaboration](/learn/modules/structure-bicep-code-collaboration/) | Créez des fichiers Bicep qui prennent en charge le développement collaboratif et respectent les bonnes pratiques. Planifiez vos paramètres pour faciliter le déploiement de vos modèles. Utilisez un style cohérent, une structure claire et des commentaires pour faciliter la compréhension, l’utilisation et la modification de votre code Bicep. |
| [Authentifier votre pipeline de déploiement Azure à l’aide de principaux de service](/learn/modules/authenticate-azure-deployment-pipeline-service-principals/) | Les principaux de service permettent à vos pipelines de déploiement de s’authentifier en toute sécurité auprès Azure. Dans ce module, vous allez découvrir les principaux de service et leur fonctionnement, ainsi que la façon de les créer. Vous découvrirez également comment leur octroyer l’autorisation d’accès à vos ressources Azure pour que vos pipelines puissent déployer vos fichiers Bicep. |
| [Créer votre premier pipeline de déploiement Bicep à l’aide d’Azure Pipelines](/learn/modules/build-first-bicep-deployment-pipeline-using-azure-pipelines/) | Créez un pipeline de déploiement de base pour le code Bicep. Utilisez une connexion de service pour identifier en toute sécurité votre pipeline auprès d’Azure. Configurez le moment où le pipeline s’exécute à l’aide de déclencheurs. |

## <a name="next-steps"></a>Étapes suivantes

* Pour une brève présentation de Bicep, consultez [Démarrage rapide Bicep](quickstart-create-bicep-use-visual-studio-code.md).
* Pour obtenir des suggestions sur la façon d’améliorer vos fichiers Bicep, consultez [Meilleures pratiques pour Bicep](best-practices.md).
