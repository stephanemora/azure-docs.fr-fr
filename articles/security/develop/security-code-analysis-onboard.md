---
title: Guide d’intégration de Microsoft Azure Security Code Analysis
description: Cet article concerne l’installation de l’extension Security Code Analysis
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8a8eca73205d4f8f33d4d069fda72638af61d355
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718172"
---
# <a name="how-to-onboarding-and-installing"></a>Procédure : Intégration et installation

Prérequis pour démarrer avec Microsoft Security Code Analysis
  - Offre de services Support unifié Microsoft éligibles (détails ci-dessous)
  - Une organisation Azure DevOps
  - Autorisations pour installer des extensions à l’organisation Azure DevOps
  - Du code source qui peut être synchronisé avec un pipeline Azure DevOps hébergé dans le cloud


## <a name="onboarding-microsoft-security-code-analysis-extension"></a>Intégration de l’extension Microsoft Security Code Analysis

- Si vous disposez déjà de l’une des offres de support suivantes, contactez simplement votre responsable technique de compte et achetez\échangez des heures existantes pour accéder à l’extension.
   - Support unifié : niveau Avancé et Performances, Support Premier pour les développeurs, Support Premier pour les partenaires ou Support Premier pour les entreprises.
- Si vous disposez de l’un des services de support suivants ou si vous n’avez pas de plan de support avec Microsoft, vous devez effectuer une mise à niveau vers une offre de support éligible :
   - Support Azure pour les partenaires, Azure Basic, Azure Developer, Azure Standard, Azure Professional Direct ou Support unifié - Niveau principal
- Pour acheter une offre de support éligible, consultez notre [page d’accueil des services de support](https://www.microsoft.com/enterprise/services/support)
- Une fois le contrat de support en place, contactez votre responsable technique de compte qui peut vous aider à démarrer et collectez toutes les informations nécessaires.
 
>[!NOTE]
> Seuls les partenaires inscrits auprès de Microsoft Partner Network sont éligibles à l’achat du Support Premier pour les partenaires ; sinon, vous devez acheter une des offres de support mentionnées précédemment.

## <a name="installing-microsoft-security-code-analysis-extension"></a>Installation de l’extension Microsoft Security Code Analysis

1. Une fois que l’extension est partagée avec votre organisation Azure DevOps, accédez à la page de votre organisation Azure DevOps (par exemple http://dev.azure.com/contoso)
2. Cliquez sur l’icône de panier d’achat en haut à droite à côté de votre nom, puis cliquez sur Gérer les extensions. 
3. Cliquez sur l’extension Microsoft Security Code Analysis, puis lancez l’Assistant Interface utilisateur d’Azure DevOps pour démarrer l’installation.
4. Choisissez l’organisation Azure DevOps dans la liste déroulante pour installer l’extension.
5. Cliquez sur Installer. Une fois l’installation terminée, vous pouvez passer à l’utilisation de l’extension.

>[!NOTE]
> Même si vous n’avez pas d’accès, continuez les étapes d’installation et vous serez en mesure de demander l’accès auprès de l’administrateur de votre organisation Azure DevOps au cours du processus.
>
Une fois l’extension installée, les tâches de build de développement sécurisé sont visibles et disponibles pour être ajoutées à vos pipelines Azure Pipelines.

## <a name="adding-specific-build-tasks-to-your-devops-pipeline"></a>Ajout de tâches de build spécifiques à votre pipeline DevOps

1. Ouvrez votre projet d’équipe à partir de votre organisation Azure DevOps.
2. Accédez à l’onglet **Builds** sous **Pipelines**. 
3. Sélectionnez le pipeline dans lequel vous voulez ajouter les tâches de build de l’extension. 
   - Nouveau : Cliquez sur **Nouveau** et suivez les étapes détaillées pour créer un nouveau pipeline.
   - Modifier : Sélectionnez le pipeline, puis cliquez sur **Modifier** pour commencer à modifier un pipeline existant.
4. Cliquez sur + pour accéder au volet **Ajouter des tâches**.
5. Recherchez la tâche de build que vous voulez ajouter dans la liste ou en utilisant la zone de recherche, puis cliquez sur **Ajouter**. 
6. Vous pouvez maintenant continuer en spécifiant les paramètres nécessaires pour la tâche.
>[!NOTE]
>Les chemins de fichier ou de répertoire sont relatifs à la racine de votre dépôt source, et les paramètres qui spécifient les fichiers et le dossier de sortie seront remplacés par l’emplacement commun que nous avons défini sur l’agent de build.

7. Placez une nouvelle build dans la file d'attente.
> [!TIP]
>  - Pour exécuter l’analyse après votre build, placez les tâches de build de Microsoft Security Code Analysis après l’étape Publier les artefacts de build de votre build. De cette façon, votre build peut se terminer et publier les résultats avant d’exécuter les outils d’analyse statique.
>  - Cochez toujours l’option **Continuer en cas d’erreur** des tâches de build de développement sécurisé. Même si un des outils échoue, les autres peuvent s’exécuter. Il n’y a aucune interdépendance.
>  - Les tâches de génération de Microsoft Security Code Analysis échouent SEULEMENT si l’exécution de l’outil échoue, mais elles n’échouent PAS si et quand l’outil identifie des problèmes dans le code. Vous pouvez configurer votre build pour qu’elle s’arrête quand un outil identifie des problèmes dans le code, en utilisant la tâche de build **Post-analyse**.
>  - Certaines tâches de build d’Azure DevOps ne sont PAS prises en charge quand elles sont exécutées via un pipeline « Mise en production ». Il s’agit d’une limitation d’Azure DevOps. Elles ne prennent pas en charge les tâches qui publient des artefacts depuis un pipeline de mise en production.
>  - Pour obtenir la liste des variables prédéfinies dans Azure DevOps Team Build que vous pouvez spécifier en tant que paramètres, consultez [Variables de build d’Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la configuration des tâches de build, consultez notre [Guide de configuration](security-code-analysis-customize.md).

Si vous avez d’autres questions sur l’extension et les outils proposés, consultez [notre page Forum aux questions](security-code-analysis-faq.md).


