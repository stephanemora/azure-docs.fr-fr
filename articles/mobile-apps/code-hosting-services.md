---
title: Héberger votre code source d’application mobile dans le cloud avec GitHub et Azure DevOps
description: Découvrez les services permettant d’héberger votre code d’application mobile dans le cloud avec les services Microsoft.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8662b9f77614339ff514fa4fcf97dc1ee8fc7417
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454485"
---
# <a name="cloud-hosted-source-code-management-services"></a>Services de gestion du code source hébergé dans le cloud
Si vous avez des équipes de développement dont plusieurs membres de l’équipe travaillent sur la même base de code, vous devez trouver un emplacement approprié pour héberger votre code. Le stockage des données dans le Cloud et la présence d’un référentiel centralisé permettent à tout le monde de charger, de modifier et de gérer les fichiers de code. Ils peuvent également interagir avec d’autres développeurs sur des projets. Le code peut être facilement accessible, quel que soit l’endroit où vous vous trouvez, à condition que vous disposiez d’une connexion Internet.

L’hébergement cloud est beaucoup plus facile que les options locales. Elle nécessite moins de configuration matérielle et permet aux organisations d’effectuer le processus d’implémentation de manière plus agile.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Avantages de l’hébergement du code source dans le cloud
- **Stockage cloud centralisé :** Affichez et gérez vos données où que vous soyez.
- **Meilleure collaboration et code plus propre :** Effectuez le suivi du code au sein des équipes et gérez les projets pour garantir la livraison continue d’excellents logiciels.
- **Plus facile de s’impliquer :** Contribuez facilement à vos projets.
- **Cycle de mise en version plus rapide :** Travaillez plus rapidement dans vos équipes et contribuez facilement à vos projets.
- **Réduisez le coût :** Ne vous souciez pas de la maintenance de votre propre matériel, de vos serveurs, de votre VPN, etc.

Utilisez les services suivants pour héberger les données de votre application dans le cloud.

## <a name="github"></a>GitHub
[GitHub](https://github.com/) est un service d’hébergement de dépôts Open Source qui héberge des projets de code source dans différents langages de programmation. GitHub effectue le suivi des différentes modifications apportées à chaque itération.

**Fonctionnalités clés**
- Utilisez l’hébergement de code pour conserver tout votre code dans un même emplacement. Les référentiels privés, publics et open source sont tous équipés d’outils pour vous aider à héberger, créer des versions et publier du code.
- Examinez le code et utilisez les outils de révision intégrés pour faire de la révision du code une partie essentielle du processus de toute équipe :
    - Protégez des branches, proposez des modifications et demandez des revues.
    - Repérez les différences, commentez en contexte et obtenez des commentaires clairs.
- Assurez la coordination durant les phases préliminaires, restez aligné et faites-en plus avec les outils de gestion de projet de GitHub.
    - Obtenez une vue d’ensemble du projet.
    - Utilisez les tableaux de tâches qui sont juste à côté de votre code dans GitHub.
    - Faites glisser des cartes pour assigner des problèmes ou des demandes de tirage (pull requests) aux membres de l’équipe.
    - Définir des jalons pour organiser et suivre la progression.
    - Écrivez des notes pour capturer des idées qui peuvent être utiles, mais qui n’entrent pas dans le cadre d’un problème particulier ou d’une demande de tirage (pull request).
- Découvrez et choisissez facilement les bons outils pour une meilleure communication et automatisation du travail en achetant des applications à partir de la [Place de marché GitHub](https://github.com/marketplace).
- Gérez et développez des équipes à l’aide des éléments suivants : 
    - Des rôles d’utilisateur pour aider à organiser les équipes et les autorisations d’accès.
    - Outils de thèmes de discussion pour tenir des conversations sur le sujet et l’équipe.
    - Instructions de la communauté pour configurer rapidement de nouveaux membres de l’équipe avec un compte.
- Parcourez et marquez des projets populaires pour les suivre.
- Étendez votre réseau et tirez des enseignements des travaux de collègues évoluant dans le même secteur.

**Informations de référence**
- [GitHub](https://github.com/)
- [Guides GitHub](https://guides.github.com/)
- [Forum de la communauté GitHub](https://github.community/)
- [Place de marché GitHub](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/) prend en charge [Azure Repos](https://azure.microsoft.com/services/devops/repos/) et [Team Foundation Version Control (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) comme options de contrôle de code source. Il offre une quantité illimitée de référentiels privés gratuits avec des revues de code collaboratives, une gestion avancée des fichiers, une recherche de code et des stratégies de branche pour garantir un code de haute qualité. Azure Repos est idéal pour les petits projets et les grandes organisations qui nécessitent une prise en charge native Azure Active Directory et des stratégies avancées.
    
**Fonctionnalités clés**
- Utilisez un référentiel de code source git hébergé sur le Cloud illimité pour vos dépôts publics et privés :
    - Obtenir la prise en charge de n’importe quel client git.
    - Utilisez des hooks Web et l’intégration d’API.
- Lancez votre prochain build à partir d’une demande de tirage Repos.
    - Collaborez pour créer un meilleur code grâce à des threads de discussion et à l’intégration continue pour chaque modification.
    - Configurez l’intégration continue/la livraison continue (CI/CD) pour déclencher automatiquement les builds, les tests et les déploiements avec chaque requête de tirage terminée. Vous pouvez utiliser Azure Pipelines ou vos outils.
    - Protégez la qualité de votre code à l’aide de stratégies de branche.
- Gérez le contrôle de version centralisé avec Team Foundation Version Control, qui comprend la révision du code.
- Connectez-vous à votre code à l’aide de Xcode, Eclipse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code et bien plus encore.
- Utilisez une recherche de code sémantique puissante.
- Profitez des avantages offerts par les fonctionnalités adaptées à l’entreprise. Azure DevOps offre une intégration native avec Azure Active Directory, qui simplifie le processus de gestion de l’accès à vos référentiels de code.
- Garantissez la qualité du code avec des stratégies de branche, telles qu’un nombre minimal de révisions de code, des exigences pour les builds réussies et l’application de stratégies de fusion git.
- Connectez votre environnement de développement favori pour accéder aux référentiels et gérer le travail.

**Informations de référence**
- [Bien démarrer avec Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Documentation Azure Repos](/azure/devops/repos/?view=azure-devops)