---
title: 'Didacticiel : Application Java Enterprise sur Linux'
description: Découvrez comment faire fonctionner une application Java Enterprise dans Wildfly sur Azure App Service sur Linux en établissant une connexion à une base de données PostgreSQL dans Azure.
author: JasonFreeberg
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 84f22d52e9a92707a26a4e64f194e82cca87757d
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687451"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Didacticiel : Créer une application web Java EE et Postgres dans Azure

Ce tutoriel vous explique comment créer une application web Java EE (Enterprise Edition) sur Azure App Service et comment la connecter à une base de données Postgres. À la fin de ce tutoriel, une application [WildFly](https://www.wildfly.org/about/) stocke les données dans [Azure Database pour Postgres](https://azure.microsoft.com/services/postgresql/) qui s’exécute sur [Azure App Service sur Linux](app-service-linux-intro.md).

Ce tutoriel vous montre comment effectuer les opérations suivantes :
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

Dans cette étape, vous clonez l’exemple d’application et configurez *pom.xml*, le fichier POM (Project Object Model) Maven, pour le déploiement.

### <a name="clone-the-sample"></a>Clonage de l’exemple

Dans la fenêtre de terminal, accédez à un répertoire de travail et clonez [l’exemple de dépôt](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Mettre à jour le fichier POM Maven

Mettez à jour le plug-in Maven Azure avec le nom et le groupe de ressources de votre choix pour votre instance App Service. Il n’est pas nécessaire de créer au préalable l’instance ou le plan App Service. Le plug-in Maven crée le groupe de ressources et l’instance App Service s’ils n’existent pas déjà.

Vous pouvez faire défiler vers le bas jusqu’à la section `<plugins>` du fichier *pom.xml* (ligne 200) pour apporter les modifications.

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```

Remplacez `YOUR_APP_NAME` et `YOUR_RESOURCE_GROUP` par les noms de votre instance App Service et de votre groupe de ressources.

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

Pour configurer un serveur de base de données Postgres, ouvrez un terminal et utilisez la commande [az postgres server create](https://docs.microsoft.com/cli/azure/postgres/server), comme indiqué dans l’exemple suivant. Remplacez les espaces réservés (y compris les crochets pointus) avec les valeurs de votre choix, en utilisant le même groupe de ressources que vous avez fourni précédemment pour votre instance App Service. Les informations d’identification administrateur que vous fournissez permettront l’accès futur ; par conséquent, veillez à les conserver pour une utilisation ultérieure.

```bash
az postgres server create \
    --name <server name> \
    --resource-group <resource group> \
    --location <location>
    --sku-name GP_Gen5_2 \
    --admin-user <administrator username> \
    --admin-password <administrator password> \
```

Après avoir exécuté cette commande, accédez au portail Azure et accédez à votre base de données Postgres. Lorsque le panneau est actif, copiez les valeurs du Nom du serveur et du Nom de connexion de l’administrateur du serveur, car vous en aurez besoin plus tard.

### <a name="allow-access-to-azure-services"></a>Autoriser l’accès aux services Azure

Dans le panneau **Sécurité de la connexion** d’Azure Database, basculez le bouton Autoriser l’accès aux services Azure sur la position **Activé**.

![Autorisation de l’accès aux services Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Mettre à jour votre application Java pour Postgres

Nous allons maintenant apporter quelques modifications à l’application Java pour qu’elle puisse utiliser notre base de données Postgres.

### <a name="add-postgres-credentials-to-the-pom"></a>Ajouter des informations d’identification Postgres au fichier POM

Dans le fichier *pom.xml*, remplacez les valeurs d’espace réservé en lettres majuscules par le nom du serveur Postgres, le nom de connexion de l’administrateur et le mot de passe. Ces champs se trouvent dans le plug-in Maven Azure (veillez à remplacer `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME` et `YOUR_PG_PASSWORD` dans les balises `<value>`et non dans les balises `<name>`).

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>Mettre à jour l’API Java Transaction

Nous devons ensuite modifier la configuration de l’API Java Transaction, afin que l’application Java communique avec Postgres plutôt qu’avec la base de données H2 en mémoire qui a été utilisée précédemment. Ouvrez *src/main/resources/META-INF/persistence.xml* dans un éditeur. Remplacez la valeur pour `<jta-data-source>` avec `java:jboss/datasources/postgresDS`. Votre fichier XML de l’API Java Transaction doit à présent contenir ce paramètre :

```xml
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
```

## <a name="configure-the-wildfly-application-server"></a>Configurer le serveur d’applications WildFly

Avant de déployer notre application reconfigurée, nous devons mettre à jour le serveur d’applications WildFly avec le module Postgres et ses dépendances. Pour plus d’informations de configuration, consultez [Configurer le serveur WildFly](configure-language-java.md#configure-java-ee-wildfly).

Pour configurer le serveur, nous avons besoin des quatre fichiers présents dans le répertoire  *wildfly_config/* :

- **postgresql-42.2.5.jar** : fichier JAR servant de pilote JDBC pour Postgres. Pour plus d’informations, visitez le [site officiel](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml** : fichier XML déclarant un nom pour le module Postgres (org.postgres). Il spécifie également les ressources et les dépendances nécessaires au fonctionnement du module.
- **jboss_cli_commands.cli** : fichier contenant les commandes de configuration à exécuter par l’interface CLI JBoss. Les commandes ajoutent le module Postgres au serveur d’applications WildFly, elles fournissent les informations d’identification, déclarent un nom JNDI, définissent le seuil de délai d’expiration, etc. Si vous ne connaissez pas l’interface CLI JBoss, référez-vous à la [documentation officielle](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh** : script shell s’exécutant chaque fois que votre instance App Service est démarrée. Le script n’assure qu’une fonction : la redirection des commandes dans *jboss_cli_commands.cli* vers l’interface CLI JBoss.

Nous vous suggérons fortement de lire le contenu de ces fichiers, en particulier *jboss_cli_commands.cli*.

### <a name="ftp-the-configuration-files"></a>Transférer via FTP les fichiers de configuration

Nous devons transférer via FTP le contenu de *wildfly_config/* sur notre instance App Service. Pour obtenir vos informations d’identification FTP, cliquez sur le bouton **Obtenir le profil de publication** qui est situé dans le panneau App Service du portail Azure. Vos nom d’utilisateur et mot de passe FTP se trouvent dans le document XML téléchargé. Pour plus d’informations sur le profil de publication, consultez [ce document](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

À l’aide d’un outil FTP de votre choix, transférez les quatre fichiers de *wildfly_config/* dans */home/site/deployments/tools/* . (Notez que vous ne devez pas transférer le répertoire, seulement les fichiers.)

### <a name="finalize-app-service"></a>Finaliser App Service

Dans le panneau App Service, accédez au volet Paramètres d’application. Sous « Runtime », définissez le champ « Fichier de démarrage » sur */home/site/deployments/tools/startup_script.sh*. De cette façon, vous êtes sûr que le script shell est exécuté après la création de l’instance App Service, et avant le démarrage du serveur WildFly.

Pour terminer, redémarrez votre instance App Service. Le bouton se trouve dans le panneau Vue d’ensemble.

## <a name="redeploy-the-application"></a>Redéployer l’application

Dans une fenêtre de terminal, regénérez et redéployez votre application.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Félicitations ! Votre application utilise désormais une base de données Postgres, et tous les enregistrements créés dans l’application sont stockés dans Postgres plutôt que dans la base de données H2 en mémoire utilisée précédemment. Pour le vérifier, vous pouvez créer un enregistrement et redémarrer votre instance App Service. Les enregistrements seront toujours là au redémarrage de votre application.

## <a name="clean-up"></a>Nettoyer

Si vous n’avez pas besoin de ces ressources pour un autre tutoriel (voir Étapes suivantes), vous pouvez les supprimer en exécutant la commande suivante :

```bash
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Déployer une application Java EE dans Azure avec Maven
> * Créez une base de données Postgres dans Azure
> * Configurer le serveur WildFly pour utiliser Postgres
> * Mise à jour et redéploiement de l’application
> * Exécuter des tests unitaires sur WildFly

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application.

> [!div class="nextstepaction"]
> [Tutoriel : Mappage d’un nom DNS personnalisé à une application](../app-service-web-tutorial-custom-domain.md)

Ou consultez les autres ressources :

> [!div class="nextstepaction"]
> [Configurer une application Java](configure-language-java.md)
