---
title: Automatiser le déploiement et la mise en production de vos applications mobiles avec Visual Studio App Center et les services Azure
description: Découvrez les services comme App Center qui aident à configurer un pipeline de livraison continue pour vos applications mobiles.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 3b8305d02345abe98b674a8ed61626eaf6b5528d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795117"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatiser le déploiement et la mise en production de vos applications mobiles avec des services de livraison continue

En tant que développeurs, vous écrivez du code que vous archivez dans le dépôt de code, mais il est possible que les validations qui y sont archivées ne soient pas toujours cohérentes. Lorsque plusieurs développeurs travaillent sur un même projet, des problèmes surviennent lors de l’intégration et l’équipe doit faire face à des situations où des choses ne fonctionnent pas, où les bogues s’accumulent et où le développement de projets est retardé. Les développeurs doivent attendre que tout le code logiciel soit créé et testé pour rechercher les erreurs, ce qui rend le processus lent et moins itératif.

Avec la **livraison continue**, vous automatisez le déploiement et la mise en production de vos applications mobiles, que vous distribuiez l’application à un groupe de testeurs ou d’employés de l’entreprise (pour les tests bêta) ou dans l’App Store (pour la production). Cela rend les déploiements moins risqués, encourage les itérations rapides et vous permet de publier de nouvelles modifications pour vos clients de manière continue.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuer des fichiers binaires d’application à des bêta-testeurs
L’exécution de tests bêta sur votre application mobile est l’une des étapes critiques du processus de développement d’applications. Ces tests vous aident à rechercher les bogues et les problèmes dans votre application à un stade précoce et les commentaires qui améliorent la qualité de votre application pour la préparer à une utilisation en production.

Utilisez les services suivants pour activer le pipeline de livraison continue dans vos applications mobiles.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Distribute](/appcenter/distribution/) est un outil qui permet aux développeurs de publier rapidement des builds sur les appareils des utilisateurs finaux. Avec une expérience d’installation complète du portail, Distribute est non seulement une solution puissante pour la distribution des testeurs d’application bêta, mais également une alternative pratique à la distribution via les App Stores publics. Les développeurs peuvent automatiser davantage leur workflow de distribution avec App Center Build et les intégrations des App Stores publics.

**Fonctionnalités clés**
- **Distribuez votre application à des bêta-testeurs et des utilisateurs** et vérifiez que tous vos testeurs utilisent la dernière version de votre application.
- **Informez les testeurs des nouvelles versions** sans qu’ils ne doivent effectuer à nouveau le téléchargement.
- **Gérez les groupes de distribution** pour les différentes versions de votre application.
- **Distribution aux Stores** 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- **Prise en charge de plateformes** : iOS, Android, macOS, tvOS, Xamarin, React Native, Unity, Cordova.
- Inscrivez automatiquement les appareils iOS dans votre profil de provisionnement.

**Informations de référence**
- [S’inscrire auprès d’App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Prise en main d’App Center Distribute](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) est un service complet d’intégration continue (CI) et de livraison continue (CD) qui fonctionne avec votre fournisseur Git préféré et peut être déployé sur la plupart des principaux services cloud, dont Azure. Vous pouvez commencer avec votre code sur GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud ou Azure Repos. Une fois déployé, il vous permet d’automatiser la génération, le test et le déploiement de votre code pour Microsoft Azure, Google Cloud Platform, ou Amazon Web Services.

