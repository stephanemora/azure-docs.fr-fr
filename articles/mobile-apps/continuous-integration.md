---
title: Automatiser le cycle de vie de vos applications avec Visual Studio App Center et les services Azure
description: Découvrez les services comme App Center, qui aident à configurer une compilation et une intégration continues de vos applications mobiles.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 07e8e0edc4638c6c0bf9acc205175aa731c0f8bc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795101"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatisez le cycle de vie de vos application avec une compilation et une intégration continues

En tant que développeurs, vous écrivez du code que vous archivez dans le référentiel de code, mais il est possible que les validations qui y sont archivées ne soient pas toujours cohérentes. Lorsque plusieurs développeurs travaillent sur un même projet, des problèmes surviennent lors de l’intégration et l’équipe doit faire face à des situations où des choses ne fonctionnent pas, où les bogues s’accumulent et où le développement de projets est retardé. Les développeurs doivent attendre que tout le code logiciel soit créé et testé pour rechercher les erreurs, ce qui rend le processus lent et moins itératif. 

Grâce à la **Compilation et à l’intégration continues**, les développeurs peuvent simplifier la compilation et le test de leur code, car non seulement ils valident leurs modifications dans le référentiel du code, mais encore ils effectuent les tests et les vérifications dans l’environnement de compilation, ce qui leur permet de toujours exécuter les tests sur leur code. Toutes les modifications apportées au code source sont générées de façon continue chaque fois qu’une validation est effectuée dans le référentiel. Lors de chaque archivage, le serveur CI valide et exécute tout test créé par le développeur. Si les tests ne sont pas réussis, le code est renvoyé pour des modifications supplémentaires. Ceci permet au développeur de ne pas rompre les builds créées et de ne pas exécuter tous les tests localement sur son ordinateur, donc d’augmenter sa productivité. 

## <a name="key-benefits"></a>Principaux avantages
- Pipeline de génération, de test et de déploiement **automatisé**.
- **Détectez les bogues et résolvez les problèmes** tôt pour garantir des taux de mise en production plus élevés.
- **Validez le code** plus fréquemment et compilez rapidement des applications.
- **Flexibilité** pour modifier rapidement le code sans aucun problème.
- **Un délai de commercialisation plus rapide** garantit que seul du code de bonne qualité franchit toutes les étapes du processus.
- **Petites modifications du code**, car ceci permet d’intégrer de petits morceaux de code en une seule fois.
- **La transparence et la responsabilité accrues de l’équipe** vous permettent d’obtenir **des commentaires en continu** non seulement de vos clients, mais également de votre équipe.

Utilisez les services suivants pour activer le pipeline d’intégration continue dans vos applications mobiles.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Le service [App Center Build](/appcenter/build/) vous aide à créer à l’aide d’une infrastructure cloud sécurisée des applications natives et multiplateformes sur lesquelles votre équipe travaille. Vous pouvez facilement connecter votre référentiel dans App Center et commencer à créer votre application dans le cloud à chaque validation, sans avoir à vous soucier de la configuration locale des serveurs de builds, des configurations compliquées ou du code compilé sur l’ordinateur d’un collègue, mais pas sur le vôtre.

Avec la puissance ajoutée des services App Center, vous pouvez continuer à automatiser votre flux de travail. Mettez automatiquement en production des builds vers des testeurs et des magasins d’applications publics avec App Center Distribute, ou exécutez des tests d’interface utilisateur automatisés sur des milliers de configurations réelles d’appareils et de systèmes d’exploitation dans le cloud avec App Center Test.

**Fonctionnalités clés**
- **Configurez l’intégration continue** en quelques minutes et créez des applications plus fréquemment et plus rapidement.
- Intégration à **GitHub, BitBucket, Azure DevOps et GitLab**.
- **Builds rapides et sécurisées** sur des ordinateurs gérés hébergés dans le cloud.
- **Autorisez vos builds à « Lancer le test »** et vérifiez si l’application est compilée dans des appareils iOS et Android réels.
- **Prise en charge native et multiplateforme** : iOS, Android, macOS, Windows, Xamarin, React Native.
- **Personnalisez vos builds** en ajoutant des scripts après clonage, avant génération et après génération.

**Informations de référence**
- [S’inscrire à App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Prise en main d’App Center Build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) dans Azure DevOps est un service complet d’intégration continue (CI) et de livraison continue (CD) qui fonctionne avec votre fournisseur Git préféré et peut être déployé sur la plupart des principaux services cloud, y compris Azure. Vous pouvez commencer avec votre code sur GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud ou Azure Repos. Une fois déployé, il vous permet d’automatiser la génération, le test et le déploiement de votre code pour Microsoft Azure, Google Cloud Platform, ou Amazon Web Services.

**Fonctionnalités clés**
- Expérience simplifiée basée sur les tâches pour la configuration d’un serveur CI pour les applications mobiles natives (Android, iOS et Windows) et multiplateformes (Xamarin, Cordova et React Native), en plus des technologies serveur basées ou non sur Microsoft (Node.js, Java).
- **Tous les langages, plateformes et clouds** : compiler, tester et déployer des applications Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android et iOS. Exécutez en parallèle sur Linux, macOS et Windows. Déployez vers des fournisseurs de cloud tels qu’Azure, AWS et GCP. Distribuez des applications mobiles via des canaux bêta et des magasins d’applications.
- **Support de conteneurs natifs** : créez facilement de nouveaux conteneurs et envoyez-les à n’importe quel registre. Déployez des conteneurs vers des hôtes indépendants ou vers Kubernetes.
- **Flux de travail avancés** : tirez parti du chaînage de builds facile et des builds multiphases. Prise en charge de YAML, de l’intégration des tests, des portes de mise en production, de la création de rapports, etc.
- **Extensible** : utilisez une gamme de tâches de compilation, de test et de déploiement créées par la communauté, des centaines d’extensions de Slack à SonarCloud. Vous pouvez même déployer à partir d’autres systèmes CI, comme Jenkins, et utiliser des webhooks et des API REST pour vous aider à intégrer.
- **Des builds hébergées gratuitement dans le cloud** pour les référentiels publics et privés.
- **Prend en charge le déploiement vers d’autres fournisseurs de cloud** comme Amazon Web Services, Google Cloud Platform, etc.

**Informations de référence**
- [Prise en main du guide Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Prise en main d’Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Démarrages rapides](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Afin de choisir le bon service pour les builds de votre application, suivez l’article qui compare [App Center Build et Azure Pipelines](/appcenter/build/choose-between-services).
