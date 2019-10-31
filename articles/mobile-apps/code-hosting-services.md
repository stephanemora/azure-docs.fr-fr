---
title: Héberger votre code source d’application mobile dans le cloud avec GitHub et Azure DevOps
description: Découvrez les services permettant d’héberger votre code d’application mobile dans le cloud avec les services Microsoft.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: f0af2ee5e62bbdbfb5d18cffc3e3ed62edec81f3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795125"
---
# <a name="cloud-hosted-source-code-management-services"></a>Services de gestion du code source hébergé dans le cloud
Si vous avez des équipes de développement dont plusieurs membres travaillent sur la même base de code, vous devez trouver un emplacement approprié pour héberger votre code. Le stockage des données dans le cloud et l’existence d’un référentiel centralisé permettent à tout le monde de charger, de modifier et de gérer les fichiers de code, et d’interagir avec d’autres développeurs sur des projets. En outre, cela rend le code facilement accessible quel que soit l’endroit où vous vous trouvez, à condition de disposer d’une connexion Internet.

L’hébergement dans le cloud est beaucoup plus simple que les options locales, car il nécessite moins de configuration matérielle et permet aux organisations d’effectuer le processus d’implémentation de manière plus agile.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Avantages de l’hébergement du code source dans le cloud
- **Stockage cloud centralisé** pour afficher et gérer vos données où que vous soyez.
- Favorise **la collaboration et la propreté du code** parmi les équipes, le suivi du code et la gestion des projets, afin de garantir la livraison continue d’excellents logiciels.
- Encourage la **participation** et facilite la **contribution** à vos projets.
- **Raccourcissement du cycle de mise en production**, car votre équipe peut travailler plus rapidement et **contribuer facilement** à vos projets.
- **Réduisez les coûts** sans avoir à vous soucier de la maintenance de votre propre matériel, de vos serveurs, de votre VPN, etc.

Utilisez les services suivants pour héberger les données de votre application dans le cloud.

## <a name="github"></a>GitHub
[GitHub](https://github.com/) est un service d’hébergement de dépôts Open Source qui héberge des projets de code source dans différents langages de programmation et effectue le suivi des différentes modifications apportées à chaque itération.

**Fonctionnalités clés**
- **Hébergement de code** avec l’ensemble de votre code à un seul emplacement. Privés, publics ou Open Source, tous les dépôts sont équipés d’outils pour vous aider à héberger, gérer les versions et publier du code.
- La **revue de code** transparente et les outils de revue intégrés font de la revue de code une partie essentielle de tout processus de travail en équipe.
    - Protégez des branches, proposez des modifications et demandez des revues.
    - Observez les différences et les commentaires dans le contexte, et obtenez des commentaires clairs.
- Assurez la coordination durant les phases préliminaires, restez aligné et faites-en plus avec les **outils de gestion de projet** de GitHub.
    - Obtenez une vue d’ensemble du projet.
    - **Tableaux de tâches** situés juste en regard de votre code dans GitHub.
    - **Glissez-déposez des cartes** pour vous permettre d’affecter des problèmes ou des demandes de tirage (pull requests) aux membres de l’équipe.
    - **Jalons** pour organiser et suivre la progression.
    - **Notes** pour capturer des idées susceptibles d’être utiles, mais qui n’entrent pas dans le cadre d’une demande de tirage ou d’un problème particulier.
- Découvrez et choisissez facilement les bons outils pour une meilleure communication et automatisation du travail en achetant des applications à partir de la **[Place de marché GitHub](https://github.com/marketplace)** .
- **Gestion d’équipes** pour gérer et développer facilement des équipes, **rôles d’utilisateurs** pour aider à organiser les équipes et les autorisations d’accès, outils de **threads de discussion** pour veiller à ce que les conversations ne soient pas hors sujet, et **recommandations relatives à la communauté** afin de configurer rapidement des comptes pour les nouveaux membres d’équipe.
- **Parcourez** et **marquez** des projets populaires pour les suivre.
- **Étendez votre réseau et tirez des enseignements** des travaux de collègues évoluant dans le même secteur.

**Informations de référence**
- [GitHub](https://github.com/)
- [Guides GitHub](https://guides.github.com/)
- [Forum de la communauté GitHub](https://github.community/)
- [Place de marché GitHub](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/) prend en charge [Azure Repos](https://azure.microsoft.com/services/devops/repos/) et [Team Foundation Version Control (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) comme options de contrôle de code source. Il offre une quantité illimitée de référentiels privés gratuits avec des revues de code collaboratives, une gestion avancée des fichiers, une recherche de code et des stratégies de branche pour garantir un code de haute qualité. Azure Repos est idéal aussi bien pour les petits projets que pour les grandes organisations qui ont besoin d’une prise en charge native d’AAD et de stratégies avancées.
    
**Fonctionnalités clés**
- **Dépôt de code source Git hébergé dans le cloud illimité** pour vos dépôts publics et privés.
    - Prise en charge de tous les clients Git.
    - Intégration d’API et webhooks.
- **Lancez votre prochainne build** à partir d’une demande de tirage Repos.
    - Collaborez pour créer un meilleur code grâce à des threads de discussion et à l’intégration continue pour chaque modification.
    - Configurez une intégration continue/livraison continue (CI/CD) pour déclencher automatiquement des générations, des tests et des déploiements à chaque demande de tirage effectuée à l’aide d’Azure Pipelines ou de vos outils.
    - Protégez la qualité de votre code à l’aide de stratégies de branche.
- **Gestion de versions centralisée avec Team Foundation Version Control**, y compris la revue de code.
- **Connectez-vous à votre code** à l’aide de Xcode, Eclipse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code et bien plus encore.
- **Puissante recherche de code sémantique**.
- **Prêt pour une utilisation en entreprise**, car il offre une intégration native avec Azure Active Directory (AD), ce qui simplifie le processus de gestion de l’accès à vos référentiels de code.
- **Garantissez la qualité du code** avec des stratégies de branche telles que le nombre minimal de revues de code, l’exigence de builds réussies et l’application de stratégies de fusion Git.
- **Connectez votre environnement de développement favori** pour accéder aux référentiels et gérer le travail.

**Informations de référence**
- [Bien démarrer avec Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Documentation Azure Repos](/azure/devops/repos/?view=azure-devops)