**Fonctionnalités clés**
- Expérience simplifiée basée sur les tâches pour la configuration d’un serveur CI à la fois pour les **applications mobiles natives (Android, iOS et Windows) et multiplateformes (Xamarin, Cordova et React Native)** .
- **Tous les langages, plateformes et clouds** : générez, testez et déployez des applications Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android et iOS. Exécutez en parallèle sur Linux, macOS et Windows. Déployez sur des fournisseurs de cloud comme Azure, AWS et GCP. Distribuez des applications mobiles via des canaux bêta et des App Stores.
- **Prise en charge native des conteneurs** : créez facilement des conteneurs et envoyez-les à n’importe quel registre. Déployez des conteneurs sur des hôtes indépendants ou sur Kubernetes.
- **Fonctionnalités et workflows avancés** : tirez parti du chaînage de builds facile et des builds multiphases. Prise en charge de YAML, de l’intégration des tests, des portes de mise en production, de la création de rapports, etc.
- **Extensible** : utilisez un éventail de tâches de génération, de test et de déploiement créées par la communauté ainsi que des centaines d’extensions allant de Slack à SonarCloud. Vous pouvez même déployer à partir d’autres systèmes CI, tels que Jenkins. Les webhooks et API REST facilitent l’intégration.
- **Des builds hébergées gratuitement dans le cloud** pour les dépôts publics et privés.
- **Prend en charge le déploiement sur d’autres fournisseurs de cloud** comme AWS, GCP, etc.

**Informations de référence**
- [Guide Prise en main d’Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Prise en main d’Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribuer votre application directement aux App Stores
Une fois que votre application est prête à être utilisée en production et que vous souhaitez l’utiliser publiquement, elle doit être envoyée aux App Stores où elle peut être téléchargée par les clients. Il existe plusieurs façons de distribuer votre application directement aux App Stores. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Le service [App Center Distribute](/appcenter/distribution/stores/) vous permet de publier vos applications mobiles directement sur les App Stores. Une fois que votre application est prête à être téléchargée par les utilisateurs finaux, vous pouvez publier vos fichiers binaires d’application directement à partir du portail App Center.  

Vous pouvez distribuer directement à :
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
L’App Store développé et géré par Apple permet aux utilisateurs de parcourir et de télécharger des applications développées pour les appareils iOS, MacOS, WatchOS et tvOS. Les développeurs doivent envoyer leurs applications iOS à l’Apple Store pour une utilisation publique.

### <a name="google-play"></a>Google Play

Google Play est l’App Store officiel pour le système d’exploitation Android, qui permet aux utilisateurs de parcourir et de télécharger des applications développées pour les appareils Android et publiées par le biais de Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) est un service cloud dans l’espace de gestion de la mobilité d’entreprise. Il peut améliorer la productivité de vos employés, tout en protégeant les données de votre entreprise. Avec Intune, vous pouvez 
- Gérer les appareils mobiles et PC que vos employés utilisent pour accéder aux données d’entreprise.
- Gérer les applications mobiles que vos employés utilisent.
- Protéger les informations de votre entreprise grâce au contrôle de la manière dont vos employés y accèdent et les partagent.
- Garantir que les appareils et applications sont conformes aux exigences de sécurité de l’entreprise.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Déployer des mises à jour directement sur les appareils des utilisateurs

### <a name="codepush"></a>CodePush
Le service [CodePush](/appcenter/distribution/codepush/) dans App Center permet aux développeurs Apache Cordova et React Native de déployer des mises à jour d’applications mobiles directement sur les appareils de leurs utilisateurs. Il fonctionne en tant que dépôt central sur lequel les développeurs peuvent publier certaines mises à jour (par exemple JS, HTML, CSS ainsi que des modifications d’image). Les applications peuvent ensuite rechercher des mises à jour dans le dépôt à l’aide des kits SDK clients fournis. Cela vous permet d’avoir un modèle d’engagement plus déterministe et direct avec vos utilisateurs finaux, tout en traitant des bogues ou en ajoutant des fonctionnalités mineures sans vous obliger à recréer un fichier binaire ni à le redistribuer par le biais de n’importe quel App Store public.

**Fonctionnalités clés**
- Permet aux développeurs Cordova et React Native de déployer des mises à jour d’applications mobiles directement sur les appareils de leurs utilisateurs sans publication sur un Store.
- Utile pour la résolution des bogues ou l’ajout/la suppression de fonctionnalités mineures sans vous obliger à recréer un fichier binaire et à le redistribuer par le biais des Stores respectifs.

**Informations de référence**
- [S’inscrire auprès d’App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Prise en main de CodePush dans App Center](/appcenter/distribution/codepush/)
- [Interface CLI CodePush](/appcenter/distribution/codepush/cli)