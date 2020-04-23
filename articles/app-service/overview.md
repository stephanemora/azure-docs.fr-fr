---
title: Vue d’ensemble
description: Découvrez comment Azure App Service vous aide à développer et héberger des applications web.
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 01/04/2017
ms.custom: mvc, seodec18
ms.openlocfilehash: f8449c516bce3f3ab949e88808f73c7240995f99
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535636"
---
# <a name="app-service-overview"></a>Vue d'ensemble d'App Service

*Azure App Service* est un service HTTP pour l’hébergement d’applications web, d’API REST et de backends mobiles. Vous pouvez développer dans votre langage préféré, par exemple .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python. Les applications s’exécutent et sont mises à l’échelle facilement dans les environnements Windows et Linux. Pour les environnements Linux, consultez [Présentation d’Azure App Service sur Linux](containers/app-service-linux-intro.md). 

App Service ajoute la puissance de Microsoft Azure à votre application, notamment la sécurité, l’équilibrage de charge, la mise à l’échelle automatique et la gestion automatisée. Vous pouvez également bénéficier de ses fonctionnalités DevOps, notamment le déploiement continu depuis Azure DevOps, GitHub, Docker Hub et d’autres sources, la gestion des packages, les environnements intermédiaires, le domaine personnalisé et les certificats TLS/SSL. 

Avec App Service, vous payez pour les ressources de calcul Azure que vous utilisez. Les ressources de calcul que vous utilisez sont déterminées par le _plan App Service_ sur lequel vous exécutez vos applications. Pour plus d’informations, consultez [Vue d’ensemble des plans Azure App Service](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Pourquoi utiliser App Service ?

Voici quelques-unes des principales fonctionnalités d’App Service :

* **Plusieurs langages et frameworks** : App Service offre une prise en charge de première classe pour ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Vous pouvez également exécuter [PowerShell et d’autres scripts ou exécutables](webjobs-create.md) comme services en arrière-plan.
* **Optimisation DevOps** : configurez [l’intégration et le déploiement continus](deploy-continuous-deployment.md) avec Azure DevOps, GitHub, BitBucket, Docker Hub ou Azure Container Registry. Assurez la promotion des mises à jour par le biais des [environnements de test et intermédiaires](deploy-staging-slots.md). Gérez vos applications dans App Service à l’aide d’[Azure PowerShell](/powershell/azureps-cmdlets-docs) ou de la [CLI interplateforme](/cli/azure/install-azure-cli).
* **Mise à l’échelle globale avec une haute disponibilité** : effectuez des [montées en puissance](manage-scale-up.md) ou [augmentez la taille des instances](../monitoring-and-diagnostics/insights-how-to-scale.md) manuellement ou automatiquement. Hébergez vos applications n’importe où dans l’infrastructure mondiale des centres de données de Microsoft, et bénéficiez de garanties sur la haute disponibilité du  [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) App Service.
* **Connexion aux plateformes SaaS et données locales** : choisissez parmi plus de 50 [connecteurs](../connectors/apis-list.md) pour des systèmes d’entreprise tels que SAP, des services SaaS tels que Salesforce et des services Internet tels que Facebook. Accédez aux données locales à l’aide de [connexions hybrides](app-service-hybrid-connections.md) et de [réseaux virtuels Azure](web-sites-integrate-with-vnet.md).
* **Sécurité et conformité** : App Service est [conforme aux normes ISO, SOC et PCI](https://www.microsoft.com/en-us/trustcenter). Authentifiez les utilisateurs avec [Azure Active Directory](configure-authentication-provider-aad.md) ou avec une connexion sociale ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) et [ Microsoft](configure-authentication-provider-microsoft.md)). Créez des [restrictions par adresse IP](app-service-ip-restrictions.md) et [gérez les identités de service](overview-managed-identity.md).
* **Modèles d’application** : faites votre choix parmi une liste complète de modèles d’application dans [Place de marché Azure](https://azure.microsoft.com/marketplace/), tels que WordPress, Joomla et Drupal.
* **Intégration Visual Studio** : les outils dédiés de Visual Studio rationalisent le travail de création, de déploiement et de débogage.
* **API et fonctionnalités mobiles** : App Service fournit une prise en charge CORS clés en main pour les scénarios API RESTful et simplifie les scénarios d’application mobile grâce à l’authentification, la synchronisation des données hors connexion, des notifications push et bien plus encore.
* **Code sans serveur** : exécutez un extrait de code ou un script à la demande sans avoir à configurer ou gérer explicitement l’infrastructure, et ne payez que pour le temps de calcul que votre code utilise réellement (voir [Azure Functions](/azure/azure-functions/)).

En plus d’App Service, Azure offre d’autres services qui peuvent être utilisés pour l’hébergement de sites et d’applications web. Pour la plupart des scénarios, App Service est le meilleur choix.  Pour l’architecture microservice, utilisez [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Si vous avez besoin de contrôler davantage les machines virtuelles sur lesquelles votre code s’exécute, utilisez plutôt [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Pour plus d’informations sur le choix entre ces services Azure, consultez [Comparaison entre Azure App Service, Virtual Machines, Service Fabric et Cloud Services](overview-compare.md).

## <a name="next-steps"></a>Étapes suivantes

Créez votre première application web.

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (sous Linux)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.JS](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python (sous Linux)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
