---
title: Automatiser le déploiement et la mise en production de vos applications mobiles avec Visual Studio App Center et les services Azure
description: Découvrez les services comme App Center qui aident à configurer un pipeline de livraison continue pour vos applications mobiles.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235351"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatiser le déploiement et la mise en production de vos applications mobiles avec des services de livraison continue

En tant que développeurs, vous écrivez du code que vous archivez dans le référentiel de code, mais il est possible que les validations qui y sont archivées ne soient pas toujours cohérentes. Lorsque plusieurs développeurs travaillent sur le même projet, des problèmes peuvent survenir avec l’intégration. Les équipes peuvent rencontrer des situations où les choses ne fonctionnent pas, les bogues s’accumulent et le développement du projet est retardé. Les développeurs doivent attendre que tout le code logiciel soit créé et testé pour rechercher les erreurs, ce qui rend le processus lent et moins itératif.

La livraison continue permet d’automatiser le déploiement et la mise en production de vos applications mobiles. Peu importe si vous distribuez l’application à un groupe de testeurs ou d’employés d’entreprise (pour effectuer des tests bêta), ou à un magasin d’applications (à des fins de production). La livraison continue rend les déploiements moins risqués et encourage des itérations rapides. Vous pouvez également publier de nouvelles modifications pour vos clients de manière continue.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuer des fichiers binaires d’application à des bêta-testeurs
L’exécution de tests bêta sur votre application mobile est l’une des étapes critiques du processus de développement d’applications. Elle vous aide à détecter précocement des bogues et autres problèmes dans votre application. Les commentaires améliorent la qualité de votre application lorsque vous préparez celle-ci pour une utilisation en production.

Utilisez les services suivants pour activer un pipeline de livraison continue dans vos applications mobiles.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Le service [App Center Distribute](/appcenter/distribution/) est un outil permettant aux développeurs de publier rapidement des builds sur des appareils. Avec une expérience complète de portail d’installation, le service App Center Distribute est une solution puissante pour la distribution à des testeurs d’application bêta. Il s’agit également d’une alternative pratique à la distribution via des magasins d’applications publics. Les développeurs peuvent automatiser davantage leur workflow de distribution avec App Center Build et les intégrations des App Stores publics.

**Fonctionnalités clés**
- Distribuez votre application à des bêta-testeurs et des utilisateurs et vérifiez que tous vos testeurs utilisent la dernière version de votre application.
- Informez les testeurs des nouvelles mises en production sans qu’ils doivent relancer le flux de téléchargement.
- Gérez des groupes de distribution pour les différentes versions de votre application.
- Distribuez aux magasins : 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Bénéficiez d’une prise en charge de plateforme pour iOS, Android, macOS, tvOS, Xamarin, React Native, Unity et Cordova.
- Inscrivez automatiquement les appareils iOS dans votre profil de provisionnement.

**Informations de référence**
- [S’inscrire auprès de Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Prise en main d’App Center Distribute](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) est un service complet d’intégration continue (CI) et de livraison continue (CD) qui fonctionne avec votre fournisseur Git préféré. La solution Azure Pipelines peut être déployée sur la plupart des services cloud de premier plan, tels que les services Azure. Vous pouvez commencer avec votre code sur GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud ou Azure Repos. Vous pouvez ensuite automatiser la génération, le test et le déploiement de votre code sur Microsoft Azure, Google Cloud Platform, ou Amazon Web Services (AWS).

