---
title: Utiliser une base de données Open Service Broker pour Azure avec une application sur Pivotal Cloud Foundry
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/11/2019
ms.topic: tutorial
description: Explique comment configurer une application Pivotal Cloud Foundry pour utiliser une base de données Open Service Broker pour Azure.
keywords: Pivotal Cloud Foundry, Cloud Foundry, Open Service Broker, Open Service Broker pour Azure
tags: Cloud-Foundry
ms.openlocfilehash: d553cd09ef42e47e3a10fb96039063b8aae665cb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258867"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>Tutoriel : Utiliser une base de données Open Service Broker pour Azure avec une application sur Pivotal Cloud Foundry

L’utilisation d’Open Service Broker pour Azure avec une instance Pivotal Cloud Foundry vous permet de provisionner des services, comme des bases de données, dans Azure, directement à partir de l’interface CLI Cloud Foundry et de votre instance Pivotal Cloud Foundry. Vous pouvez également lier ces services à une application en cours d’exécution dans votre instance Pivotal Cloud Foundry. Quand vous liez une application à un service de cette manière, vous n’avez pas besoin de mettre à jour du code ou une configuration au sein de votre application. Cet article explique comment utiliser un service Open Service Broker pour Azure pour une base de données avec une application dans Pivotal Cloud Foundry.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparer votre espace d’application dans Pivotal Cloud Foundry.
> * Cloner un exemple de source de l’application à partir de GitHub.
> * Préparer l’application au déploiement.
> * Déployez l’application.
> * Créer une base de données à l’aide d’Open Service Broker pour Azure.
> * Lier la base de données à votre application.

## <a name="prerequisites"></a>Prérequis

Avant de continuer, vous devez avoir effectué les opérations suivantes :

