---
title: Configurer Java APM et des outils de supervision avec Azure App Service sur Linux
description: Découvrez comment envoyer des journaux et des métriques pour vos applications Java s’exécutant sur App Service Linux aux fournisseurs APM NewRelic et Dynamics
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 11/27/2018
ms.author: astay;routlaw
ms.openlocfilehash: 06ae71fea1b85a74d87588d2635038c64c8540cc
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52577000"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Procédure : Outils de supervision des performances d’application avec les applications Java sur Azure App Service sur Linux

Cet article explique comment connecter des applications Java déployées sur Azure App Service sur Linux avec les plateformes de supervision des performances d’application (APM) NewRelic et AppDynamics.

## <a name="configure-new-relic"></a>Configurer New Relic
1. Créez un compte NewRelic sur [NewRelic.com](https://newrelic.com/signup)
1. Téléchargez l’agent Java à partir de NewRelic, son nom doit ressembler à `newrelic-java-x.x.x.zip`.
1. Copiez votre clé de licence, vous en aurez besoin pour configurer l’agent par la suite.
1. [Connectez-vous avec SSH à votre instance App Service](/azure/app-service/containers/app-service-linux-ssh-support) et créez un répertoire `/home/site/wwwroot/apm`. 
1. Chargez les fichiers de l’agent Java de NewRelic décompressés dans un répertoire sous `/home/site/wwwroot/apm`. Les fichiers de votre agent doivent être dans `/home/site/wwwroot/apm/newrelic`.
1. Modifiez le fichier YAML dans `/home/site/wwwroot/apm/newrelic/newrelic.yml` et remplacez la valeur de la licence d’espace réservé par votre propre clé de licence.
1. Dans le portail Azure, accédez à votre application dans App Service et créez un paramètre d’application.
    - Si votre application utilise **Java SE**, créez une variable d’environnement nommée `JAVA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Si vous utilisez **Tomcat**, créez une variable d’environnement nommée `CATALINA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Si vous avez déjà une variable d’environnement pour `JAVA_OPTS` ou `CATALINA_OPTS`, ajoutez l’option `javaagent` à la fin de la valeur actuelle.

## <a name="configure-appdynamics"></a>Configurer AppDynamics
1. Créez un compte AppDynamics sur [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Téléchargez l’agent Java à partir du site web AppDynamics, le nom de fichier ressemble à `AppServerAgent-x.x.x.xxxxx.zip`
1. [Connectez-vous avec SSH à votre instance App Service](/azure/app-service/containers/app-service-linux-ssh-support) et créez un répertoire `/home/site/wwwroot/apm`. 
1. Chargez les fichiers de l’agent Java dans un répertoire sous `/home/site/wwwroot/apm`. Les fichiers de votre agent doivent être dans `/home/site/wwwroot/apm/appdynamics`.
1. Dans le portail Azure, accédez à votre application dans App Service et créez un paramètre d’application.
    - Si vous utilisez **Java SE**, créez une variable d’environnement nommée `JAVA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` où `<YOUR_SITE_NAME>` est votre nom App Service.
    - Si vous utilisez **Tomcat**, créez une variable d’environnement nommée `CATALINA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` où `<YOUR_SITE_NAME>` est votre nom App Service.
