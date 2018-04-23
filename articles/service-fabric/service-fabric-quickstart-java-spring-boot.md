---
title: Déployer une application Spring Boot sur Azure Service Fabric | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous déployez une application Spring Boot sur Azure Service Fabric à l’aide de l’exemple d’application Spring Boot.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: e41a7754e6e170dda7818bceadab7858a9d9fa76
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-azure"></a>Démarrage rapide : déployer une application Spring Boot Java sur Azure
Azure Service Fabric est une plateforme de systèmes distribués pour le déploiement et la gestion de microservices et de conteneurs. 

Ce guide de démarrage rapide montre comment déployer une application Spring Boot sur Service Fabric. Il utilise l’exemple [Getting Started](https://spring.io/guides/gs/spring-boot/) disponible sur le site web de Spring. Il explique comment déployer l’exemple Spring Boot en tant qu’application Service Fabric à l’aide d’outils en ligne de commande familiers. Une fois que vous aurez terminé, l’exemple Getting Started de Spring Boot sera opérationnel sur Service Fabric. 

![Capture d’écran de l’application](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Dans ce guide de démarrage rapide, vous apprenez à :

* Déployer une application Spring Boot sur Service Fabric
* Déployer l’application dans le cluster local 
* Déployer l’application sur un cluster dans Azure
* Mettre à l’échelle avec une montée en puissance parallèle sur plusieurs nœuds
* Effectuer le basculement de votre service sans aucun impact sur la disponibilité

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide :
1. [Installez le SDK Service Fabric et l’interface CLI Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Installez Git](https://git-scm.com/)
3. [Installez Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. [Configurez l’environnement Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Téléchargez l’exemple
Dans une fenêtre de terminal, exécutez la commande suivante pour cloner l’exemple d’application Spring Boot Getting Started sur votre ordinateur local.
```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>Empaqueter l’application Spring Boot 
1. Dans le répertoire `gs-spring-boot` de votre clone, exécutez la commande `yo azuresfguest`. 

2. Entrez les informations suivantes à chaque invite. 

    ![Entrées Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. Dans le dossier `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code`, créez un fichier nommé `entryPoint.sh`. Ajoutez le code suivant au fichier. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

À ce stade, vous avez créé une application Service Fabric pour l’exemple Getting Started de Spring Boot que vous pouvez déployer sur Service Fabric.

## <a name="run-the-application-locally"></a>Exécuter l’application localement
1. Démarrez le cluster local en exécutant la commande suivante :

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Le démarrage du cluster local prend un certain temps. Pour vérifier que le cluster est entièrement opérationnel, accédez à Service Fabric Explorer avec **http://localhost:19080**. Les cinq nœuds sains indiquent que le cluster local est opérationnel. 
    
    ![Cluster local sain](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. Accédez au dossier `gs-spring-boot/SpringServiceFabric`.
3. Exécutez la commande suivante pour vous connecter à votre cluster local. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. Exécutez le script `install.sh`. 

    ```bash
    ./install.sh
    ```

5. Ouvrez votre navigateur web par défaut, puis accédez à l’application en accédant à **http://localhost:8080**. 

    ![Frontend de l’application - Local](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
Vous pouvez maintenant accéder à l’application Spring Boot qui a été déployée sur un cluster Service Fabric.  

## <a name="deploy-the-application-to-azure"></a>Déploiement de l'application dans Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configurer un cluster Azure Service Fabric
Pour déployer l’application sur un cluster dans Azure, créez votre propre cluster.

Les clusters tiers sont des clusters Service Fabric gratuits, limités dans le temps et hébergés sur Azure. En outre, ils sont exécutés par l’équipe Service Fabric. Vous pouvez utiliser des clusters de tiers pour déployer des applications et en apprendre davantage sur la plateforme. Le cluster utilise un seul certificat auto-signé pour la sécurité de nœud à nœud et de client à nœud.

Connectez-vous et rejoignez un [cluster Linux](http://aka.ms/tryservicefabric). Téléchargez le certificat PFX sur votre ordinateur en cliquant sur le lien **PFX**. Cliquez sur le lien **Lisez-moi** pour obtenir le mot de passe du certificat et les instructions associées à la configuration de plusieurs environnements pour utiliser le certificat. Gardez les pages **d’accueil** et **Lisez-moi** ouvertes, car vous allez suivre certaines des instructions qui y sont affichées dans la procédure ci-après. 

> [!Note]
> Le nombre de clusters tiers disponibles par heure est limité. Si vous obtenez une erreur lors de votre inscription à un cluster tiers, attendez un peu et réessayez. Vous pouvez également suivre la procédure décrite dans [Créer un cluster Service Fabric dans Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) pour créer un cluster dans votre abonnement. 
>
> Le service Spring Boot est configuré pour écouter le trafic entrant sur le port 8080. Assurez-vous que ce port est ouvert dans votre cluster. Si vous utilisez le cluster tiers, ce port est ouvert.
>

Service Fabric propose plusieurs outils que vous pouvez utiliser pour gérer un cluster et ses applications :

- Service Fabric Explorer, un outil reposant sur un navigateur.
- L’interface CLI Service Fabric, qui s’exécute sur Azure CLI 2.0.
- Commandes PowerShell. 

Dans ce démarrage rapide, vous utilisez CLI Service Fabric et Service Fabric Explorer. 

Pour utiliser l’interface CLI, vous devez créer un fichier PEM basé sur le fichier PFX que vous avez téléchargé. Pour convertir le fichier, utilisez la commande suivante. (Pour les clusters tiers, vous pouvez copier une commande spécifique à votre fichier PFX dans les instructions de la page **Lisez-moi**.)

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

Pour utiliser Service Fabric Explorer, vous devez importer le fichier PFX du certificat que vous avez téléchargé depuis le site du cluster tiers dans votre magasin de certificats (Windows ou Mac) ou dans le navigateur lui-même (Ubuntu). Vous avez besoin du mot de passe de la clé privée PFX, que vous pouvez obtenir depuis la page **Lisez-moi**.

Utilisez la méthode qui vous convient le mieux pour importer les certificats sur votre système. Par exemple : 

- Sur Windows : double-cliquez sur le fichier PFX et suivez les invites pour installer le certificat dans votre magasin personnel, `Certificates - Current User\Personal\Certificates`. Vous pouvez également utiliser la commande PowerShell dans les instructions **Lisez-moi**.
- Sur Mac : double-cliquez sur le fichier PFX et suivez les invites pour installer le certificat dans votre chaîne de clés.
- Sur Ubuntu : Mozilla Firefox est le navigateur par défaut dans Ubuntu 16.04. Pour importer le certificat dans Firefox, cliquez sur le bouton de menu dans le coin supérieur droit de votre navigateur, puis cliquez sur **Options**. Sur la page **Préférences**, recherchez « certificats » via la zone de recherche. Cliquez sur **Afficher les certificats**, sélectionnez l’onglet **Vos certificats**, cliquez sur **Importer** et suivez les invites pour importer le certificat.
 
   ![Installer le certificat sur Firefox](./media/service-fabric-quickstart-java-spring-boot/install-cert-firefox.png) 


### <a name="deploy-the-application-using-cli"></a>Déployer l’application à l’aide de l’interface CLI
Maintenant que l’application et votre cluster sont prêts, vous pouvez déployer l’application sur le cluster directement à partir de la ligne de commande.

1. Accédez au dossier `gs-spring-boot/SpringServiceFabric`.
2. Exécutez la commande suivante pour vous connecter à votre cluster Azure. 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    Si le cluster est sécurisé avec un certificat auto-signé, vous exécutez la commande suivante : 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Exécutez le script `install.sh`. 

    ```bash
    ./install.sh
    ```

4. Ouvrez votre navigateur web, puis accédez à l’application ici : **http://\<ConnectionIPOrUrl>:8080**. 

    ![Frontend de l’application - Local](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
Vous pouvez maintenant accéder à l’application Spring Boot exécutée dans un cluster Service Fabric sur Azure.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Mettre à l’échelle les applications et services dans un cluster
Les services peuvent facilement être mis à l’échelle dans un cluster pour prendre en compte une modification de la charge sur les services. Pour mettre à l’échelle un service, vous modifiez le nombre d’instances s’exécutant dans le cluster. Il existe différentes méthode pour mettre à l’échelle vos services. Par exemple, vous pouvez utiliser des scripts ou des commandes de l’interface CLI de Service Fabric (sfctl). Dans les étapes suivantes, utilisez Service Fabric Explorer.

Service Fabric Explorer s’exécute dans tous les clusters Service Fabric et est accessible à partir d’un navigateur, en accédant au port de gestion HTTP des clusters (19080), par exemple, `http://localhost:19080`.

Pour mettre à l’échelle le service frontal web, procédez comme suit :

1. Ouvrez Service Fabric Explorer dans votre cluster. Par exemple, `http://localhost:19080`.
2. Cliquez sur le bouton de sélection (points de suspension) à côté du nœud ****fabric:/SpringServiceFabric/SpringGettingStarted** dans l’arborescence, puis choisissez **Scale Service (Mettre à l’échelle le service)**.

    ![Mettre à l’échelle le service dans Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Vous pouvez maintenant choisir de mettre à l’échelle le nombre d’instances du service.

3. Définissez le nombre sur **3**, puis cliquez sur **Scale Service**.

    Une autre façon de mettre à l’échelle le service consiste à utiliser la ligne de commande :

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint http://localhost:19080

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. Cliquez sur le nœud **fabric:/SpringServiceFabric/SpringGettingStarted** dans l’arborescence, puis développez le nœud de la partition (représenté par un GUID).

    ![Mettre à l’échelle le service dans Service Fabric Explorer - Terminé](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    Le service possède trois instances, et l’arborescence indique les nœuds sur lesquels elles s’exécutent.

Via cette tâche de gestion simple, vous avez doublé les ressources disponibles pour permettre au service frontal de traiter la charge utilisateur. Il est important de comprendre que vous n’avez pas besoin de plusieurs instances d’un service pour que celui-ci s’exécute de manière fiable. En cas de défaillance d’un service, Service Fabric veille à ce qu'une nouvelle instance du service s’exécute dans le cluster.

## <a name="fail-over-services-in-a-cluster"></a>Services de basculement dans un cluster 
Pour illustrer le basculement du service, il est possible de simuler un redémarrage du nœud à l’aide de Service Fabric Explorer. Vérifiez qu’une seule instance de votre service est en cours d’exécution. 

1. Ouvrez Service Fabric Explorer dans votre cluster. Par exemple, `http://localhost:19080`.
2. Cliquez sur le bouton de sélection (points de suspension) en regard du nœud qui exécute l’instance de votre service, et redémarrez le nœud. 

    ![Redémarrage du nœud dans Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. L’instance de votre service est alors déplacée dans un autre nœud, sans que votre application subisse d’interruption de service. 

    ![Succès du redémarrage du nœud dans Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez appris comment :

* Déployer une application Spring Boot sur Service Fabric
* Déployer l’application dans le cluster local 
* Déployer l’application sur un cluster dans Azure
* Mettre à l’échelle avec une montée en puissance parallèle sur plusieurs nœuds
* Effectuer le basculement de votre service sans aucun impact sur la disponibilité

Pour en savoir plus sur l’utilisation des applications Java dans Service Fabric, passez au tutoriel sur les applications Java.

> [!div class="nextstepaction"]
> [Déployer une application Java](./service-fabric-tutorial-create-java-app.md)
