---
title: Intégrer une application MongoDB existante avec l’API Azure Cosmos DB pour MongoDB et Open Service Broker pour Azure (OSBA)
description: Dans cet article, vous allez découvrir comment intégrer une application Java et MongoDB existante avec l’API Azure Cosmos DB pour MongoDB à l’aide d’Open Service Broker pour Azure (OSBA).
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, Open Service Broker pour Azure
ms.openlocfilehash: 3d0ab0b27d77e45d779227d30c5a8e4f824ba62a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277705"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Intégrer une application MongoDB existante avec l’API Azure Cosmos DB pour MongoDB et Open Service Broker pour Azure (OSBA)

Azure Cosmos DB est un service de base de données multimodèle distribué à l’échelle mondiale. Il fournit également la compatibilité de protocole filaire avec plusieurs API NoSQL, notamment pour MongoDB. L’API Cosmos DB pour MongoDB vous permet d’utiliser Cosmos DB avec votre application MongoDB existante sans avoir à changer l’implémentation ou les pilotes de base de données de votre application. Vous pouvez également provisionner un service Cosmos DB à l’aide d’Open Service Broker pour Azure.

Dans cet article, vous allez prendre une application Java qui utilise une base de données MongoDB et la mettre à jour de façon à utiliser une base de données Cosmos DB à l’aide d’Open Service Broker pour Azure.

## <a name="prerequisites"></a>Conditions préalables requises

Avant de continuer, vous devez avoir effectué les opérations suivantes :
    
