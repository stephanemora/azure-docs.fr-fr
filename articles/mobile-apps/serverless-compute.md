---
title: Créer un back-end d’application mobile serverless avec Azure Functions et d’autres services
description: Apprenez-en davantage sur les services de calcul permettant de créer un back-end d’application mobile solide et serverless.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240144"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Créer des composants de back-end mobile avec les services de calcul
Chaque application mobile a besoin d’un back-end qui est responsable du stockage des données, de la logique métier et de la sécurité. La gestion de l’infrastructure pour héberger et exécuter du code back-end vous oblige à dimensionner, provisionner et mettre à l’échelle plusieurs serveurs. Vous devez également gérer les mises à jour du système d’exploitation et le matériel impliqué, ainsi qu’appliquer des correctifs de sécurité. Vous devez ensuite surveiller les performances, la disponibilité et la tolérance de panne de tous ces composants d’infrastructure. 

L’architecture serverless est très utile pour ce type de scénario, car vous n’avez aucun serveur à gérer ni aucune mise à jour logicielle ou matérielle associée à gérer. L’architecture serverless permet aux développeurs d’économiser beaucoup de temps et de frais, ce qui est synonyme de raccourcissement du Time-to-Market et d’un meilleur ciblage des énergies envers la création d’applications.

## <a name="benefits-of-compute"></a>Avantages du calcul
- L’abstraction des serveurs signifie que vous n’avez plus à vous soucier de l’hébergement, de la mise à jour corrective et de la sécurité. Vous pouvez ainsi vous concentrer uniquement sur le code.
- La mise à l’échelle instantanée et efficace garantit le provisionnement automatique ou à la demande des ressources, quelle que soit l’échelle dont vous avez besoin.
- Haute disponibilité et tolérance de panne.
- Grâce à la micro-facturation, vous êtes certain d’être facturé uniquement quand votre code est réellement en cours d’exécution.
- Le code s’exécute dans le cloud écrit dans le langage de votre choix.

Utilisez les services suivants pour activer les fonctionnalités de calcul serverless dans vos applications mobiles.

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/) est une expérience de calcul pilotée par événements, qui vous permet d’exécuter du code écrit dans le langage de programmation de votre choix sans vous soucier des serveurs. Vous n’avez pas besoin de gérer l’application ou l’infrastructure sur laquelle l’exécuter. Les fonctions sont mises à l’échelle à la demande, et vous payez uniquement en fonction du temps d’exécution de votre code. Azure Functions est un excellent moyen d’implémenter une API pour une application mobile. Les fonctions sont faciles à implémenter et à gérer et sont accessibles via HTTP.