**Fonctionnalités clés**
- **Expérience simplifiée basée sur les tâches pour la configuration d’un serveur CI :** Configurez un serveur CI pour les applications mobiles natives (Android, iOS et Windows) et multiplateformes (Xamarin, Cordova et React Native).
- **Tout langage, plateforme et cloud :** Compilez, testez et déployez des applications Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android et iOS. Exécutez en parallèle sur Linux, macOS et Windows. Déployez sur des fournisseurs de cloud comme Azure, AWS et Google Cloud Platform. Distribuez des applications mobiles via des canaux bêta et des App Stores.
- **Prise en charge native des conteneurs :** Créez facilement des conteneurs et envoyez-les à n’importe quel registre. Déployez des conteneurs sur des hôtes indépendants ou sur Kubernetes.
- **Flux de travail et fonctionnalités avancés :** Créez facilement des chaînes de génération et des builds multiphases. Bénéficiez de la prise en charge de YAML, de l’intégration des tests, des portes de mise en production, de la création de rapports, etc.
- **Extensible :** Utilisez un éventail de tâches de génération, de test et de déploiement créées par la communauté, dont des centaines d’extensions allant de Slack à SonarCloud. Vous pouvez même déployer à partir d’autres systèmes CI, tels que Jenkins. Les webhooks et API REST peuvent faciliter l’intégration.
- **Builds hébergées dans le cloud gratuitement :** Ces builds sont disponibles pour les référentiels publics et privés.
- **Prise en charge du déploiement vers d’autres fournisseurs cloud :** Les fournisseurs incluent AWS et Google Cloud Platform.

**Informations de référence**
- [Guide Prise en main d’Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Prise en main d’Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribuer votre application directement aux App Stores
Une fois que votre application est prête à être utilisée en production et que vous souhaitez l’utiliser publiquement, vous devez l’envoyer à des magasins d’applications à partir desquels des clients peuvent la télécharger. Il existe plusieurs façons de distribuer votre application directement à des magasins d’applications. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Le service [App Center Distribute](/appcenter/distribution/stores/) vous permet de publier vos applications mobiles directement dans des magasins d’applications. Une fois que votre application est prête pour téléchargement par les utilisateurs finaux, vous pouvez publier ses fichiers binaires directement à partir du portail App Center. 

Vous pouvez distribuer directement à :
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Dans le magasin d’applications développé et géré par Apple, les utilisateurs peuvent parcourir et télécharger des applications développées pour les appareils iOS, MacOS, WatchOS et tvOS. Les développeurs doivent envoyer leurs applications iOS à l’Apple App Store pour une utilisation publique.

### <a name="google-play"></a>Google Play

Google Play est le magasin d’applications officiel pour le système d’exploitation Android. Les utilisateurs peuvent le parcourir et télécharger des applications développées pour les appareils Android, qui sont publiées via Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) est un service cloud dans l’espace de gestion de la mobilité d’entreprise, qui peut améliorer la productivité de vos employés, tout en protégeant les données de votre entreprise. Avec Intune, vous pouvez effectuer les opérations suivantes :
- Gérer les appareils mobiles et PC que vos employés utilisent pour accéder aux données d’entreprise.
- Gérer les applications mobiles que vos employés utilisent.
- Protéger les informations de votre entreprise grâce au contrôle de la manière dont vos employés y accèdent et les partagent.
- Vérifier que les appareils et applications sont conformes aux exigences de sécurité de l’entreprise.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Déployer des mises à jour directement sur les appareils des utilisateurs

### <a name="codepush"></a>CodePush
Le service [CodePush](/appcenter/distribution/codepush/) dans App Center permet aux développeurs Apache Cordova et React Native de déployer des mises à jour d’applications mobiles directement sur les appareils de leurs utilisateurs. Il fait office de référentiel central sur lequel les développeurs peuvent publier certaines mises à jour telles que JavaScript, HTML, CSS ainsi que des modifications d’image. Les applications peuvent ensuite rechercher des mises à jour dans le référentiel à l’aide de kits de développement logiciel (SDK) clients fournis. De cette façon, vous pouvez avoir un modèle d’engagement plus déterministe et direct avec vos utilisateurs, tandis que vous résolvez des bogues ou ajoutez des fonctionnalités mineures. Vous n’êtes pas obligé de régénérer ou redistribuer un fichier binaire via des magasins d’applications publics.

**Fonctionnalités clés**
- Les développeurs Cordova et React Native peuvent déployer des mises à jour d’applications mobiles directement sur les appareils de leurs utilisateurs sans devoir les publier dans magasin d’applications.
- Ces fonctionnalités sont utiles pour la résolution des bogues, ainsi que l’ajout et la suppression de fonctionnalités mineures sans devoir recréer et redistribuer de fichier binaire via des magasins.

**Informations de référence**
- [S’inscrire auprès de Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Prise en main de CodePush dans App Center](/appcenter/distribution/codepush/)
- [Interface CLI CodePush](/appcenter/distribution/codepush/cli)