* Avoir créé un [cluster Azure Kubernetes Service](kubernetes-walkthrough.md).
* Avoir [installé et configuré Open Service Broker pour Azure sur votre cluster AKS](integrate-azure.md). 
* Avoir installé et configuré l’[interface CLI du catalogue de services](https://svc-cat.io/docs/install/) pour exécuter des commandes `svcat`.
* Avoir une base de données [MongoDB](https://www.mongodb.com/). Par exemple, vous pourriez avoir MongoDB en cours d’exécution sur votre [machine de développement](https://docs.mongodb.com/manual/administration/install-community/) ou sur une [machine virtuelle Azure](../virtual-machines/linux/install-mongodb.md).
* Disposer d’un moyen de vous connecter à la base de données MongoDB et de l’interroger, par exemple avec l’[interpréteur de commandes mongo](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Obtenir le code d’application
    
Dans cet article, vous allez utiliser l’[exemple d’application musicale Spring de Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) pour illustrer une application qui utilise une base de données MongoDB.
    
Clonez l’application à partir de GitHub, puis accédez à son répertoire :
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Préparer l’application pour utiliser votre base de données MongoDB

L’exemple d’application musicale Spring offre de nombreuses options pour les sources de données. Dans cet article, vous allez la configurer pour utiliser une base de données MongoDB existante. 

Ajoutez le code YAML suivant à la fin de *src/main/resources/application.yml*. Cet ajout crée un profil nommé *mongodb* et configure un nom de base de données et un URI. Remplacez l’URI par les informations de connexion à votre base de données MongoDB existante. L’ajout de l’URI, qui contient un nom d’utilisateur et un mot de passe, directement à ce fichier est **réservé exclusivement au développement** et **ne doit jamais être ajouté au contrôle de version**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Quand vous démarrez votre application et lui indiquez d’utiliser le profil *mongodb*, elle se connecte à votre base de données MongoDB et l’utilise pour stocker les données de l’application.

Pour générer votre application :

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Démarrez votre application et faites en sorte qu’elle utilise le profil *mongodb* :

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Accédez à `http://localhost:8080` dans votre navigateur.

![Application musicale Spring avec des données par défaut](media/music-app.png)

Notez que l’application a été renseignée avec quelques [données par défaut](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Interagissez avec elle en supprimant quelques albums et en en créant quelques-uns.

Vous pouvez vérifier que votre application utilise votre base de données MongoDB en vous y connectant et en interrogeant la base de données *musicdb* :

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

L’exemple précédent utilise l’[interpréteur de commandes mongo](https://docs.mongodb.com/manual/mongo/) pour se connecter à la base de données MongoDB et l’interroger. Vous pouvez également vérifier que vos modifications sont conservées en arrêtant votre application, en la redémarrant puis en y accédant à nouveau dans votre navigateur. Remarquez que les modifications que vous avez apportées sont toujours là.


## <a name="create-a-cosmos-db-database"></a>Créer une base de données Cosmos DB

Pour créer une base de données Cosmos DB dans Azure à l’aide d’Open Service Broker, utilisez la commande `svcat provision` :

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

La commande précédente provisionne une base de données Cosmos DB dans Azure dans le groupe de ressources *MyResourceGroup* dans la région *eastus*. Pour plus d’informations sur les paramètres *resourceGroup*, *location* et d’autres paramètres JSON propres à Azure, consultez la [documentation de référence du module Cosmos DB](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Pour vérifier que votre base de données a terminé le provisionnement, utilisez la commande `svcat get instance` :

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Votre base de données est prête quand *État* indique *Prêt*.

Une fois que votre base de données a terminé le provisionnement, vous devez lier ses métadonnées à un [secret Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/). D’autres applications peuvent alors accéder à ces données une fois qu’elles ont été liées à un secret. Pour lier les métadonnées de votre base de données à un secret, utilisez la commande `svcat bind` :

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>Utiliser la base de données Cosmos DB avec votre application

Pour utiliser la base de données Cosmos DB avec votre application, vous devez connaître l’URI pour vous y connecter. Pour obtenir cette information, utilisez la commande `kubectl get secret` :

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

La commande précédente obtient le secret *musicdb* et affiche uniquement l’URI. Le secret étant stocké au format base64, la commande précédente le décode également.

À l’aide de l’URI de la base de données Cosmos DB, mettez à jour *src/main/resources/application.yml* pour l’utiliser :

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

La mise à jour de l’URI, qui contient un nom d’utilisateur et un mot de passe, directement à ce fichier est **réservée exclusivement au développement** et **ne doit jamais être ajoutée au contrôle de version**.

Regénérez et démarrez votre application pour commencer à utiliser la base de données Cosmos DB :

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Notez que votre application utilise toujours le profil *mongodb* et un URI qui commence par *mongodb://* pour se connecter à la base de données Cosmos DB. L’[API Azure Cosmos DB pour MongoDB](../cosmos-db/mongodb-introduction.md) fournit cette compatibilité. Elle permet à votre application de continuer à fonctionner comme si elle utilisait une base de données MongoDB, alors qu’en fait elle utilise Cosmos DB.

Accédez à `http://localhost:8080` dans votre navigateur. Notez que les données par défaut ont été restaurées. Interagissez avec elle en supprimant quelques albums et en en créant quelques-uns. Vous pouvez vérifier que vos modifications sont conservées en arrêtant votre application, en la redémarrant puis en y accédant à nouveau dans votre navigateur. Remarquez que les modifications que vous avez apportées sont toujours là. Les modifications sont conservées dans l’instance Cosmos DB que vous avez créée à l’aide d’Open Service Broker pour Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Exécuter votre application sur votre cluster AKS

Vous pouvez utiliser [Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) pour déployer l’application sur votre cluster AKS. Azure Dev Spaces vous aide à générer des artefacts, tels que des fichiers Dockerfile et charts Helm, et à déployer et exécuter une application dans AKS.

Pour activer Azure Dev Spaces dans votre cluster AKS :

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Utilisez les outils Azure Dev Spaces pour préparer votre application à s’exécuter dans AKS :

```cmd
azds prep --public
```

Cette commande génère plusieurs artefacts, notamment un dossier *charts/* , qui est votre graphique Helm, à la racine du projet. Cette commande ne peut pas générer de *Dockerfile* pour ce projet spécifique ; vous devez donc le créer.

Créez un fichier à la racine de votre projet nommé *Dockerfile* avec ce contenu :

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Vous devez mettre à jour la propriété *configurations.develop.build* dans *azds.yaml* en lui affectant la valeur *false* :
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Vous devez aussi mettre à jour l’attribut *containerPort* avec la valeur *8080* dans *charts/spring-music/templates/deployment.yaml* :

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

Pour déployer votre application sur AKS :

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

Accédez à l’URL affichée dans les journaux d’activité. Dans l’exemple précédent, vous utiliseriez *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* . 

Vérifiez que l’application est visible, avec vos modifications.

## <a name="next-steps"></a>Étapes suivantes

Cet article a décrit comment mettre à jour une application existante pour qu’elle utilise l’API Cosmos DB pour MongoDB à la place de MongoDB. Il a également montré comment provisionner un service Cosmos DB à l’aide d’Open Service Broker pour Azure et comment déployer cette application sur AKS avec Azure Dev Spaces.

Pour plus d’informations sur Cosmos DB, Open Service Broker pour Azure et Azure Dev Spaces, consultez :
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker pour Azure](https://osba.sh)
* [Développer avec Dev Spaces](../dev-spaces/azure-dev-spaces.md)