* [Installation et configuration de Pivotal Cloud Foundry](create-cloud-foundry-on-azure.md)
* [Installation et configuration d’Open Service Broker pour Azure](https://network.pivotal.io/products/azure-open-service-broker-pcf) avec votre instance Cloud Foundry

Voici un exemple d’écran Pivotal Cloud Foundry Ops Manager avec la vignette du service Open Service Broker pour Azure installé et configuré :

![Pivotal Cloud Foundry avec Open Service Broker pour Azure installé](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>Préparer votre espace d’application dans Pivotal Cloud Foundry

Pour déployer votre application sur votre instance Pivotal Cloud Foundry, vous devez vous connecter avec l’outil en ligne de commande `cf`. Vous devez également avoir ciblé vos organisation et espace souhaités.

Pour vérifier que vous êtes connecté et afficher l’espace que vous ciblez, utilisez `cf target`. L’exemple ci-dessous montre que l’utilisateur est déjà connecté en tant qu’*administrateur*, qu’il utilise l’organisation *myorg* et qu’il cible l’espace *dev* :

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

Pour vous connecter, utilisez `cf login` :

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Pour créer une organisation et un espace, utilisez `cf create-org` et `cf create-space` :

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

Pour cibler un espace, utilisez `cf target` :

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>Obtenir le code d’application

Pour qu’une application utilise Open Service Broker pour Azure, elle a besoin d’utiliser une source de données, comme une base de données. Dans cet article, nous allons utiliser l’[exemple d’application musicale de Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) pour illustrer une application qui utilise une source de données.

Clonez l’application à partir de GitHub, puis accédez à son répertoire :

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> Pour voir les sources de données de cette application, ouvrez le fichier [src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml).


## <a name="prepare-the-application-for-deployment"></a>Préparer l’application au déploiement

Avant de pouvoir déployer l’application sur votre instance Pivotal Cloud Foundry, vous devez la générer. À des fins de démonstration, nous activons également une journalisation de *débogage* qui permettra de journaliser les informations de connexion de source de données.

Pour activer la journalisation de *débogage* des détails de connexion de base de données, ajoutez la propriété yaml ci-dessous à la fin du fichier *application.yml* :

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

L’exemple d’application utilise Gradle pour générer l’application dans un fichier jar exécutable Spring Boot. Le fichier jar exécutable sera déployé sur votre instance Pivotal Cloud Foundry. Pour générer l’application :

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>Déployer votre application

Utilisez la commande `cf push` pour déployer l’application sur votre instance Pivotal Cloud Foundry :

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

Copiez la valeur de *routes* affichée dans la sortie de `cf push`. La route correspond à l’URL que vous allez utiliser pour accéder à l’application en cours d’exécution. Par exemple : 

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


Une fois que votre application est déployée, vous pouvez consulter ses journaux d’activité pour voir l’URL de connexion qu’elle utilise. La commande ci-dessous affiche les journaux d’activité de l’application et utilise `grep` pour rechercher la configuration *jdbcUrl*.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

Notez que l’application utilise *h2:mem:testdb*, qui est la base de données en mémoire. Une application Spring est automatiquement configurée pour utiliser une base de données en mémoire quand il existe une dépendance de base de données en mémoire vis-à-vis du chemin de classe et quand la [configuration automatique](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html) est activée. L’exemple d’application a la [dépendance de base de données en mémoire h2 configurée](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49) et la configuration automatique activée dans [src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8).

Utilisez la route de l’application pour y accéder dans un navigateur. La route, ou l’URL, s’affiche dans la sortie de la commande `cf push`.

> [!TIP]
> Vous pouvez également afficher l’URL de l’application en exécutant `cf apps`.

Une fois que vous accédez à l’application à l’aide de votre navigateur, supprimez quelques albums existants et créez-en quelques-uns. L’exemple d’application utilise la base de données en mémoire pour enregistrer vos modifications. Vous remarquerez également que l’application a été renseignée avec quelques [données par défaut](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). 

![Application musicale Spring avec des données par défaut](media/music-app.png)

Étant donné que votre application utilise une base de données en mémoire, vos modifications ne seront pas conservées si vous la redémarrez ou la redéployez. Pour illustrer cette non-persistance des modifications, une fois que vous les avez apportées, effectuez une copie intermédiaire de votre application à l’aide de `cf restage` :

```cmd
cf restage spring-music
```

Une fois la copie intermédiaire de l’application effectuée, accédez-y dans un navigateur à l’aide de la même URL. Notez que les modifications que vous avez apportées ont disparu et que les données par défaut sont affichées.

![Application musicale Spring avec des données par défaut](media/music-app.png)

## <a name="create-a-database"></a>Créer une base de données

Pour créer une base de données persistante sur Azure à l’aide d’Open Service Broker, utilisez la commande `cf create-service`. La commande ci-dessous provisionne une base de données PostgreSQL dans Azure dans le groupe de ressources *MyResourceGroup* dans la région *eastus*. Pour plus d’informations sur les paramètres *resourceGroup*, *location* et d’autres paramètres JSON propres à Azure, consultez la [documentation de référence du module PostgreSQL](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision) :

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

La base de données est exposée dans votre instance Pivotal Cloud Foundry en tant que service nommé *mypgsql*. Une fois que votre base de données a terminé le provisionnement, lequel ne doit durer que quelques minutes, vous pouvez la lier à votre application. Pour vérifier que votre base de données a terminé le provisionnement, utilisez la commande `cf services` :

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

La valeur de la *dernière opération* du service sera *create succeeded* (création réussie) quand le provisionnement sera terminé.

## <a name="bind-the-database-to-your-application"></a>Lier la base de données à votre application

Utilisez la commande `bind-service` pour lier le service à votre application.

```cmd
cf bind-service spring-music mypgsql
```

Après avoir lié le service à votre application, vous devez encore effectuer une copie intermédiaire de l’application pour que les modifications prennent effet.

```cmd
cf restage spring-music
```

L’application est [configurée pour utiliser Spring Cloud Connectors](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46), ce qui permet à votre instance Pivotal Cloud Foundry d’utiliser la [configuration automatique](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto) sur la source de données de votre application. Dans cet exemple, votre instance Pivotal Cloud Foundry reconfigure automatiquement votre application pour utiliser un service auquel elle est liée pour une source de données au moment où la copie intermédiaire de votre application est effectuée.

Une fois que la copie intermédiaire de votre application a été effectuée, elle utilise *mypgsql* pour stocker les données au lieu de la base de données en mémoire.

Toutes les modifications que vous apportez sont désormais persistantes après des redémarrages et des redéploiements. Vous pouvez également voir l’URL de connexion que l’application utilise en reconsultant les journaux d’activité de l’application.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

Remarquez qu’il y a deux entrées :

* La valeur d’origine *h2:mem:testdb*.
* La valeur mise à jour pour votre base de données PostgreSQL quand la copie intermédiaire de votre application a été effectuée.

Pour vérifier que les données persistent dans la base de données PostgreSQL, accédez à l’application dans votre navigateur, apportez des modifications, puis effectuez une copie intermédiaire de votre application :

```cmd
cf restage spring-music
```

Une fois la copie intermédiaire de l’application effectuée, accédez-y dans un navigateur à l’aide de la même URL. Remarquez que les modifications que vous avez apportées sont toujours là.

## <a name="cleanup"></a>Nettoyage

Pour déconnecter votre application de la base de données, vous pouvez annuler le lien à l’aide de la commande `cf unbind-service`.

```cmd
cf unbind-service spring-music mypgsql
```

Comme quand vous liez votre application à un service, vous devez effectuer une copie intermédiaire de votre application pour que ces modifications prennent effet. Cette action laisse à la fois le service *mypgsql* et votre application intacts, mais votre application commence à utiliser la base de données en mémoire au lieu de *mypgsql*.

Pour supprimer votre base de données, vous pouvez utiliser la commande `cf delete-service`. *Vous ne pouvez pas annuler cette action, donc soyez bien sûr de vouloir supprimer votre base de données avant de continuer.*

```cmd
cf delete-service mypgsql
```

Pour supprimer votre application de votre instance Pivotal Cloud Foundry :
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel a couvert le déploiement d’une application sur Pivotal Cloud Foundry, ainsi que la création d’une base de données à l’aide d’Open Service Broker pour Azure. Il a également couvert la liaison de votre base de données à votre application au sein de votre instance Pivotal Cloud Foundry. Pour plus d’informations sur le déploiement d’applications sur Cloud Foundry sur Azure, consultez :

* [Cloud Foundry sur Azure](../virtual-machines/linux/cloudfoundry-get-started.md)
* [Déployer votre première application sur Cloud Foundry sur Microsoft Azure](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)