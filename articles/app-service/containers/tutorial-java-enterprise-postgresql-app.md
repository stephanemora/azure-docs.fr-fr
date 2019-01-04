---
title: Créer une application web Java Enterprise sur Linux - Azure App Service | Microsoft Docs
description: Découvrez comment faire fonctionner une application Java entreprise dans Wildfly avec Azure App Service sur Linux.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 3a668783e8257ef9074d12b30ff0afc3a40325f4
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539719"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Didacticiel : Créer une application web Java EE et Postgres dans Azure

Ce tutoriel vous explique comment créer une application web Java EE (Enterprise Edition) sur Azure App Service et comment la connecter à une base de données Postgres. À la fin de ce tutoriel, une application [WildFly](https://www.wildfly.org/about/) stocke les données dans [Azure Database pour Postgres](https://azure.microsoft.com/services/postgresql/) qui s’exécute sur [Azure App Service pour Linux](app-service-linux-intro.md).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Déployer une application Java EE dans Azure avec Maven
> * Créez une base de données Postgres dans Azure
> * Configurer le serveur WildFly pour utiliser Postgres
> * Mise à jour et redéploiement de l’application
> * Exécuter des tests unitaires sur WildFly

## <a name="prerequisites"></a>Prérequis

1. [Téléchargement et installation de Git](https://git-scm.com/)
2. [Téléchargement et installation de Maven 3](https://maven.apache.org/install.html)
3. [Téléchargement et installation de l’interface Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Cloner et modifier l’exemple d’application

Dans cette étape, vous clonez l’exemple d’application et configurez pom.xml, le fichier POM (Project Object Model) Maven, pour le déploiement.

### <a name="clone-the-sample"></a>Clonage de l’exemple

Dans la fenêtre de terminal, accédez à un répertoire de travail et clonez [l’exemple de dépôt](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Mettre à jour le fichier POM Maven

Mettez à jour le fichier POM Maven avec le nom et le groupe de ressources de votre choix pour votre instance App Service. Ces valeurs sont injectées dans le plug-in Azure qui se trouve plus bas dans le fichier _pom.xml_. Il n’est pas nécessaire de créer au préalable l’instance ou le plan App Service. Le plug-in Maven crée le groupe de ressources et l’instance App Service s’ils n’existent pas déjà.

Vous pouvez faire défiler vers le bas jusqu’à la section `<plugins>` du fichier _pom.xml_ pour inspecter le plug-in Azure. La section de configuration `<plugin>` du fichier _pom.xml_ pour azure-webapp-maven-plugin doit inclure la configuration suivante :

```xml
      <!--*************************************************-->
      <!-- Deploy to WildFly in App Service Linux           -->
      <!--*************************************************-->
 
      <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.5.0</version>
        <configuration>
 
          <!-- Web App information -->
          <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
          <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
          <appName>${WEBAPP_NAME}</appName>
          <region>${REGION}</region>
 
          <!-- Java Runtime Stack for Web App on Linux-->
          <linuxRuntime>wildfly 14-jre8</linuxRuntime>
 
        </configuration>
      </plugin>
```

Remplacez les espaces réservés par les noms des ressources de votre choix :
```xml
<azure.plugin.appname>YOUR_APP_NAME</azure.plugin.appname>
<azure.plugin.resourcegroup>YOUR_RESOURCE_GROUP</azure.plugin.resourcegroup>
```


## <a name="build-and-deploy-the-application"></a>Générer et déployer l’application

Nous allons à présent utiliser Maven pour créer notre application et la déployer dans App Service.

### <a name="build-the-war-file"></a>Générer le fichier .war

Le fichier POM de ce projet est configuré pour créer le package de l’application dans un fichier WAR (Web Archive). Créez l’application avec Maven :

```bash
mvn clean install -DskipTests
```

Les cas de test de cette application sont conçus pour être exécutés lorsque l’application est déployée sur WildFly. Nous allons ignorer ces tests pour effectuer une création localement, et vous procéderez ensuite à leur exécution après le déploiement de l’application sur App Service.

### <a name="deploy-to-app-service"></a>Déployer dans App Service

Le fichier WAR étant prêt, nous pouvons utiliser le plug-in Azure et déployer sur App Service :

```bash
mvn azure-webapp:deploy
```

Une fois le déploiement terminé, passez à l’étape suivante.

### <a name="create-a-record"></a>Créer un enregistrement

Ouvrez un navigateur et accédez à `https://<your_app_name>.azurewebsites.net/`. Félicitations, vous avez déployé votre application Java EE sur Azure App Service !

À ce stade, l’application utilise une base de données H2 en mémoire. Cliquez sur Administrateur dans la barre de navigation et créez une catégorie. Si vous redémarrez votre instance App Service, l’enregistrement effectué dans votre base de données en mémoire sera perdu. Dans les étapes suivantes, vous résolvez ce problème en provisionnant une base de données Postgres sur Azure, et configurez WildFly pour l’utiliser.

![Emplacement du bouton Administrateur](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Provisionner une base de données Postgres

Pour provisionner un serveur de base de données Postgres, ouvrez un terminal et exécutez la commande suivante avec les valeurs de votre choix pour le nom du serveur, le nom d’utilisateur, le mot de passe et l’emplacement. Utilisez le même groupe de ressources que celui dans lequel se trouve votre instance App Service. Notez votre mot de passe pour une utilisation ultérieure !

```bash
az postgres server create -n <desired-name> -g <same-resource-group> --sku-name GP_Gen4_2 -u <desired-username> -p <desired-password> -l <location>
```

Accédez au portail et recherchez votre base de données Postgres. Lorsque le panneau est actif, copiez les valeurs du Nom du serveur et du Nom de connexion de l’administrateur du serveur, car vous en aurez besoin plus tard.

### <a name="allow-access-to-azure-services"></a>Autoriser l’accès aux services Azure

Dans le panneau **Sécurité de la connexion** d’Azure Database, basculez le bouton Autoriser l’accès aux services Azure sur la position **Activé**.

![Autorisation de l’accès aux services Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Mettre à jour votre application Java pour Postgres

Nous allons maintenant apporter quelques modifications à l’application Java pour qu’elle puisse utiliser notre base de données Postgres.

### <a name="add-postgres-credentials-to-the-pom"></a>Ajouter des informations d’identification Postgres au fichier POM

Dans le fichier _pom.xml_, remplacez les valeurs d’espace réservé par le nom du serveur Postgres, le nom de connexion de l’administrateur et le mot de passe. Ces valeurs vont être injectées en tant que variables d’environnement dans votre instance App Service lors du redéploiement de l’application.

```xml
<azure.plugin.postgres-server-name>SERVER_NAME</azure.plugin.postgres-server-name>
<azure.plugin.postgres-username>USERNAME@FIRST_PART_OF_SERVER_NAME</azure.plugin.postgres-username>
<azure.plugin.postgres-password>PASSWORD</azure.plugin.postgres-password>
```

### <a name="update-the-java-transaction-api"></a>Mettre à jour l’API Java Transaction

Nous devons ensuite modifier la configuration de l’API Java Transaction, afin que l’application Java communique avec Postgres plutôt qu’avec la base de données H2 en mémoire qui a été utilisée précédemment. Ouvrez _src/main/resources/META-INF/persistence.xml_ dans un éditeur. Remplacez la valeur pour `<jta-data-source>` avec `java:jboss/datasources/postgresDS`. Votre fichier XML de l’API Java Transaction doit à présent contenir ce paramètre :

```xml
...
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
...
```

## <a name="configure-the-wildfly-application-server"></a>Configurer le serveur d’applications WildFly

Avant de déployer notre application reconfigurée, nous devons mettre à jour le serveur d’applications WildFly avec le module Postgres et ses dépendances. Pour configurer le serveur, nous avons besoin des quatre fichiers présents dans le répertoire `wildfly_config/` :

- **postgresql-42.2.5.jar** : fichier JAR servant de pilote JDBC pour Postgres. Pour plus d’informations, visitez le [site officiel](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml** : fichier XML déclarant un nom pour le module Postgres (org.postgres). Il spécifie également les ressources et les dépendances nécessaires au fonctionnement du module.
- **jboss_cli_commands.cl** : fichier contenant les commandes de configuration à exécuter par l’interface CLI JBoss. Les commandes ajoutent le module Postgres au serveur d’applications WildFly, elles fournissent les informations d’identification, déclarent un nom JNDI, définissent le seuil de délai d’expiration, etc. Si vous ne connaissez pas l’interface CLI JBoss, référez-vous à la [documentation officielle](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh** : script shell s’exécutant chaque fois que votre instance App Service est démarrée. Le script n’assure qu’une fonction : la redirection des commandes dans `jboss_cli_commands.cli` vers l’interface CLI JBoss.

Nous vous suggérons fortement de lire le contenu de ces fichiers, en particulier _jboss_cli_commands.cli_.

### <a name="ftp-the-configuration-files"></a>Transférer via FTP les fichiers de configuration

Nous devons transférer via FTP le contenu de `wildfly_config/` sur notre instance App Service. Pour obtenir vos informations d’identification FTP, cliquez sur le bouton **Obtenir le profil de publication** qui est situé dans le panneau App Service du portail Azure. Vos nom d’utilisateur et mot de passe FTP se trouvent dans le document XML téléchargé. Pour plus d’informations sur le profil de publication, consultez [ce document](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

À l’aide de l’outil FTP de votre choix, transférez les quatre fichiers de `wildfly_config/` vers `/home/site/deployments/tools/`. (Notez que vous ne devez pas transférer le répertoire, seulement les fichiers.)


### <a name="finalize-app-service"></a>Finaliser App Service

Dans le panneau App Service, accédez au volet Paramètres d’application. Sous Runtime, définissez le champ Fichier de démarrage sur `/home/site/deployments/tools/startup_script.sh`. De cette façon, vous êtes sûr que le script shell est exécuté après la création de l’instance App Service, et avant le démarrage du serveur WildFly.

Pour terminer, redémarrez votre instance App Service. Le bouton se trouve dans le panneau Vue d’ensemble.

## <a name="redeploy-the-application"></a>Redéployer l’application

Dans une fenêtre de terminal, regénérez et redéployez votre application.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Félicitations ! Votre application utilise désormais une base de données Postgres, et tous les enregistrements créés dans l’application sont stockés dans Postgres plutôt que dans la base de données H3 en mémoire qui a été utilisée précédemment. Pour le vérifier, vous pouvez créer un enregistrement et redémarrer votre instance App Service. Les enregistrements seront toujours là au redémarrage de votre application.

## <a name="clean-up"></a>Nettoyer

Si vous n’avez pas besoin de ces ressources pour un autre tutoriel (voir Étapes suivantes), vous pouvez les supprimer en exécutant la commande suivante :

```bash
az group delete --name <your_resource_group> 
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’une application Java EE déployée sur App Service, consultez le [Guide du développeur Java Enterprise](https://aka.ms/wildfly-quickstart) pour obtenir plus d’informations sur la configuration des services, la résolution de problèmes et la mise à l’échelle de votre application.