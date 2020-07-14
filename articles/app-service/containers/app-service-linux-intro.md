---
title: Exécuter du code sur les conteneurs Linux par défaut
description: Azure App Service peut exécuter votre code sur des conteneurs Linux prédéfinis. Découvrez comment vous pouvez exécuter vos applications web Linux sur Azure.
keywords: azure app service, linux, oss
author: msangapu-msft
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 891e0c18b3f95dca905fbc14b957af773135eeec
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2020
ms.locfileid: "85557899"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Présentation d’Azure App Service sur Linux

[Azure App Service](../overview.md) est une plateforme de calcul complètement managée, optimisée pour l’hébergement de sites et d’applications web. Les clients peuvent utiliser App Service sur Linux pour héberger des applications web en mode natif sur Linux pour les piles d’applications prises en charge.

## <a name="languages"></a>Languages

App Service sur Linux prend en charge de nombreuses images intégrées afin d’augmenter la productivité des développeurs. Les langages pris en charge sont les suivants : Node.js, Java (JRE 8 et JRE 11), PHP, Python, .NET Core et Ruby. Exécutez [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) pour afficher les derniers langages et versions pris en charge. Si le runtime nécessaire à votre application n’est pas pris en charge dans les images intégrées, il existe des instructions sur la façon de [créer votre propre image Docker](tutorial-custom-docker-image.md) en vue de son déploiement sur Web App pour conteneurs.

## <a name="deployments"></a>Déploiements

* FTP
* Git local
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Environnements intermédiaires
* Intégration/livraison continue [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) et DockerHub

## <a name="console-publishing-and-debugging"></a>Console, publication et débogage

* Environnements
* Déploiements
* Console de base
* SSH

## <a name="scaling"></a>Mise à l'échelle

* Les clients peuvent faire monter ou descendre en puissance les applications web en modifiant le niveau de leur [plan App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json).

## <a name="locations"></a>Emplacements

Consultez le [tableau de bord Statut Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Limites

Le portail Azure affiche uniquement les fonctionnalités compatibles avec Web App for Containers. Les fonctionnalités seront visibles sur le portail au fur et à mesure de leur activation.

App Service sur Linux n’est pris en charge qu’avec les plans App Service [Gratuit, De base, Standard, Premium et Isolé](https://azure.microsoft.com/pricing/details/app-service/plans/) et ne dispose pas d’un niveau [Partagé](https://azure.microsoft.com/pricing/details/app-service/plans/). Il n’est pas possible de créer une application web Linux dans un plan App Service qui héberge déjà des applications web autres que Linux.  

Il existe une limitation actuelle pour le même groupe de ressources, qui vous empêche de mélanger les applications Windows et Linux dans la même région.

## <a name="troubleshooting"></a>Dépannage

> [!NOTE]
> La [Supervision Azure (préversion)](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview) comprend une nouvelle fonctionnalité de journalisation intégrée. 
>
>

Lorsque votre application ne démarre pas ou que vous souhaitez vérifier la journalisation à partir de votre application, consultez les journaux d’activité Docker dans le répertoire LogFiles. Vous pouvez accéder à ce répertoire par le biais de votre site SCM ou d’un FTP. Pour journaliser `stdout` et `stderr` à partir de votre conteneur, vous devez activer le **Journal des applications** sous **Journaux App Service**. Le paramètre prend effet immédiatement. App Service détecte le changement et redémarre le conteneur automatiquement.

Vous pouvez accéder au site SCM à partir d’**Outils avancés** dans le menu **Outils de développement**.

![Affichage des journaux d’activité Docker avec Kudu][1]

## <a name="next-steps"></a>Étapes suivantes

Les articles suivants vous aident dans la prise en main d’App Service sur Linux avec des applications web écrites dans une variété de langages :

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.JS](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Application à plusieurs conteneurs](quickstart-multi-container.md)

Pour plus d’informations sur App Service sur Linux, consultez :

* [FAQ pour App Service pour Linux](app-service-linux-faq.md)
* [Prise en charge SSH pour App Service sur Linux](app-service-linux-ssh-support.md)
* [Configurer des environnements intermédiaires dans App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Déploiement continu Docker Hub](app-service-linux-ci-cd.md)

Vous pouvez poser des questions et signaler vos préoccupations sur [notre forum](https://docs.microsoft.com/answers/topics/azure-webapps.html).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
