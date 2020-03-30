---
title: Configurer un cluster Linux Azure Service Fabric sur Windows
description: Cet article explique comment configurer des clusters Linux Service Fabric exécutés sur des machines de développement Windows. Cette méthode est particulièrement utile pour le développement multiplateforme.
author: suhuruli
ms.topic: conceptual
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 806e77a928d25e30aed24147525f74507bc32795
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462980"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Configurer un cluster Linux Service Fabric sur votre machine de développement Windows

Ce document explique comment configurer une infrastructure Linux Service Fabric locale sur des machines de développement Windows. La configuration d'un cluster Linux local est utile pour tester rapidement les applications ciblées pour les clusters Linux mais développées sur une machine Windows.

## <a name="prerequisites"></a>Conditions préalables requises
Les clusters Service Fabric basés sur Linux ne s'exécutent pas nativement sous Windows. Pour exécuter un cluster Service Fabric local, une image de conteneur Docker préconfigurée est fournie. Avant de commencer, vous avez besoin des éléments suivants :

* Au moins 4 Go de RAM
* Dernière version de [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker doit être en cours d’exécution dans le mode Linux

>[!TIP]
> * Vous pouvez suivre les étapes indiquées dans la [documentation](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) officielle de Docker pour installer Docker sur Windows. 
> * Une fois l’installation terminée, suivez les étapes indiquées [ici](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) pour vérifier que l’installation s’est déroulée correctement.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Créer un conteneur local et configurer Service Fabric
Pour configurer un conteneur Docker local et y exécuter un cluster Service Fabric, effectuez les opérations suivantes dans PowerShell :


1. Mettez à jour la configuration du démon Docker sur votre ordinateur hôte avec les informations suivantes, puis redémarrez le démon Docker : 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    La méthode conseillée pour la mise à jour est la suivante : accédez à l’icône Docker > Réglages > Démon > Avancé et effectuez la mise à jour. Redémarrez ensuite le démon Docker pour que les modifications prennent effet. 

2. Dans le nouveau répertoire, créez un fichier nommé `Dockerfile` pour créer votre image Service Fabric :

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >Vous pouvez adapter ce fichier pour ajouter des programmes supplémentaires ou des dépendances dans votre conteneur.
    >Par exemple, l’ajout de `RUN apt-get install nodejs -y` permet de prendre en charge les applications `nodejs` comme exécutables invités.
    
    >[!TIP]
    > Par défaut, cela extraira l’image avec la dernière version de Service Fabric. Pour des révisions particulières, visitez la page [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

3. Pour créer votre image réutilisable à partir de `Dockerfile`, ouvrez un terminal et `cd` vers le répertoire contenant votre `Dockerfile`, puis exécutez :

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Cette opération prend un certain temps, mais ne doit être effectuée qu’une seule fois.

4. Vous pouvez maintenant démarrer rapidement une copie locale de Service Fabric, chaque fois que nécessaire, en exécutant :

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Donnez un nom à votre instance de conteneur de sorte qu’elle puisse être gérée plus facilement. 
    >
    >Si votre application écoute sur certains ports, ceux-ci doivent être spécifiés à l’aide de balises `-p` supplémentaires. Par exemple, si votre application écoute sur le port 8080, ajoutez la balise `-p` suivante :
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. Le démarrage du cluster prend un certain temps. Vous pouvez afficher les journaux d’activité à l’aide de la commande suivante ou accéder au tableau de bord pour afficher l’intégrité des clusters [http://localhost:19080](http://localhost:19080) :

    ```powershell 
    docker logs sftestcluster
    ```

6. Une fois l’étape 5 terminée, vous pouvez accéder à ``http://localhost:19080`` à partir de votre machine Windows. Vous devriez alors voir l’Explorateur Service Fabric. À ce stade, vous pouvez vous connecter à ce cluster à l'aide des outils de votre machine de développement Windows et déployer une application ciblée pour les clusters Linux Service Fabric. 

    > [!NOTE]
    > Le plug-in Eclipse n’est actuellement pas pris en charge sous Windows. 

7. Une fois terminé, arrêtez puis nettoyez le conteneur avec cette commande :

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limites connues 
 
 Voici les limitations connues d’un cluster local qui s’exécute dans un conteneur sous Mac : 
 
 * Le service DNS ne s’exécute pas et n’est pas pris en charge [Problème n° 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Étapes suivantes
* Prise en main d'[Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Consulter d’autres [exemples Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
