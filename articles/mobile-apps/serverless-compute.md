---
title: Créer un back-end d’application mobile serverless avec Azure Functions et d’autres services
description: Apprenez-en davantage sur les services de calcul permettant de créer un back-end d’application mobile solide et serverless.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b8378be675b86ada10b8442c40a54b635ee115a9
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795197"
---
# <a name="build-mobile-backend-components-with-compute-services"></a>Créer des composants de back-end mobile avec les services de calcul
Chaque application mobile a besoin d’un back-end qui est responsable du stockage des données, de la logique métier et de la sécurité. La gestion de l’infrastructure pour héberger et exécuter le code de back-end nécessite le dimensionnement, le provisionnement et la mise à l’échelle d’un ensemble de serveurs, la gestion des mises à jour du système d’exploitation et du matériel impliqué, l’application des correctifs de sécurité, puis la supervision des performances, de la disponibilité et de la tolérance de panne de tous ces composants d’infrastructure. C’est là que l’architecture serverless est très pratique, car les développeurs n’ont aucun serveur, système d’exploitation ou mise à jour logicielle/matérielle associée à gérer. Cela permet aux développeurs d’économiser beaucoup de temps et de frais, ce qui est synonyme de raccourcissement du délai de commercialisation et d’un meilleur ciblage des énergies envers la création d’applications.

## <a name="benefits-of-compute"></a>Avantages du calcul
- Abstraction des serveurs : inutile de se soucier de l’hébergement, de la mise à jour corrective et de la sécurité, ce qui permet aux développeurs de se concentrer uniquement sur le code.
- La mise à l’échelle instantanée et efficace garantit le provisionnement automatique ou à la demande des ressources, quelle que soit l’échelle dont vous avez besoin.
- Haute disponibilité et tolérance de panne.
- Grâce à la micro-facturation, vous êtes certain de n’être facturé que quand votre code est réellement en cours d’exécution.
- Écrivez du code qui s’exécute dans le cloud dans le langage de votre choix.

Utilisez les services suivants pour activer les fonctionnalités de calcul serverless dans vos applications mobiles.

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/) est une expérience de calcul pilotée par événements, qui vous permet d’exécuter du code écrit dans le langage de programmation de votre choix sans vous soucier des serveurs. Les développeurs n’ont pas besoin de gérer l’application ou l’infrastructure sur laquelle l’exécuter. Les fonctions sont mises à l’échelle à la demande, et vous payez uniquement pour le temps d’exécution de votre code. Les fonctions Azure constituent un excellent moyen d’implémenter une API pour une application mobile, car elles sont très faciles à implémenter et à gérer, et accessibles par le biais du protocole HTTP.

