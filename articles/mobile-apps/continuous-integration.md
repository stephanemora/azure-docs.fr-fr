---
title: Automatiser le cycle de vie de vos applications avec Visual Studio App Center et les services Azure
description: Découvrez les services comme App Center, qui aident à configurer une compilation et une intégration continues de vos applications mobiles.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 9b0a9e10a88836ce83e636db20180c3692ab4429
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453188"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatisez le cycle de vie de vos application avec une compilation et une intégration continues

En tant que développeurs, vous écrivez du code que vous archivez dans le référentiel de code, mais il est possible que les validations qui y sont archivées ne soient pas toujours cohérentes. Lorsque plusieurs développeurs travaillent sur le même projet, des problèmes peuvent survenir avec l’intégration. Les équipes peuvent rencontrer des situations où les choses ne fonctionnent pas, les bogues s’accumulent et le développement du projet est retardé. Les développeurs doivent attendre que tout le code logiciel soit créé et testé pour rechercher les erreurs, ce qui rend le processus lent et moins itératif. 

Grâce à la compilation et à l’intégration continues, les développeurs peuvent simplifier la compilation et le test de leur code, car ils valident leurs modifications dans le référentiel du code, et ils effectuent aussi les tests et les vérifications dans l’environnement de compilation. De cette façon, ils exécutent toujours des tests sur leur code. Toutes les modifications apportées au code source sont générées de façon continue chaque fois qu’une validation est effectuée dans le référentiel. Lors de chaque archivage, le serveur d’intégration continue (CI) valide et exécute tout test créé par le développeur. Si les tests ne sont pas réussis, le code est renvoyé pour des modifications supplémentaires. De cette façon, les développeurs n’interrompent pas les builds créées. Ils n’ont pas non plus besoin d’exécuter tous les tests localement sur leurs ordinateurs, ce qui augmente la productivité des développeurs. 

## <a name="key-benefits"></a>Principaux avantages
- Automatisez vos builds, tests et déploiements pour les pipelines.
- Détectez les bogues et résolvez les problèmes tôt pour garantir des taux de mise en production plus élevés.
- Validez le code plus fréquemment et compilez rapidement des applications.
- Bénéficiez de plus de flexibilité pour modifier rapidement le code sans aucun problème.
- Atteignez un délai de commercialisation plus rapide pour que seul du code de bonne qualité franchisse toutes les étapes du processus.
- Apportez des modifications de code mineures plus efficacement, car les petits morceaux de code sont intégrés en une fois.
- Augmentez la transparence et la responsabilité de l’équipe pour obtenir des commentaires en continu de vos clients ainsi que de votre équipe.

Utilisez les services suivants pour activer le pipeline d’intégration continue dans vos applications mobiles.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Build](/appcenter/build/) vous aide à créer à l’aide d’une infrastructure cloud sécurisée des applications natives et multiplateformes sur lesquelles votre équipe travaille. Vous pouvez facilement connecter votre référentiel dans Visual Studio App Center et commencer à créer votre application dans le cloud à chaque validation. Vous n’avez pas à vous soucier de la configuration locale des serveurs de builds, de configurations compliquées et de code qui s’appuie sur la machine d’un collègue mais pas sur la vôtre.

Avec la puissance ajoutée des services Visual Studio App Center, vous pouvez continuer à automatiser votre flux de travail. Vous pouvez publier automatiquement des builds vers des testeurs et des magasins d’applications publics avec App Center Distribute. Vous pouvez également exécuter des tests automatisés de l’interface utilisateur sur des milliers de configurations d’appareil et de système d’exploitation réelles dans le cloud avec App Center Test.

**Fonctionnalités clés**
- Configurez l’intégration continue en quelques minutes et créez des applications plus fréquemment et plus rapidement.
- Intégration à GitHub, BitBucket, Azure DevOps et GitLab.
- Créez des builds rapides et sécurisées sur des ordinateurs gérés hébergés dans le cloud.
- Autorisez vos builds à lancer le test et vérifiez si l’application est compilée dans des appareils iOS et Android réels.
- Obtenez la prise en charge native et multiplateforme pour iOS, Android, macOS, Windows, Xamarin et React Native.
- Personnalisez vos builds en ajoutant des scripts après clonage, avant génération et après génération.

**Informations de référence**
- [S’inscrire avec Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Prise en main d’App Center Build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) dans Azure DevOps est un service complet d’intégration continue (CI) et de livraison continue (CD) qui fonctionne avec votre fournisseur Git préféré. Il peut être déployé sur la plupart des services cloud les plus populaires, y compris Azure. Vous pouvez commencer avec votre code sur GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud ou Azure Repos. Une fois déployé, il vous permet d’automatiser la génération, le test et le déploiement de votre code pour Microsoft Azure, Google Cloud Platform, ou Amazon Web Services (AWS).

**Fonctionnalités clés**
- **Expérience basée sur les tâches simplifiée pour la configuration d’un serveur CI :** Configurez un serveur CI pour les applications mobiles natives (Android, iOS et Windows) et multiplateformes (Xamarin, Cordova et React Native), en plus des technologies serveur basées ou non sur Microsoft (Node.js, Java).
- **Tous les langages, plateformes et cloud :** Compiler, tester et déployer des applications Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android et iOS. Exécutez en parallèle sur Linux, macOS et Windows. Déployez sur des fournisseurs de cloud comme Azure, AWS et Google Cloud Platform. Distribuez des applications mobiles via des canaux bêta et des App Stores.
- **Prise en charge native des conteneurs :** Créez facilement des conteneurs et envoyez-les à n’importe quel registre. Déployez des conteneurs sur des hôtes indépendants ou sur Kubernetes.
- **Workflows avancés :** Créez facilement des chaînes de génération et des builds multiphases. Obtenez la prise en charge de YAML, l’intégration des tests, les portes de mise en production, la création de rapports, etc.
- **Extensible :** Utilisez un éventail de tâches de génération, de test et de déploiement créées par la communauté, dont des centaines d’extensions allant de Slack à SonarCloud. Vous pouvez même déployer à partir d’autres systèmes CI, tels que Jenkins. Les webhooks et API REST peuvent faciliter l’intégration.
- **Builds hébergées dans le cloud gratuitement :** Ces builds sont disponibles pour les référentiels publics et privés.
- **Prise en charge du déploiement vers d’autres fournisseurs cloud :** Les fournisseurs incluent AWS et Google Cloud Platform.

**Informations de référence**
- [Guide Prise en main d’Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Prise en main d’Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Démarrages rapides](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Pour vous aider à choisir le bon service pour les builds de votre application, consultez l’article qui compare [App Center Build et Azure Pipelines](/appcenter/build/choose-between-services).
