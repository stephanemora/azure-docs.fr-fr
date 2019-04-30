---
title: Configurer Java APM et des outils de supervision sous Linux - Azure App Service
description: Découvrez comment envoyer des journaux d’activité et des métriques pour vos applications Java s’exécutant sur App Service Linux aux fournisseurs APM NewRelic et Dynamics
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 03/21/2019
ms.author: astay;routlaw
ms.custom: seodec18
ms.openlocfilehash: e6a22258266bda18c9ff79590d88e70d512f6c77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60849963"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Procédure : Outils de supervision des performances d’application avec les applications Java sur Azure App Service sur Linux

Cet article explique comment connecter des applications Java déployées sur Azure App Service sur Linux avec les plateformes de supervision des performances d’application (APM) NewRelic et AppDynamics.

## <a name="configure-new-relic"></a>Configurer New Relic

1. Créez un compte NewRelic sur [NewRelic.com](https://newrelic.com/signup)
2. Téléchargez l’agent Java à partir de NewRelic, son nom doit ressembler à `newrelic-java-x.x.x.zip`.
3. Copiez votre clé de licence, vous en aurez besoin pour configurer l’agent par la suite.
4. [Connectez-vous avec SSH à votre instance App Service](/azure/app-service/containers/app-service-linux-ssh-support) et créez un répertoire `/home/site/wwwroot/apm`.
5. Chargez les fichiers de l’agent Java de NewRelic décompressés dans un répertoire sous `/home/site/wwwroot/apm`. Les fichiers de votre agent doivent être dans `/home/site/wwwroot/apm/newrelic`.
6. Modifiez le fichier YAML dans `/home/site/wwwroot/apm/newrelic/newrelic.yml` et remplacez la valeur de la licence d’espace réservé par votre propre clé de licence.
7. Dans le portail Azure, accédez à votre application dans App Service et créez un paramètre d’application.
    - Si votre application utilise **Java SE**, créez une variable d’environnement nommée `JAVA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Si vous utilisez **Tomcat**, créez une variable d’environnement nommée `CATALINA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Si vous utilisez **WildFly**, consultez la documentation de New Relic [ici](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) pour obtenir des conseils sur l’installation de l’agent Java et la configuration de JBoss.
    - Si vous avez déjà une variable d’environnement pour `JAVA_OPTS` ou `CATALINA_OPTS`, ajoutez l’option `javaagent` à la fin de la valeur actuelle.

## <a name="configure-appdynamics"></a>Configurer AppDynamics

1. Créez un compte AppDynamics sur [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Téléchargez l’agent Java à partir du site web AppDynamics, le nom de fichier ressemble à `AppServerAgent-x.x.x.xxxxx.zip`
1. [Connectez-vous avec SSH à votre instance App Service](/azure/app-service/containers/app-service-linux-ssh-support) et créez un répertoire `/home/site/wwwroot/apm`.
1. Chargez les fichiers de l’agent Java dans un répertoire sous `/home/site/wwwroot/apm`. Les fichiers de votre agent doivent être dans `/home/site/wwwroot/apm/appdynamics`.
1. Dans le portail Azure, accédez à votre application dans App Service et créez un paramètre d’application.
    - Si vous utilisez **Java SE**, créez une variable d’environnement nommée `JAVA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` où `<YOUR_SITE_NAME>` est votre nom App Service.
    - Si vous utilisez **Tomcat**, créez une variable d’environnement nommée `CATALINA_OPTS` avec la valeur `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` où `<YOUR_SITE_NAME>` est votre nom App Service.
    - Si vous utilisez **WildFly**, consultez la documentation AppDynamics [ici](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) pour obtenir des conseils sur l’installation de l’agent Java et la configuration de JBoss.