**Fonctionnalités clés**
- **Pilotage par événements et scalabilité** : les développeurs peuvent utiliser **des déclencheurs et des liaisons** pour définir le moment où une fonction est appelée et les données auxquelles elle se connecte.
- **Intégration de vos propres dépendances** : Azure Functions prenant en charge NuGet et NPM, vous pouvez utiliser vos bibliothèques préférées.
- La **sécurité intégrée** vous permet de protéger les fonctions déclenchées par HTTP à l’aide de fournisseurs OAuth comme Azure Active Directory, Facebook, Google, Twitter et Microsoft Account.
- **Intégration simplifiée** avec différents [services Azure](/azure/azure-functions/functions-overview#integrations) et offres SaaS (software as a Service).
- La **flexibilité en matière de développement** vous permet de coder vos fonctions directement dans le portail ou de configurer une intégration continue et de déployer votre code par le biais de GitHub, Azure DevOps Services et d’autres outils de développement pris en charge.
- Le runtime Azure Functions est **Open Source** et disponible sur [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
-  **Expérience de développement améliorée**, où les développeurs peuvent coder, tester et déboguer localement à l’aide de leur éditeur préféré ou d’une interface web facile à utiliser, avec supervision à l’aide d’outils intégrés et fonctionnalités DevOps intégrées.
- **Ensemble de langages de programmation et d’options d’hébergement** : développez à l’aide de C#, Node.js, Java, JavaScript ou Python.
- **Modèle de tarification à l’utilisation** : seul le temps passé à exécuter votre code vous est facturé.

**Informations de référence**
- [Portail Azure](https://portal.azure.com)
- [Documentation](/azure/azure-functions/)
- [Guide du développeur Azure Functions](/azure/azure-functions/functions-reference)
- [Démarrages rapides](/azure/azure-functions/functions-create-first-function-vs-code)
- [Exemples](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="app-service"></a>App Service
[Azure App Service](https://azure.microsoft.com/services/app-service/) vous permet de créer et d’héberger des applications web et des API RESTful dans le langage de programmation de votre choix sans gérer l’infrastructure. Il offre une mise à l’échelle automatique et une haute disponibilité, prend en charge à la fois Windows et Linux et permet des déploiements automatisés à partir de GitHub, Azure DevOps ou n’importe quel référentiel Git.

**Fonctionnalités clés**
- **Plusieurs langages et frameworks** : prise en charge d’ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Vous pouvez également exécuter PowerShell et d’autres scripts ou exécutables comme services en arrière-plan.
- **Optimisation DevOps** : configurez l’intégration et le déploiement continus avec Azure DevOps, GitHub, BitBucket, Docker Hub ou Azure Container Registry. Gérez vos applications dans App Service à l’aide d’Azure PowerShell ou de la CLI interplateforme.
- **Mise à l’échelle globale avec une haute disponibilité** : effectuez un scale-up ou un scale-out manuellement ou automatiquement.
- **Connexion aux plateformes SaaS et données locales** : pour choisir parmi plus de 50 connecteurs pour des systèmes d’entreprise tels que SAP, des services SaaS tels que Salesforce et des services Internet tels que Facebook. Accédez aux données locales à l’aide de connexions hybrides et de réseaux virtuels Azure.
- **Sécurité et conformité** : App Service est conforme aux normes ISO, SOC et PCI. Authentifiez les utilisateurs avec Azure Active Directory ou avec une connexion sociale (Google, Facebook, Twitter et Microsoft). Créez des restrictions par adresse IP et gérez les identités de service.
- **Modèles d’application** pour choisir parmi une liste complète de modèles d’application sur la Place de marché Azure, tels que WordPress, Joomla et Drupal.
- L’**intégration Visual Studio** avec les outils dédiés de Visual Studio rationalisent le travail de création, de déploiement et de débogage.

**Informations de référence**
- [Portail Azure](https://portal.azure.com/)
- [Documentation](/azure/app-service/)
- [Démarrages rapides](/azure/app-service/app-service-web-get-started-dotnet)
- [Exemples](/azure/app-service/samples-cli)
- [Tutoriels](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) gère votre environnement Kubernetes hébergé, ce qui vous permet de déployer et de gérer de manière simple et rapide des applications en conteneur sans avoir à maîtriser l’orchestration de conteneurs. Il élimine également la charge des opérations en cours et la maintenance par configuration, la mise à niveau et la mise à l’échelle des ressources à la demande, sans déconnecter vos applications. 

**Fonctionnalités clés**
- **Migrez facilement des applications existantes** vers des conteneurs et exécutez-les dans AKS.
- **Simplifiez le déploiement et la gestion** des applications basées sur des microservices.
- **Sécurisez DevOps pour AKS** afin d’obtenir un bon compromis entre vitesse et sécurité, et fournir du code plus rapidement à l’échelle.
- **Effectuez facilement une mise à l’échelle à l’aide d’AKS et ACI** pour provisionner des pods dans ACI qui démarrent en quelques secondes.
- **Déploiement et gestion des appareils IoT** à la demande.
- **Entraînement de modèle Machine Learning** à l’aide d’outils tels que TensorFlow et KubeFlow.

**Informations de référence**
- [Portail Azure](https://portal.azure.com/)
- [Documentation](/azure/aks/)
- [Démarrages rapides](/azure/aks/kubernetes-walkthrough-portal)
- [Tutoriels](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances (ACI)](https://azure.microsoft.com/services/container-instances/) est une excellente solution pour les scénarios qui peuvent fonctionner dans des conteneurs isolés, y compris les applications basiques, automatiser des tâches et créer des travaux. Développez rapidement des applications sans gérer de machines virtuelles.

**Fonctionnalités clés**
- **Réduction des temps de démarrage**, car ACI peut démarrer des conteneurs dans Azure en quelques secondes, sans qu’il soit nécessaire de configurer ou de gérer des machines virtuelles.
- **Connectivité IP publique et nom DNS personnalisé**.
- **Sécurité de niveau hyperviseur** qui garantit que votre application se retrouve aussi isolée dans un conteneur que dans une machine virtuelle.
- **Tailles personnalisées** pour une utilisation optimale en autorisant les spécifications exactes des cœurs d’unité centrale et de la mémoire. Vous payez pour ce dont vous avez besoin et vous êtes facturé immédiatement, pour pouvoir ajuster vos dépenses selon vos besoins.
- **Stockage persistant** : pour récupérer et conserver l’état, ACI offre un montage direct des partages Azure Files.
- **Conteneurs Linux et Windows** planifiés avec la même API.

**Informations de référence**
- [Portail Azure](https://portal.azure.com/)
- [Documentation](/azure/container-instances/)
- [Démarrages rapides](/azure/container-instances/container-instances-quickstart-portal)
- [Exemples](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Tutoriels](/azure/container-instances/container-instances-tutorial-prepare-app)