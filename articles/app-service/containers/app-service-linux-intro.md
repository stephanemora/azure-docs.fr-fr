---
title: Présentation d'App Service sous Linux - Azure | Microsoft Docs
description: Découvrez Azure App Service sur Linux.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 6bca1b067f5ec667e8b5da92a182a5618582b2f3
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617442"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Présentation d’Azure App Service sur Linux

[Azure App Service](../overview.md) est une plateforme de calcul complètement managée, optimisée pour l’hébergement de sites et d’applications web. Les clients peuvent utiliser App Service sur Linux pour héberger des applications web en mode natif sur Linux pour les piles d’applications prises en charge. La section [Langues](#languages) liste les piles d’applications actuellement prises en charge.

## <a name="languages"></a>Languages

App Service sur Linux prend en charge de nombreuses images intégrées afin d’augmenter la productivité des développeurs. Si le runtime nécessaire à votre application n’est pas pris en charge dans les images intégrées, il existe des instructions sur la façon de [créer votre propre image Docker](tutorial-custom-docker-image.md) en vue de son déploiement sur Web App pour conteneurs.

| Langage | Versions prises en charge |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 8.12, 9.4, 10.1, 10.10, 10.14 |
| Java * | Tomcat 8.5, 9.0, Java SE, WildFly 14 (tous exécutant JRE 8) |
| PHP | 5.6, 7.0, 7.2, 7.3 |
| Python | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1, 2.2 |
| Ruby | 2.3, 2.4, 2.5, 2.6 |

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

App Service sur Linux n’est pris en charge que par les plans App Service [Gratuit, De base, Standard et Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) et ne dispose pas d’un niveau [Partagé](https://azure.microsoft.com/pricing/details/app-service/plans/). Il n’est pas possible de créer une application web Linux dans un plan App Service qui héberge déjà des applications web autres que Linux.  

Il existe une limitation actuelle pour le même groupe de ressources, qui vous empêche de mélanger les applications Windows et Linux dans la même région.

## <a name="troubleshooting"></a>Résolution de problèmes

Lorsque votre application ne démarre pas ou que vous souhaitez vérifier la journalisation à partir de votre application, consultez les journaux d’activité Docker dans le répertoire LogFiles. Vous pouvez accéder à ce répertoire par le biais de votre site SCM ou d’un FTP. Pour journaliser `stdout` et `stderr` à partir de votre conteneur, vous devez activer **Journalisation de conteneur Docker** sous **Journaux App Service**. Le paramètre prend effet immédiatement. App Service détecte le changement et redémarre le conteneur automatiquement.

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

Vous pouvez poser des questions et signaler vos préoccupations sur [notre forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