**Fonctionnalités clés**
- Pilotage par événements et scalabilité : vous pouvez utiliser des déclencheurs et des liaisons pour définir le moment où une fonction est appelée et les données auxquelles elle se connecte.
- Intégration de vos propres dépendances : Azure Functions prenant en charge NuGet et NPM, vous pouvez utiliser vos bibliothèques préférées.
- La sécurité intégrée vous permet de protéger les fonctions déclenchées par HTTP à l’aide de fournisseurs OAuth comme Azure Active Directory, Facebook, Google, Twitter et Microsoft Account.
- Intégration simplifiée avec différents [services Azure](/azure/azure-functions/functions-overview) et offres SaaS (software as a Service).
- La flexibilité en matière de développement vous permet de coder vos fonctions directement dans le portail ou de configurer une intégration continue et de déployer votre code par le biais de GitHub, Azure DevOps Services et d’autres outils de développement pris en charge.
- Le runtime Azure Functions est open source et disponible sur [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
- L’expérience de développement améliorée vous permet de coder, de tester et de déboguer localement à l’aide de votre éditeur préféré ou d’une interface web facile à utiliser, avec supervision à l’aide d’outils intégrés et fonctionnalités DevOps intégrées.
- Ensemble de langages de programmation et d’options d’hébergement : développez à l’aide de C#, Node.js, Java, JavaScript ou Python.
- Avec le modèle de tarification à l’utilisation, seul le temps passé à exécuter votre code vous est facturé.

**Informations de référence**
- [Azure portal](https://portal.azure.com)
- [Documentation Azure Functions](/azure/azure-functions/)
- [Guide du développeur Azure Functions](/azure/azure-functions/functions-reference)
- [Démarrages rapides](/azure/azure-functions/functions-create-first-function-vs-code)
- [Exemples](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
Avec l’offre [Azure App Service](https://azure.microsoft.com/services/app-service/), vous pouvez créer et héberger des applications web et des API RESTful dans le langage de programmation de votre choix sans gérer l’infrastructure. Elle propose une mise à l’échelle automatique et une haute disponibilité, prend en charge à la fois Windows et Linux et permet des déploiements automatisés à partir de GitHub, Azure DevOps ou n’importe quel dépôt Git.

**Fonctionnalités clés**
- Plusieurs langages et frameworks : prise en charge d’ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Vous pouvez également exécuter PowerShell et d’autres scripts ou exécutables comme services en arrière-plan.
- Optimisation DevOps via l’intégration et le déploiement continus avec Azure DevOps, GitHub, BitBucket, Docker Hub ou Azure Container Registry. Gérez vos applications dans App Service à l’aide d’Azure PowerShell ou de la CLI interplateforme.
- Mise à l’échelle globale avec une haute disponibilité : effectuez un scale-up ou un scale-out manuellement ou automatiquement.
- Connexion aux plateformes SaaS et données locales : choisissez parmi plus de 50 connecteurs pour des systèmes d’entreprise tels que SAP, des services SaaS tels que Salesforce et des services Internet tels que Facebook. Accédez aux données locales à l’aide de connexions hybrides et de réseaux virtuels Azure.
- App Service est conforme aux normes ISO, SOC et PCI. Authentifiez les utilisateurs avec Azure Active Directory ou avec une connexion à un réseau social (Google, Facebook, Twitter et Microsoft). Créez des restrictions par adresse IP et gérez les identités de service.
- Modèles d’application pour choisir parmi une liste complète de modèles d’application sur la Place de marché Azure, tels que WordPress, Joomla et Drupal.
- L’intégration Visual Studio avec les outils dédiés de Visual Studio simplifie le travail de création, de déploiement et de débogage.

**Informations de référence**
- [Azure portal](https://portal.azure.com/)
- [Documentation Azure App Service](/azure/app-service/)
- [Démarrages rapides](/azure/app-service/app-service-web-get-started-dotnet)
- [Exemples](/azure/app-service/samples-cli)
- [Tutoriels](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) gère votre environnement Kubernetes hébergé. AKS permet de déployer et de gérer rapidement et facilement des applications conteneurisées sans expertise d’orchestration de conteneurs. Il élimine également la charge des opérations en cours et la maintenance. AKS provisionne, met à niveau et met à l’échelle des ressources à la demande, sans mettre vos applications hors ligne.

**Fonctionnalités clés**
- Migrez facilement des applications existantes vers des conteneurs et exécutez-les dans AKS.
- Simplifiez le déploiement et la gestion des applications basées sur des microservices.
- Sécurisez DevOps pour AKS afin d’obtenir un bon compromis entre vitesse et sécurité, et fournir du code plus rapidement à grande échelle.
- Effectuez facilement une mise à l’échelle à l’aide d’AKS et d’Azure Container Instances pour provisionner des pods dans Container Instances qui démarrent en quelques secondes.
- Déployez et gérez des appareils IoT à la demande.
- Entraînez des modèles Machine Learning à l’aide d’outils tels que TensorFlow et KubeFlow.

**Informations de référence**
- [Azure portal](https://portal.azure.com/)
- [Documentation sur Azure Kubernetes Service](/azure/aks/)
- [Démarrages rapides](/azure/aks/kubernetes-walkthrough-portal)
- [Tutoriels](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances](https://azure.microsoft.com/services/container-instances/) est une excellente solution pour les scénarios qui peuvent fonctionner dans des conteneurs isolés, notamment les applications basiques, automatiser des tâches et créer des travaux. Développez rapidement des applications sans gérer de machines virtuelles.

**Fonctionnalités clés**
- Délais de démarrage plus courts, car Azure Container Instances peut démarrer des conteneurs dans Azure en quelques secondes, sans avoir à configurer ni gérer des machines virtuelles.
- Connectivité IP publique et nom DNS personnalisé.
- Sécurité de niveau hyperviseur qui garantit que votre application se retrouve aussi isolée dans un conteneur que dans une machine virtuelle.
- Tailles personnalisées pour une utilisation optimale en autorisant les spécifications exactes des cœurs d’unité centrale et de la mémoire. Vous payez pour ce dont vous avez besoin et vous êtes facturé immédiatement, pour pouvoir ajuster vos dépenses selon vos besoins.
- Stockage persistant pour récupérer et rendre persistant l’état. Container Instances offre un montage direct des partages de fichiers Azure.
- Conteneurs Linux et Windows planifiés avec la même API.

**Informations de référence**
- [Azure portal](https://portal.azure.com/)
- [Documentation Azure Container Instances](/azure/container-instances/)
- [Démarrages rapides](/azure/container-instances/container-instances-quickstart-portal)
- [Exemples](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Tutoriels](/azure/container-instances/container-instances-tutorial-prepare-app)