---
title: Prise en charge de Java Enterprise sous Linux – Azure App Service | Microsoft Docs
description: Guide du développeur pour déployer des applications Java Enterprise avec Azure App Service sur Linux.
keywords: azure app service, application web, linux, oss, java, wildfly, enterprise
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 408141650a11a81f0c6000c6e7927af8333e2afe
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548474"
---
# <a name="java-enterprise-guide-for-app-service-on-linux"></a>Guide de Java Enterprise pour App Service sur Linux

Azure App Service sur Linux permet aux développeurs Java de générer, déployer et mettre à l’échelle des applications JEE (Java Enterprise) sur un service Linux entièrement managé.  L’environnement d’exécution Java Enterprise sous-jacent est le serveur d’applications open source [WildFly](https://wildfly.org/).

Ce guide fournit des concepts et des instructions clés aux développeurs Java Enterprise qui utilisent App Service pour Linux. Si vous n’avez jamais déployé des applications Java Enterprise avec Azure App Service pour Linux, il est recommandé de suivre d’abord le [Démarrage rapide de Java](quickstart-java.md). Les questions sur App Service pour Linux qui ne sont pas spécifiques à Java Enterprise sont traitées dans le [Guide du développeur Java](app-service-linux-java.md) et dans les [Questions fréquentes (FAQ) sur App Service Linux](app-service-linux-faq.md).

## <a name="scale-with-app-service"></a>Mettre à l’échelle avec App Service 

Le serveur d’applications WildFly s’exécutant dans App Service sur Linux s’exécute en mode autonome, et non pas dans une configuration de domaine. Quand vous effectuez un scale-out du plan App Service, chaque instance WildFly est configurée comme un serveur autonome.

 Mettez à l’échelle votre application verticalement ou horizontalement, avec des [règles de mise à l’échelle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started?toc=%2Fazure%2Fapp-service%2Fcontainers%2Ftoc.json) et en [augmentant le nombre de vos instances](https://docs.microsoft.com/azure/app-service/web-sites-scale?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json). 

## <a name="customize-application-server-configuration"></a>Personnaliser la configuration du serveur d’applications

Les instances d’application web sont sans état, chaque nouvelle instance démarrée doit donc être configurée au démarrage pour prendre en charge la configuration Wildfly nécessaire par application.
Vous pouvez écrire un script Bash de démarrage pour appeler l’interface CLI WildFly afin de :

- Configurer les sources de données
- Configurer les fournisseurs de messagerie
- Ajouter d’autres modules et dépendances à la configuration de serveur WildFly.

 Le script s’exécute une fois que WildFly est opérationnel, mais avant le démarrage de l’application. Le script doit utiliser l’[interface CLI JBOSS](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) appelée depuis `/opt/jboss/wildfly/bin/jboss-cli.sh` pour configurer le serveur d’applications avec une certaine configuration ou avec les modifications nécessaires après le démarrage du serveur. 

N’utilisez pas le mode interactif de l’interface CLI pour configurer WildFly. Au lieu de cela, vous pouvez fournir un script de commandes à l’interface CLI JBoss avec la commande `--file`, par exemple :

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Chargez le script de démarrage sur `/home/site/deployments/tools` dans votre instance App Service. Consultez [ce document](/azure/app-service/deploy-configure-credentials#userscope) pour obtenir des instructions sur l’obtention de vos informations d’identification FTP. 

Définissez le champ **Script de démarrage** champ dans le portail Azure sur l’emplacement de votre script shell de démarrage, par exemple `/home/site/deployments/tools/your-startup-script.sh`.

Fournissez les [paramètres d’application](/azure/app-service/web-sites-configure#application-settings) dans la configuration d’application pour passer les variables d’environnement à utiliser dans le script. Les paramètres d’application conservent les chaînes de connexion et les autres secrets nécessaires pour configurer votre application en dehors de la gestion de version.

## <a name="modules-and-dependencies"></a>Modules et dépendances

Pour installer des modules et leurs dépendances dans le classpath WildFly via l’interface CLI JBoss, vous devez créer les fichiers suivants dans leur propre répertoire.  Certains modules et certaines dépendances peuvent nécessiter une configuration supplémentaire, comme le nommage JNDI ou d’autres configurations spécifiques à l’API : cette liste est donc un ensemble minimal de ce vous devez configurer dans la plupart des cas pour une dépendance.

- [Descripteur de module XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Ce fichier XML définit le nom, les attributs et les dépendances de votre module. Cet [exemple de fichier module.xml](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) définit un module Postgres, sa dépendance JDBC du fichier JAR et d’autres dépendances nécessaires du module.
- Dépendances de fichier JAR nécessaires pour votre module.
- Script avec vos commandes d’interface CLI JBoss pour configurer le nouveau module. Ce fichier contient vos commandes à exécuter par l’interface CLI JBoss pour configurer le serveur de façon à ce qu’il utilise la dépendance. Pour une documentation sur les commandes pour ajouter des modules, des sources de données et des fournisseurs de messagerie, reportez-vous à [ce document](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
-  Script de démarrage Bash pour appeler l’interface CLI JBoss et exécuter le script de l’étape précédente. Ce fichier est exécuté quand votre instance App Service est redémarrée ou quand de nouvelles instances sont provisionnées lors d’un scale-out.  C’est dans ce script de démarrage que vous pouvez effectuer les autres configurations pour votre application, car les commandes JBoss sont passées à l’interface CLI JBoss. Au minimum, ce fichier peut être une commande unique pour passer votre script de commandes d’interface CLI JBoss à l’interface CLI JBoss : 
   
```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli` 
``` 

Une fois que vous avez les fichiers et le contenu pour votre module, suivez les étapes ci-dessous pour ajouter le module au serveur d’applications WildFly. 

1. Chargez vos fichiers via FTP dans `/home/site/deployments/tools` sur votre instance App Service. Consultez ce document pour obtenir des instructions sur l’obtention de vos informations d’identification FTP. 
2. Dans le panneau Paramètres de l’application du portail Azure, définissez le champ « Script de démarrage » sur l’emplacement de votre script shell de démarrage, par exemple `/home/site/deployments/tools/your-startup-script.sh`.
3. Redémarrez votre instance App Service en cliquant sur le bouton **Redémarrer** situé dans la section **Vue d’ensemble** du portail ou avec l’interface CLI Azure.

## <a name="data-sources"></a>Sources de données

Pour configurer une connexion de source de données WildFly, suivez le même processus que ci-dessus dans la section Installation de modules et de dépendances. Vous pouvez effectuer les mêmes étapes pour n’importe quel service Azure Database.

1. Téléchargez le pilote JDBC pour votre base de données. Pour des raisons pratiques, voici les pilotes pour [Postgres](https://jdbc.postgresql.org/download.html) et [MySQL](https://dev.mysql.com/downloads/connector/j/). Décompressez le téléchargement pour obtenir le fichier .jar.
2. Suivez le plan des étapes de « Modules et dépendances » pour créer et charger votre descripteur de module XML, le script d’interface CLI JBoss, le script de démarrage et la dépendance .jar JDBC.


Des informations plus détaillées sur la configuration de WildFly avec [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) et [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) sont disponibles. Vous pouvez utiliser ces instructions personnalisées ainsi que l’approche généralisée ci-dessus pour ajouter des définitions de source de données à votre serveur.

## <a name="messaging-providers"></a>Fournisseurs de messagerie

Pour activer des beans pilotés par les messages avec Service Bus comme mécanisme de messagerie :

1. Utilisez la [bibliothèque de messagerie Apache QPId JMS](https://qpid.apache.org/proton/index.html). Incluez cette dépendance dans votre fichier pom.xml (ou dans un autre fichier de build) pour l’application.

2.  Créez des [ressources Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Créez un espace de noms Azure Service Bus et une file d’attente dans cet espace de noms ainsi qu’une stratégie d’accès partagé avec des fonctionnalités d’envoi et de réception.

3. Passez la clé de la stratégie d’accès partagé à votre code, en encodant la clé principale de votre stratégie dans une URL ou en [utilisant le SDK Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Suivez les étapes décrites dans la section Installation de modules et de dépendances avec le descripteur XML de votre module, les dépendances .jar, les commandes CLI JBoss et le script de démarrage pour le fournisseur JMS. En plus des quatre fichiers, vous devez aussi créer un fichier XML qui définit le nom JNDI pour la file d’attente et la rubrique JMS. Pour les fichiers de configuration de référence, consultez [ce dépôt](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig).


## <a name="configure-session-management-caching"></a>Configurer la mise en cache de la gestion de session

Par défaut, App Service sur Linux utilise des cookies de session d’affinités pour garantir que les demandes des clients ayant des sessions existantes sont routées vers la même instance de votre application. Ce comportement par défaut ne nécessite aucune configuration, mais il présente certaines limitations :

- Si une instance d’application est redémarrée ou fait l’objet d’un scale-down, l’état de la session utilisateur dans le serveur d’applications est perdu.
- Si les applications ont des paramètres de délai d’expiration de session avec une durée longue ou bien un nombre fixe d’utilisateurs, la réception de la charge par les instances mises à l’échelle automatiquement peut prendre un certain temps, car seules les nouvelles sessions sont routées vers les instances nouvellement démarrées.

Vous pouvez configurer WildFly de façon à utiliser un magasin de sessions externe comme le [Cache Azure pour Redis](/azure/azure-cache-for-redis/). Vous devez alors [désactiver la configuration de l’affinité d’instance ARR existante](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) pour arrêter le routage de session basé sur les cookies et autoriser le magasin de sessions WildFly configuré pour un fonctionnement sans interférence.

## <a name="enable-web-sockets"></a>Activer les sockets web

Par défaut, les sockets web sont activés sur App Service. Pour bien démarrer avec WebSockets dans votre application, reportez-vous à [ce démarrage rapide](https://github.com/wildfly/quickstart/tree/master/websocket-hello).

## <a name="logs-and-troubleshooting"></a>Journaux et résolution des problèmes

App Service fournit des outils pour vous aider à résoudre les problèmes rencontrés avec votre application.

-   Activez la journalisation en cliquant sur **Journaux de diagnostic** dans le volet de navigation gauche. Cliquez sur **Système de fichiers** pour définir votre quota de stockage et la période de conservation, et enregistrez vos modifications. Vous pouvez trouver ces journaux sous `/home/LogFiles/`.
-   [Utilisez SSH pour vous connecter à l’instance d’application](app-service-linux-ssh-support.md) et afficher les journaux des applications en cours d’exécution.
-   Vérifiez les journaux de diagnostic dans le panneau **Journaux de diagnostic** du portail ou avec la commande Azure CLI : ` az webapp log tail --name <your-app-name> --resource-group <your-apps-resource-group> `
