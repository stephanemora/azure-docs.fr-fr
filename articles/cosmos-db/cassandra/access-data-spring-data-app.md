---
title: Comment utiliser l’API Apache Cassandra de Spring Data avec Azure Cosmos DB
description: Découvrez comment utiliser l’API Apache Cassandra de Spring Data avec Azure Cosmos DB.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: how-to
ms.date: 07/17/2021
ms.openlocfilehash: 92f9045891aa56589e13d887dfea6c3157292185
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532632"
---
# <a name="how-to-use-spring-data-apache-cassandra-api-with-azure-cosmos-db"></a>Comment utiliser l’API Apache Cassandra de Spring Data avec Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Cet article illustre la création d’un exemple d’application qui utilise [Spring Data] pour stocker et récupérer des informations à l’aide de l’[API Cassandra d’Azure Cosmos DB](/azure/cosmos-db/cassandra-introduction).

## <a name="prerequisites"></a>Prérequis

Pour réaliser les étapes décrites dans cet article, vous devez disposer des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas déjà un abonnement Azure, vous pouvez activer vos [avantages d’abonné MSDN] ou vous inscrire pour un [compte Azure gratuit].
* Un kit de développement Java (JDK) pris en charge. Pour plus d’informations sur les JDK disponibles pour le développement sur Azure, consultez [Prise en charge de Java sur Azure et Azure Stack](/azure/developer/java/fundamentals/java-support-on-azure).
* [Apache Maven](http://maven.apache.org/), version 3.0 ou ultérieure.
* [Curl](https://curl.haxx.se/) ou l’utilitaire HTTP similaire pour tester la fonctionnalité.
* Un client [Git](https://git-scm.com/downloads).

> [!NOTE]
> Les exemples mentionnés ci-dessous implémentent des extensions personnalisées pour une meilleure expérience lors de l’utilisation de l’API Cassandra Azure Cosmos DB. Ils incluent des stratégies personnalisées de nouvelle tentative et d’équilibrage de charge, ainsi que l’implémentation de paramètres de connexion recommandés. Pour obtenir des informations complètes sur l’utilisation des stratégies personnalisées, consultez les exemples Java pour la [version 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample) et la [version 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample-v4). 

## <a name="create-a-cosmos-db-cassandra-api-account"></a>Créer un compte d’API Cassandra Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="configure-the-sample-application"></a>Configurer l’exemple d’application

La procédure suivante permet de configurer l’application de test.

1. Ouvrez un interpréteur de commandes et clonez l’un des exemples suivants :

   Pour Java, [pilote version 3](https://github.com/datastax/java-driver/tree/3.x), et la version Spring correspondante :

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure-extension-v3.git
   ```
   
   Pour Java, [pilote version 4](https://github.com/datastax/java-driver/tree/4.x), et la version Spring correspondante :

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure-extension-v4.git
   ```     

    > [!NOTE]    
    > Bien que l’utilisation décrite ci-dessous soit identique pour les exemples Java version 3 et version 4 ci-dessus, la façon selon laquelle ils ont été implémentés pour inclure des stratégies personnalisées de nouvelle tentative et d’équilibrage de charge est différente. Nous vous recommandons de consulter le code pour comprendre comment implémenter des stratégies personnalisées si vous apportez des modifications à une application Java Spring existante.  

1. Recherchez le fichier *application.properties* dans le répertoire *resources* de l’exemple de projet, ou créez le fichier s’il n’existe pas.

1. Ouvrez le fichier *application.properties* dans un éditeur de texte, ajoutez ou configurez les lignes suivantes dans le fichier et remplacez les exemples de valeurs par les valeurs appropriées mentionnées précédemment :

   ```yaml
   spring.data.cassandra.contact-points=<Account Name>.cassandra.cosmos.azure.com
   spring.data.cassandra.port=10350
   spring.data.cassandra.username=<Account Name>
   spring.data.cassandra.password=********
   ```

   Où :

   | Paramètre | Description |
   |---|---|
   | `spring.data.cassandra.contact-points` | Spécifie le **point de contact** mentionné plus haut dans cet article. |
   | `spring.data.cassandra.port` | Spécifie le **port** mentionné plus haut dans cet article. |
   | `spring.data.cassandra.username` | Spécifie le **nom d’utilisateur** mentionné plus haut dans cet article. |
   | `spring.data.cassandra.password` | Spécifie le **mot de passe principal** mentionné plus haut dans cet article. |

1. Enregistrez et fermez le fichier *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Packager et tester l’exemple d’application 

Accédez au répertoire contenant le fichier .pom pour générer et tester l’application.

1. Compilez l’exemple d’application avec Maven :

   ```shell
   mvn clean package
   ```

1. Démarrez l’exemple d’application :

   ```shell
   java -jar target/spring-data-cassandra-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. Créez des enregistrements à l’aide de `curl` à partir d’une invite de commandes comme dans les exemples suivants :

   ```shell
   curl -s -d "{\"name\":\"dog\",\"species\":\"canine\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d "{\"name\":\"cat\",\"species\":\"feline\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   Votre application doit renvoyer des valeurs comme suit :

   ```shell
   Added Pet{id=60fa8cb0-0423-11e9-9a70-39311962166b, name='dog', species='canine'}.

   Added Pet{id=72c1c9e0-0423-11e9-9a70-39311962166b, name='cat', species='feline'}.
   ```

1. Récupérez tous les enregistrements existants à l’aide de `curl` à partir d’une invite de commandes comme dans les exemples suivants :

   ```shell
   curl -s http://localhost:8080/pets
   ```

   Votre application doit renvoyer des valeurs comme suit :

   ```json
   [{"id":"60fa8cb0-0423-11e9-9a70-39311962166b","name":"dog","species":"canine"},{"id":"72c1c9e0-0423-11e9-9a70-39311962166b","name":"cat","species":"feline"}]
   ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Spring et Azure, poursuivez vers le centre de documentation Spring sur Azure.

> [!div class="nextstepaction"]
> [Spring sur Azure](../../index.yml)

### <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations sur l’utilisation d’Azure avec Java, renseignez-vous sur [Azure pour les développeurs Java] et l’[utilisation d’Azure DevOps et Java].

<!-- URL List -->

[Azure pour les développeurs Java]: ../index.yml
[compte Azure gratuit]: https://azure.microsoft.com/pricing/free-trial/
[Utilisation d’Azure DevOps et Java]: /azure/devops/
[avantages d’abonné MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/access-data-spring-data-app/create-cosmos-db-01.png
[COSMOSDB02]: media/access-data-spring-data-app/create-cosmos-db-02.png
[COSMOSDB03]: media/access-data-spring-data-app/create-cosmos-db-03.png
[COSMOSDB04]: media/access-data-spring-data-app/create-cosmos-db-04.png
[COSMOSDB05]: media/access-data-spring-data-app/create-cosmos-db-05.png
[COSMOSDB05-1]: media/access-data-spring-data-app/create-cosmos-db-05-1.png
[COSMOSDB06]: media/access-data-spring-data-app/create-cosmos-db-06.png