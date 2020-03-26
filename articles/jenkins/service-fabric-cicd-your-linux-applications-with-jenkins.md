---
title: Génération en continu pour les applications Linux à l’aide de Jenkins
description: Génération et intégration en continu pour votre application Linux Service Fabric à l’aide de Jenkins
keywords: jenkins, azure, devops, cicd, linux
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 96e8c3721b5849b874878ea6a846271fb6760ffd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77674642"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Utiliser Jenkins pour générer et déployer vos applications Linux

Ce tutoriel décrit différentes méthodes possibles pour configurer votre environnement Jenkins ainsi que différentes méthodes pour déployer votre application sur un cluster Service Fabric une fois qu’elle a été générée. Suivez ces étapes générales pour configurer Jenkins, extraire les modifications de GitHub, générer votre application et la déployer sur votre cluster :

1. Veillez à installer les [composants requis](#prerequisites).
1. Ensuite, suivez les étapes décrites de l’une de ces sections pour configurer Jenkins :
   * [Configurer Jenkins à l’intérieur d’un cluster Service Fabric](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Configurer Jenkins en dehors d’un cluster Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster), ou
   * [Installer le plug-in Service Fabric dans un environnement Jenkins existant](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Lorsque que vous avez configuré Jenkins, suivez les étapes de [Créer et configurer un travail Jenkins](#create-and-configure-a-jenkins-job) pour configurer GitHub afin de déclencher Jenkins lorsque des modifications sont apportées à votre application et pour configurer le pipeline de votre travail Jenkins via l’étape de génération pour extraire les modifications de GitHub et générer votre application. 
1. Enfin, configurez l’étape post-génération de travail Jenkins pour déployer votre application sur votre cluster Service Fabric. Deux méthodes de configuration de Jenkins pour déployer votre application sur un cluster sont possibles :    
   * Pour les environnements de développement et de test, utilisez [Configurer le déploiement à l’aide d’un point de terminaison de gestion de cluster](#configure-deployment-using-cluster-management-endpoint). Il s’agit de la méthode de déploiement la plus simple à configurer.
   * Pour les environnements de production, utilisez [Configurer le déploiement à l’aide des informations d’identification Azure](#configure-deployment-using-azure-credentials). Microsoft recommande cette méthode pour les environnements de production, car les informations d’identification Azure vous permettent de limiter l’accès dont dispose un travail Jenkins sur vos ressources Azure. 

## <a name="prerequisites"></a>Prérequis

- Assurez-vous que Git est installé localement. Vous pouvez installer la version appropriée de Git à partir de [la page de téléchargements de Git](https://git-scm.com/downloads) en fonction de votre système d’exploitation. Si vous débutez avec Git, vous trouverez plus d’informations à ce sujet dans la [documentation Git](https://git-scm.com/docs).
- Cet article utilise l’*exemple de prise en main de Service Fabric* sur GitHub : [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) pour l’application à générer et déployer. Vous pouvez dupliquer ce référentiel pour suivre la procédure, ou en déviant légèrement des instructions, utiliser votre propre projet GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Installer le plug-in Service Fabric dans un environnement Jenkins existant

Si vous ajoutez le plug-in Service Fabric à un environnement Jenkins existant, vous devez effectuer les étapes suivantes :

- [Interface CLI de Service Fabric (sfctl)](../service-fabric/service-fabric-cli.md) Installez l’interface CLI au niveau du système et non pas au niveau de l’utilisateur, pour que Jenkins puisse exécuter des commandes CLI. 
- Pour déployer des applications Java, installez [Gradle et Open JDK 8.0](../service-fabric/service-fabric-get-started-linux.md#set-up-java-development). 
- Pour déployer des applications .NET Core 2.0, installez le [Kit de développement logiciel (SDK) .NET Core 2.0](../service-fabric/service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Lorsque vous avez installé les composants requis nécessaires pour votre environnement, vous pouvez rechercher le plug-in Azure Service Fabric dans le marketplace Jenkins et l’installer.

Une fois que vous avez installé le plug-in, passez à [Créer et configurer un travail Jenkins](#create-and-configure-a-jenkins-job).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configurer Jenkins à l’intérieur d’un cluster Service Fabric

Vous pouvez configurer Jenkins à l’intérieur ou en dehors d’un cluster Service Fabric. Les sections suivantes montrent comment le configurer dans un cluster lors de l’utilisation d’un compte de stockage Azure pour enregistrer l’état de l’instance de conteneur.

1. Vérifiez que vous disposez d’un cluster Linux Service Fabric sur lequel Docker est installé. Docker est déjà installé sur les clusters Service Fabric exécutés dans Azure. Si vous exécutez le cluster localement (environnement de développement OneBox), vérifiez si Docker est installé sur votre ordinateur avec la commande `docker info`. S’il n’est pas installé, installez-le en utilisant les commandes suivantes :

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Assurez-vous que le port 8081 est spécifié comme un point de terminaison personnalisé sur le cluster. Si vous utilisez un cluster local, assurez-vous que le port 8081 est ouvert sur la machine hôte et qu’il dispose d’une adresse IP publique.
   >

1. Clonez l’application en utilisant les commandes suivantes :
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Conservez l’état du conteneur Jenkins dans un partage de fichiers :
   1. Créez un compte de stockage Azure dans la **même région** que votre cluster, avec un nom tel que `sfjenkinsstorage1`.
   1. Créez un **partage de fichiers** sous ce compte de stockage, avec un nom tel que `sfjenkins`.
   1. Cliquez sur **Connecter** pour le partage de fichiers et notez les valeurs affichées sous **Connexion à partir de Linux** ; la valeur devrait ressembler à ce qui suit :

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Pour que vous puissiez monter des partages cifs, le package cifs-utils doit être installé dans les nœuds de cluster.      
   >

1. Mettez à jour les valeurs d’espace réservé dans le script `setupentrypoint.sh` avec les détails azure-storage de l’étape 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Remplacez `[REMOTE_FILE_SHARE_LOCATION]` par la valeur `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` de la sortie de la connexion à l’étape 2 ci-dessus.
   * Remplacez `[FILE_SHARE_CONNECT_OPTIONS_STRING]` par la valeur `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` de l’étape 2 ci-dessus.

1. **Cluster sécurisé uniquement :** 
   
   Pour configurer le déploiement d’applications sur un cluster sécurisé à partir de Jenkins, le certificat de cluster doit être accessible au sein du conteneur Jenkins. Dans le fichier *ApplicationManifest.xml*, sous la balise **ContainerHostPolicies**, ajoutez cette référence de certificat et mettez à jour la valeur de l’empreinte avec celle du certificat de cluster.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Ajoutez également les lignes suivantes sous la balise **ApplicationManifest** (racine) dans le fichier *ApplicationManifest.xml* et mettez à jour la valeur de l’empreinte avec celle du certificat de cluster.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Connectez-vous au cluster et installez l’application de conteneur.

   **Cluster sécurisé**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   La commande précédente accepte le certificat au format PEM. Si votre certificat est au format PFX, vous pouvez utiliser la commande suivante pour le convertir. Si votre fichier PFX n’est pas protégé par un mot de passe, spécifiez le paramètre **passin** en tant que `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Cluster non sécurisé**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Cela permet d’installer un conteneur Jenkins sur le cluster et de l’analyser à l’aide de Service Fabric Explorer.

   > [!NOTE]
   > Le téléchargement de l’image Jenkins sur le cluster peut prendre quelques minutes.
   >

1. Dans votre navigateur, accédez à `http://PublicIPorFQDN:8081`. Vous obtenez le chemin d’accès du mot de passe administrateur initial requis pour vous connecter. 
1. Consultez Service Fabric Explorer pour déterminer le nœud sur lequel le conteneur Jenkins est en cours d’exécution. Connectez-vous via Secure Shell (SSH) à ce nœud.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Obtenez l’ID d’instance conteneur à l’aide de `docker ps -a`.
1. Connectez-vous en SSH (Secure Shell) au conteneur et collez le chemin d’accès indiqué sur le portail Jenkins. Par exemple, si dans le portail le chemin d’accès indiqué est `PATH_TO_INITIAL_ADMIN_PASSWORD`, exécutez les commandes suivantes :

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Sur la page de prise en main de Jenkins, choisissez l’option Sélectionner les plug-ins à installer, cochez la case **Aucun**, puis cliquez sur Installer.
1. Créez un utilisateur ou sélectionnez cette option pour continuer en tant qu’administrateur.

Une fois que vous avez configuré Jenkins, passez à [Créer et configurer un travail Jenkins](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configurer Jenkins en dehors d’un cluster Service Fabric

Vous pouvez configurer Jenkins à l’intérieur ou en dehors d’un cluster Service Fabric. Les sections suivantes expliquent comment le configurer en dehors d’un cluster.

1. Vérifiez que Docker est installé sur votre machine en exécutant `docker info` dans le terminal. La sortie indique que le service Docker est en cours d’exécution.

1. Si Docker n’est pas installé, exécutez les commandes suivantes :

    ```sh
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
    ```

1. Extrayez l’image du conteneur Jenkins de Service Fabric : `docker pull rapatchi/jenkins:latest`. Cette image est fournie avec le plug-in Jenkins de Service Fabric préinstallé.
1. Exécutez l’image de conteneur :`docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Récupérez l’ID de l’instance d’image du conteneur. Vous pouvez répertorier tous les conteneurs Docker avec la commande `docker ps –a`
1. Connectez-vous au portail Jenkins en procédant comme suit :

   1. Connectez-vous à un interpréteur de commandes Jenkins à partir de votre hôte. Utilisez les quatre premiers chiffres de l’ID de conteneur. Par exemple, si l’ID de conteneur est `2d24a73b5964`, utilisez `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. À partir de l’interpréteur de commandes Jenkins, obtenez le mot de passe administrateur de votre instance de conteneur :

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Pour vous connecter au tableau de bord Jenkins, ouvrez l’URL suivante dans un navigateur web : `http://<HOST-IP>:8080`. Utilisez le mot de passe de l’étape précédente pour déverrouiller Jenkins.
   1. (Facultatif.) Après votre première connexion, vous pouvez créer votre propre compte d’utilisateur et l’utiliser pour les étapes suivantes, ou vous pouvez continuer à utiliser le compte d’administrateur. Si vous créez un utilisateur, vous devez continuer avec celui-ci.
1. Configurez GitHub pour utiliser Jenkins en suivant les étapes dans [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Génération d’une clé SSH et ajout de celle-ci à l’agent SSH).
   * Suivez les instructions fournies par GitHub pour générer la clé SSH, puis l’ajouter au compte GitHub qui héberge le référentiel.
   * Exécutez les commandes mentionnées dans le lien ci-dessus dans le shell Jenkins Docker (et non sur l’hôte).
   * Pour vous connecter au shell Jenkins depuis votre hôte, utilisez la commande suivante :

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Assurez-vous que le cluster ou la machine hébergeant l’image de conteneur Jenkins possède une adresse IP publique. Cela permet à l’instance Jenkins de recevoir des notifications de GitHub.

Une fois que vous avez configuré Jenkins, passez à la section suivante, [Créer et configurer un travail Jenkins](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Créer et configurer un travail Jenkins

Les étapes de cette section vous montrent comment configurer un travail Jenkins pour répondre aux modifications apportées dans un référentiel GitHub, extraire les modifications et les générer. À la fin de cette section, vous êtes dirigé vers les dernières étapes pour configurer le travail pour déployer votre application selon que vous déployez dans un environnement de développement/test ou un environnement de production. 

1. Sur le tableau de bord Jenkins, cliquez sur **Nouvel élément**.
1. Entrez un nom d’élément (par exemple, **MyJob**). Sélectionnez **free-style project** (projet libre), puis cliquez sur **OK**.
1. La page de configuration du travail s’ouvre. (Pour accéder à la configuration à partir du tableau de bord Jenkins, cliquez sur le travail, puis cliquez sur **Configurer**).

1. Dans l’onglet **Général**, cochez la case **GitHub project** (Projet GitHub), puis spécifiez l’URL de votre projet GitHub. Cette URL héberge l’application Java Service Fabric que vous souhaitez intégrer au flux d’intégration continue et de déploiement continu Jenkins (CI/CD) (par exemple, `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Dans l’onglet **Source Code Management** (Gestion du code source), sélectionnez **Git**. Spécifiez l’URL du référentiel qui héberge l’application Java Service Fabric que vous souhaitez intégrer au flux Jenkins CI/CD (par exemple, `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Vous pouvez également spécifier la branche à générer (par exemple, `/master`).
1. Configurer votre référentiel *GitHub* pour communiquer avec Jenkins :

   a. Sur la page de votre référentiel GitHub, accédez à **Settings** > **Integrations and Services** (Paramètres -> Intégrations et services).

   b. Sélectionnez **Add Service** (Ajouter un service), tapez **Jenkins** et sélectionnez le **plug-in Jenkins-Github**.

   c. Entrez votre URL du webhook Jenkins (par défaut, il doit être `http://<PublicIPorFQDN>:8081/github-webhook/`). Cliquez sur **Add/Update service** (Ajouter/Mettre à jour le service).

   d. Un événement de test est envoyé à votre instance Jenkins. Vous devez voir une coche verte à côté du webhook dans GitHub et votre projet sera généré.

1. Dans l’onglet **Build Triggers** (Générer des déclencheurs) de Jenkins, sélectionnez l’option de génération souhaitée. Pour cet exemple, vous souhaitez déclencher une génération à chaque envoi (push au référentiel. Par conséquent, sélectionnez **GitHub hook trigger for GITScm polling**. (Cette option s’appelait auparavant **Build when a change is pushed to GitHub** (Générer lorsqu’une modification est envoyée à GitHub).)
1. Dans l’onglet **Build** (Générer), effectuez l’une des opérations suivantes selon que vous générez une application Java ou une application .NET Core :

   * **Pour les applications Java :** dans la liste déroulante **Add build step** (Ajouter une étape de génération), sélectionnez **Invoke Gradle Script** (Appeler le script Gradle). Cliquez sur **Avancé**. Dans le menu Avancé, spécifiez le chemin d’accès à **Root build script** (Script de génération racine) de votre application. Il récupère le Gradle de la génération à partir du chemin spécifié et fonctionne en conséquence. Pour l’[application ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), il s’agit de : `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Action de génération Jenkins de Service Fabric](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png)

   * **Pour les applications .NET Core :** dans la liste déroulante **Add build step** (Ajouter une étape de génération), sélectionnez **Execute Shell** (Exécuter le Shell). Dans la zone de commande qui apparaît, le répertoire doit d'abord être remplacé par le chemin d'accès au fichier build.sh. Une fois le répertoire modifié, le script build.sh peut être exécuté et va générer l’application.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     La capture d’écran suivante présente un exemple de commandes utilisées pour générer l’exemple [Counter Service](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) (Service de compteur) avec le nom de travail Jenkins CounterServiceApplication.

      ![Action de génération Jenkins de Service Fabric](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png)

1. Pour configurer Jenkins pour déployer votre application sur un cluster Service Fabric lors des actions post-génération, vous devez connaître l’emplacement du certificat de ce cluster dans votre conteneur Jenkins. Choisissez une des options suivantes selon que votre conteneur Jenkins est en cours d’exécution dans ou en dehors de votre cluster et notez l’emplacement du certificat de cluster :

   * **Jenkins exécuté dans votre cluster :** Pour trouver le chemin du certificat, vous pouvez faire écho à la variable d'environnement *Certificates_JenkinsOnSF_Code_MyCert_PEM* à partir du conteneur.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Jenkins exécuté en dehors de votre cluster :** procédez comme suit pour copier le certificat de cluster sur votre conteneur :
     1. Votre certificat doit être au format PEM. Si vous n’avez pas de fichier PEM, vous pouvez en créer à partir du fichier PFX de certificat. Si votre fichier PFX n’est pas protégé par un mot de passe, exécutez la commande suivante à partir de votre hôte :

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Si le fichier PFX est protégé par un mot de passe, incluez le mot de passe dans le paramètre `-passin`. Par exemple :

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Pour obtenir l’ID de votre conteneur Jenkins, exécutez `docker ps` à partir de votre hôte.
     1. Copiez le fichier PEM sur votre conteneur avec la commande Docker suivante :
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Vous avez presque terminé ! Gardez le travail Jenkins ouvert. La dernière tâche consiste à configurer les étapes post-génération pour déployer votre application sur votre cluster Service Fabric :

* Pour déployer sur un environnement de développement ou de test, suivez les étapes de [Configurer le déploiement à l’aide d’un point de terminaison de gestion de cluster](#configure-deployment-using-cluster-management-endpoint).
* Pour déployer sur un environnement de production, suivez les étapes de [Configurer le déploiement à l’aide des informations d’identification Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Configurer le déploiement à l’aide d’un point de terminaison de gestion de cluster

Pour les environnements de développement et de test, vous pouvez utiliser le point de terminaison de gestion de cluster pour déployer votre application. La configuration de l’action post-génération avec le point de terminaison de gestion de cluster pour déployer votre application implique le moins de configuration. Si vous déployez sur un environnement de production, passez directement à [Configurer le déploiement à l’aide des informations d’identification Azure](#configure-deployment-using-azure-credentials) pour configurer un principal du service Azure Active Directory à utiliser pendant le déploiement.    

1. Dans le travail Jenkins, cliquez sur l’onglet **Post-Build Actions** (Actions post-génération). 
1. Dans la liste déroulante **Post-Build Actions** (Actions post-génération), sélectionnez **Deploy Service Fabric Project** (Déployer le projet Service Fabric). 
1. Sous **Service Fabric Cluster Configuration** (Configuration du cluster Service Fabric), sélectionnez la case d’option **Fill the Service Fabric Management Endpoint** (Remplir le point de terminaison de gestion Service Fabric).
1. Pour **Management Host** (Hôte de gestion), entrez le point de terminaison de connexion pour votre cluster ; par exemple `{your-cluster}.eastus.cloudapp.azure.com`.
1. Pour **Client Key** (Clé client) et **Client Cert** (Certificat client), entrez l’emplacement du fichier PEM dans votre conteneur Jenkins ; par exemple `/var/jenkins_home/clustercert.pem`. (Vous avez copié l’emplacement du certificat dans la dernière étape de [Créer et configurer un travail Jenkins](#create-and-configure-a-jenkins-job).)
1. Sous **Configuration de l’application**, configurez les champs **Nom de l’application**, **Type d’application** et **Path to Application Manifest** (Chemin d’accès au manifeste d’application) (relatif).

   ![Action post-génération Service Fabric Jenkins, configurer le point de terminaison de gestion](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Cliquez sur **Verify configuration**. Lorsque la vérification réussit, cliquez sur **Enregistrer**. Le pipeline de votre travail Jenkins est maintenant entièrement configuré. Passez à [Étapes suivantes](#next-steps) pour tester votre déploiement.

## <a name="configure-deployment-using-azure-credentials"></a>Configurer le déploiement à l’aide des informations d’identification Azure

Pour les environnements de production, la configuration d’informations d’identification Azure pour déployer votre application est fortement recommandée. Cette section vous montre comment configurer un principal du service Azure Active Directory à utiliser pour déployer votre application lors de l’action post-génération. Vous pouvez affecter des principaux du service à des rôles dans votre annuaire afin de limiter les autorisations du travail Jenkins. 

Pour les environnements de développement et de test, vous pouvez configurer des informations d’identification Azure ou le point de terminaison de gestion de cluster pour déployer votre application. Pour plus d’informations sur la façon de configurer un point de terminaison de gestion de cluster, consultez [Configurer le déploiement à l’aide d’un point de terminaison de gestion de cluster](#configure-deployment-using-cluster-management-endpoint).   

1. Pour créer un principal du service Azure Active Directory et lui affecter des autorisations dans votre abonnement Azure, suivez les étapes de [Use the portal to create an Azure Active Directory application and service principal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) (Utiliser le portail pour créer une application et un principal du service Azure Active Directory). Prenez note des points suivants :

   * Suivez les étapes décrites dans la rubrique en veillant à copier et enregistrer les valeurs suivantes : *ID de l'application*, *Clé de l'application*, *ID du répertoire (ID du locataire)* et *ID de l'abonnement*. Vous en avez besoin pour configurer les informations d’identification Azure dans Jenkins.
   * Si vous ne disposez pas des [autorisations requises](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) sur votre annuaire, vous devez demander à un administrateur de vous accorder les autorisations ou de créer le principal du service pour vous, ou vous devez configurer le point de terminaison de gestion pour votre cluster dans les **actions post-génération** de votre projet dans Jenkins.
   * Dans la section [Create an Azure Active Directory application](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) (Créer une application Azure Active Directory), vous pouvez entrer n’importe quelle URL correcte pour l’**URL de connexion**.
   * Dans la section [Assign application to a Role](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) (Affecter un rôle à l’application), vous pouvez affecter à votre application le rôle *Lecteur* sur le groupe de ressources de votre cluster.

1. De retour dans le travail Jenkins, cliquez sur l’onglet **Post-Build Actions** (Actions post-génération).
1. Dans la liste déroulante **Post-Build Actions** (Actions post-génération), sélectionnez **Deploy Service Fabric Project** (Déployer le projet Service Fabric). 
1. Sous **Configuration du cluster Service Fabric**, cliquez sur **Sélectionner le cluster Service Fabric**. Cliquez sur **Ajouter** en regard de **Azure Credentials** (Informations d’identification Azure). Cliquez sur **Jenkins** pour sélectionner le fournisseur d’informations d’identification Jenkins.
1. Dans le fournisseur d’informations d’identification Jenkins, sélectionnez **Microsoft Azure Service Principal** (Principal du service Microsoft Azure) dans la liste déroulante **Kind** (Type).
1. Utilisez les valeurs que vous avez enregistrées lors de la configuration de votre principal du service à l’étape 1 pour définir les champs suivants :

   * **ID client** : *ID de l'application*
   * **Clé secrète client** : *Clé de l'application*
   * **ID du locataire** : *ID du répertoire*
   * **ID de l'abonnement** : *Identifiant d’abonnement*
1. Entrez un **ID** descriptif que vous utilisez pour sélectionner les informations d’identification Jenkins et une brève **Description**. Cliquez ensuite sur **Verify Service Principal** (Vérifier le principal du service). Si la vérification réussit, cliquez sur **Ajouter**.

   ![Service Fabric Jenkins, entrer les informations d’identification Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. Sous **Service Fabric Cluster Configuration** (Configuration du cluster Service Fabric), vérifiez que vos nouvelles informations d’identification sont sélectionnées pour **Azure Credentials** (Informations d’identification Azure). 
1. Dans la liste déroulante **Groupe de ressources**, sélectionnez le groupe de ressources du cluster sur lequel vous souhaitez déployer l’application.
1. Dans la liste déroulante **Service Fabric**, sélectionnez le cluster sur lequel vous souhaitez déployer l’application.
1. Pour **Client Key** (Clé client) et **Client Cert** (Certificat client), entrez l’emplacement du fichier PEM dans votre conteneur Jenkins. Par exemple, `/var/jenkins_home/clustercert.pem`. 
1. Sous **Configuration de l’application**, configurez les champs **Nom de l’application**, **Type d’application** et **Path to Application Manifest** (Chemin d’accès au manifeste d’application) (relatif).
    ![Action post-génération du Service Fabric Jenkins - Configurer les informations d’identification Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Cliquez sur **Verify configuration**. Lorsque la vérification réussit, cliquez sur **Enregistrer**. Le pipeline de votre travail Jenkins est maintenant entièrement configuré. Passez à [Étapes suivantes](#next-steps) pour tester votre déploiement.

## <a name="troubleshooting-the-jenkins-plugin"></a>Dépannage du plug-in Jenkins

Si vous constatez des bogues dans les plug-ins Jenkins, enregistrez un problème dans le [Jenkins JIRA](https://issues.jenkins-ci.org/) du composant en question.

## <a name="ideas-to-try"></a>Idées à essayer

GitHub et Jenkins sont maintenant configurés. Envisagez de modifier certains exemples du projet `reliable-services-actor-sample/Actors/ActorCounter` dans votre duplication du référentiel, https://github.com/Azure-Samples/service-fabric-java-getting-started. Envoyez vos modifications à la branche `master` distante (ou à n’importe quelle branche configurée pour être compatible). Cela déclenche le travail Jenkins, `MyJob`, que vous avez configuré. Il extrait les modifications de GitHub, les génère et déploie l’application sur le cluster spécifié dans les actions post-génération.  

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Jenkins sur Azure](/azure/Jenkins/)