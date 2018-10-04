---
title: Créer une application Spring Boot sur Service Fabric dans Azure | Microsoft Docs
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
ms.openlocfilehash: 702d247bf331a4231b4b71ae6661ff88c9e0be2e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982764"
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-service-fabric"></a>Démarrage rapide : Déployer une application Spring Boot Java sur Service Fabric

Azure Service Fabric est une plateforme de systèmes distribués pour le déploiement et la gestion de microservices et de conteneurs.

Ce guide de démarrage rapide montre comment déployer une application Spring Boot sur Service Fabric. Il utilise l’exemple [Getting Started](https://spring.io/guides/gs/spring-boot/) disponible sur le site web de Spring. Il explique comment déployer l’exemple Spring Boot en tant qu’application Service Fabric à l’aide d’outils en ligne de commande familiers. Une fois que vous aurez terminé, l’exemple Getting Started de Spring Boot sera opérationnel sur Service Fabric.

![Capture d’écran de l’application](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Dans ce guide de démarrage rapide, vous apprenez à :

* Déployer une application Spring Boot sur Service Fabric
* Déployer l’application dans le cluster local
* Déployer l’application sur un cluster dans Azure
* Mettre à l’échelle avec une montée en puissance parallèle sur plusieurs nœuds
* Effectuer le basculement de votre service sans aucun impact sur la disponibilité

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide :

1. Installer le Kit de développement logiciel (SDK) Service Fabric et l’interface de ligne de commande (CLI) Service Fabric

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

1. [Installez Git](https://git-scm.com/)
1. Installer Yeoman

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
1. Configurer l’environnement Java

    a. [Mac](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Télécharger l’exemple

Dans une fenêtre de terminal, exécutez la commande suivante pour cloner l’exemple d’application Spring Boot Getting Started sur votre ordinateur local.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Générer l’application Spring Boot 
1. Dans le répertoire `gs-spring-boot/complete`, exécutez la commande ci-après pour générer l’application 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>Empaqueter l’application Spring Boot 
1. Dans le répertoire `gs-spring-boot` de votre clone, exécutez la commande `yo azuresfguest`. 

1. Entrez les informations suivantes à chaque invite.

    ![Entrées Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

1. Dans le dossier `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code`, créez un fichier nommé `entryPoint.sh`. Ajoutez le code suivant au fichier `entryPoint.sh`. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

1. Ajoutez la ressource **Endpoints** au fichier `gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml`

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    Le fichier **ServiceManifest.xml** ressemble désormais à ceci : 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

À ce stade, vous avez créé une application Service Fabric pour l’exemple Getting Started de Spring Boot que vous pouvez déployer sur Service Fabric.

## <a name="run-the-application-locally"></a>Exécuter l’application localement

1. Démarrez le cluster local sur des machines Ubuntu en exécutant la commande suivante :

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Si vous utilisez un Mac, démarrez le cluster local à partir de l’image Docker (cette opération implique que vous ayez respecté les [prérequis](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric) pour configurer votre cluster local pour Mac). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    Le démarrage du cluster local prend un certain temps. Pour vérifier que le cluster est entièrement opérationnel, accédez à Service Fabric Explorer à l’adresse **http://localhost:19080**. Les cinq nœuds sains indiquent que le cluster local est opérationnel. 
    
    ![Cluster local sain](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

1. Accédez au dossier `gs-spring-boot/SpringServiceFabric`.
1. Exécutez la commande suivante pour vous connecter à votre cluster local.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Exécutez le script `install.sh`.

    ```bash
    ./install.sh
    ```

1. Ouvrez votre navigateur web préféré, puis accédez à l’application à l’adresse **http://localhost:8080**.

    ![Frontend de l’application - Local](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Vous pouvez maintenant accéder à l’application Spring Boot qui a été déployée sur un cluster Service Fabric.

## <a name="deploy-the-application-to-azure"></a>Déploiement de l’application dans Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configurer un cluster Azure Service Fabric

Pour déployer l’application sur un cluster dans Azure, créez votre propre cluster.

Les clusters tiers sont des clusters Service Fabric gratuits, limités dans le temps et hébergés sur Azure. En outre, ils sont exécutés par l’équipe Service Fabric. Vous pouvez utiliser des clusters tiers pour déployer des applications et en apprendre davantage sur la plateforme. Le cluster utilise un seul certificat auto-signé pour la sécurité de nœud à nœud et de client à nœud.

Connectez-vous et rejoignez un [cluster Linux](http://aka.ms/tryservicefabric). Téléchargez le certificat PFX sur votre ordinateur en cliquant sur le lien **PFX**. Cliquez sur le lien **Lisez-moi** pour obtenir le mot de passe du certificat et les instructions associées à la configuration de plusieurs environnements pour utiliser le certificat. Gardez les pages **d’accueil** et **Lisez-moi** ouvertes, car vous allez suivre certaines des instructions qui y sont affichées dans la procédure ci-après.

> [!Note]
> Le nombre de clusters tiers disponibles par heure est limité. Si vous obtenez une erreur lors de votre inscription à un cluster tiers, attendez un peu et réessayez. Vous pouvez également suivre la procédure décrite dans [Didacticiel : Déployer un cluster Service Fabric Linux dans un réseau virtuel Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) pour créer un cluster dans votre abonnement.
>
> Le service Spring Boot est configuré pour écouter le trafic entrant sur le port 8080. Assurez-vous que ce port est ouvert dans votre cluster. Si vous utilisez le cluster tiers, ce port est ouvert.
>

Service Fabric propose plusieurs outils que vous pouvez utiliser pour gérer un cluster et ses applications :

* Service Fabric Explorer, un outil reposant sur un navigateur.
* Interface CLI Service Fabric, qui s’exécute sur Azure CLI.
* Commandes PowerShell.

Dans ce démarrage rapide, vous utilisez la CLI Service Fabric et Service Fabric Explorer.

Pour utiliser la CLI, vous devez créer un fichier PEM basé sur le fichier PFX que vous avez téléchargé. Pour convertir le fichier, utilisez la commande suivante. (Pour les clusters tiers, vous pouvez copier une commande spécifique à votre fichier PFX dans les instructions de la page **Lisez-moi**.)

```bash
openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
``` 

Pour utiliser Service Fabric Explorer, vous devez importer le fichier PFX du certificat que vous avez téléchargé depuis le site du cluster tiers dans votre magasin de certificats (Windows ou Mac) ou dans le navigateur lui-même (Ubuntu). Vous avez besoin du mot de passe de la clé privée PFX, que vous pouvez obtenir depuis la page **Lisez-moi**.

Utilisez la méthode qui vous convient le mieux pour importer les certificats sur votre système. Par exemple :

* Sur Windows : double-cliquez sur le fichier PFX et suivez les invites pour installer le certificat dans votre magasin personnel, `Certificates - Current User\Personal\Certificates`. Vous pouvez également utiliser la commande PowerShell dans les instructions **Lisez-moi**.
* Sur Mac : double-cliquez sur le fichier PFX et suivez les invites pour installer le certificat dans votre application Keychain.
* Sur Ubuntu : Mozilla Firefox est le navigateur par défaut dans Ubuntu 16.04. Pour importer le certificat dans Firefox, cliquez sur le bouton de menu dans le coin supérieur droit de votre navigateur, puis cliquez sur **Options**. Sur la page **Préférences**, recherchez « certificats » via le champ de recherche. Cliquez sur **Afficher les certificats**, sélectionnez l’onglet **Your Certificates** (Vos certificats), cliquez sur **Importer** et suivez les invites pour importer le certificat.

   ![Installer le certificat sur Firefox](./media/service-fabric-quickstart-java-spring-boot/install-cert-firefox.png)

### <a name="deploy-the-application-using-cli"></a>Déployer l’application à l’aide de l’interface CLI

Maintenant que l’application et votre cluster sont prêts, vous pouvez déployer l’application sur le cluster directement à partir de la ligne de commande.

1. Accédez au dossier `gs-spring-boot/SpringServiceFabric`.
1. Exécutez la commande suivante pour vous connecter à votre cluster Azure.

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
1. Exécutez le script `install.sh`.

    ```bash
    ./install.sh
    ```

1. Ouvrez votre navigateur web, puis accédez à l’application ici : **http://\<ConnectionIPOrUrl>:8080**.

    ![Frontend de l’application - Local](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)

Vous pouvez maintenant accéder à l’application Spring Boot exécutée dans un cluster Service Fabric sur Azure.

## <a name="scale-applications-and-services-in-a-cluster"></a>Mettre à l’échelle les applications et services dans un cluster

Les services peuvent facilement être mis à l’échelle dans un cluster pour prendre en compte une modification de la charge sur les services. Pour mettre à l’échelle un service, vous modifiez le nombre d’instances s’exécutant dans le cluster. Il existe différentes méthode pour mettre à l’échelle vos services. Par exemple, vous pouvez utiliser des scripts ou des commandes de l’interface CLI de Service Fabric (sfctl). Dans les étapes suivantes, utilisez Service Fabric Explorer.

Service Fabric Explorer s’exécute dans tous les clusters Service Fabric et est accessible à partir d’un navigateur, en accédant au port de gestion HTTP (19080) du cluster, par exemple, `http://localhost:19080`.

Pour mettre à l’échelle le service web frontal, procédez comme suit :

1. Ouvrez Service Fabric Explorer dans votre cluster. Par exemple, `http://localhost:19080`.
1. Cliquez sur le bouton de sélection (points de suspension) à côté du nœud **fabric:/SpringServiceFabric/SpringGettingStarted** dans l’arborescence, puis choisissez **Scale Service (Mettre à l’échelle le service)**.

    ![Mettre à l’échelle le service dans Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Vous pouvez maintenant choisir de mettre à l’échelle le nombre d’instances du service.

1. Définissez le nombre sur **3**, puis cliquez sur **Scale Service**.

    Une autre façon de mettre à l’échelle le service consiste à utiliser la ligne de commande.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Cliquez sur le nœud **fabric:/SpringServiceFabric/SpringGettingStarted** dans l’arborescence, puis développez le nœud de la partition (représenté par un GUID).

    ![Mettre à l’échelle le service dans Service Fabric Explorer - Terminé](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    Le service possède trois instances, et l’arborescence indique les nœuds sur lesquels elles s’exécutent.

Via cette tâche de gestion simple, vous avez doublé les ressources disponibles pour permettre au service frontal de traiter la charge utilisateur. Il est important de comprendre que vous n’avez pas besoin de plusieurs instances d’un service pour que celui-ci s’exécute de manière fiable. En cas de défaillance d’un service, Service Fabric veille à ce qu’une nouvelle instance du service s’exécute dans le cluster.

## <a name="fail-over-services-in-a-cluster"></a>Services de basculement dans un cluster

Pour illustrer le basculement du service, il est possible de simuler un redémarrage du nœud à l’aide de Service Fabric Explorer. Vérifiez qu’une seule instance de votre service est en cours d’exécution.

1. Ouvrez Service Fabric Explorer dans votre cluster. Par exemple, `http://localhost:19080`.
1. Cliquez sur le bouton de sélection (points de suspension) en regard du nœud qui exécute l’instance de votre service, et redémarrez le nœud.

    ![Redémarrage du nœud dans Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
1. L’instance de votre service est alors déplacée dans un autre nœud, sans que votre application subisse d’interruption de service.

    ![Succès du redémarrage du nœud dans Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

* Déployer une application Spring Boot sur Service Fabric
* Déployer l’application dans le cluster local
* Déployer l’application sur un cluster dans Azure
* Mettre à l’échelle avec une montée en puissance parallèle sur plusieurs nœuds
* Effectuer le basculement de votre service sans aucun impact sur la disponibilité

Pour en savoir plus sur l’utilisation des applications Java dans Service Fabric, passez au tutoriel sur les applications Java.

> [!div class="nextstepaction"]
> [Didacticiel : créer et déployer une application avec un service frontal API Web Java et un service principal avec état](./service-fabric-tutorial-create-java-app.md)
