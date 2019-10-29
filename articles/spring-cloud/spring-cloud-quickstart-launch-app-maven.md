---
title: 'Démarrage rapide : Lancer une application à l’aide de Maven - Azure Spring Cloud'
description: Lancer un exemple d’application à l’aide de Maven
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: e773b997cca3fa9a1f11fec2ac449e1fc11c5364
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554561"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>Démarrage rapide : Lancer une application Azure Spring Cloud à l’aide du plug-in Maven

Le plug-in Maven d’Azure Spring Cloud vous permet de créer et de mettre à jour facilement les applications de votre service Azure Spring Cloud. En prédéfinissant une configuration, vous pouvez déployer des applications sur votre service Azure Spring Cloud existant. Dans cet article, vous utilisez un exemple d’application appelé PiggyMetrics pour illustrer cette fonctionnalité.

>[!Note]
> Avant de commencer ce guide de démarrage rapide, vérifiez que votre abonnement Azure a accès à Azure Spring Cloud. Dans la mesure où il s’agit d’un service en préversion, nous vous invitons à nous contacter pour que nous puissions ajouter votre abonnement à notre liste verte. Si vous souhaitez découvrir les fonctionnalités d’Azure Spring Cloud, remplissez et envoyez le [formulaire de demande d’informations sur Azure Spring Cloud (préversion privée)](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u).

>[!TIP]
> Azure Cloud Shell est un interpréteur de commandes interactif gratuit, qui vous permet d’exécuter les commandes de cet article. Il comporte une préinstallation des outils Azure usuels, notamment les dernières versions de Git, du JDK (Java Development Kit), de Maven et d’Azure CLI. Si vous êtes connecté à votre abonnement Azure, lancez [Azure Cloud Shell](https://shell.azure.com). Pour plus d’informations, voir la [présentation d’Azure Cloud Shell](../cloud-shell/overview.md).

Pour suivre ce guide de démarrage rapide :

1. [Installer Git](https://git-scm.com/).
2. [Installez le kit JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Installez Maven 3.0 ou ultérieur](https://maven.apache.org/download.cgi).
4. [Installer l’interface de ligne de commande Microsoft Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
5. [Inscrivez-vous pour un abonnement gratuit à Azure](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>Installer l’extension Azure CLI

Installez l’extension Azure Spring Cloud pour Azure CLI à l’aide de la commande suivante :

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Provisionner une instance de service sur le portail Azure

1. Dans un navigateur web, ouvrez le [portail Azure](https://portal.azure.com), puis connectez-vous à votre compte.

1. Recherchez, puis sélectionnez **Azure Spring Cloud**. 
1. Dans la page Vue d’ensemble, sélectionnez **Créer**, puis effectuez ce qui suit :  

    a. Dans la zone **Nom du service**, spécifiez le nom de votre instance de service. Le nom doit comporter entre 4 et 32 caractères. Il peut contenir uniquement des lettres minuscules, des chiffres et des traits d’union. Le premier caractère du nom de service doit être une lettre, et le dernier doit être une lettre ou un chiffre.  

    b. Dans la liste déroulante **Abonnement**, sélectionnez l’abonnement à facturer pour cette ressource. Vérifiez que cet abonnement a été ajouté à notre liste verte pour Azure Spring Cloud.  

    c. Dans la zone **Groupe de ressources**, créez un groupe de ressources. La création de groupes de ressources pour de nouvelles ressources est une bonne pratique.  

    d. Dans la liste déroulante **Emplacement**, sélectionnez l’emplacement de votre instance de service. Les emplacements actuellement pris en charge sont USA Est, USA Ouest 2, Europe Ouest et Asie Sud-Est.
    
Le déploiement du service prend environ 5 minutes. Une fois le service déployé, la page **Vue d’ensemble** de l’instance de service s’affiche.

## <a name="set-up-your-configuration-server"></a>Configurer votre serveur de configuration

1. Dans la page **Vue d’ensemble** du service, sélectionnez **Serveur de configuration**.
1. Dans la section **Dépôt par défaut**, affectez à **URI** la valeur **https://github.com/Azure-Samples/piggymetrics** , affectez à **Étiquette** la valeur **config**, puis sélectionnez **Appliquer** pour enregistrer les changements apportés.

## <a name="clone-and-build-the-sample-application-repository"></a>Cloner et générer le dépôt d’exemples d’applications

1. Clonez le dépôt Git en exécutant la commande suivante :

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Changez d’annuaire, puis générez le projet en exécutant la commande suivante :

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Générer et déployer la configuration Azure Spring Cloud

1. Pour que Maven fonctionne avec Azure Spring Cloud, ajoutez le code suivant à votre fichier *pom.xml* ou *settings.xml*.

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. Générez une configuration en exécutant la commande suivante :

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. Sélectionnez les modules `gateway`, `auth-service` et `account-service`.

    b. Sélectionnez votre abonnement et le cluster de services Azure Spring Cloud.

    c. Dans la liste des projets fournis, entrez le numéro qui correspond à `gateway` pour lui donner un accès public.
    
    d. Confirmez la configuration.

1. Déployez les applications à l’aide de la commande suivante :

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. Vous pouvez accéder à PiggyMetrics à l’aide de l’URL fournie dans la sortie de la commande précédente.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une application Spring Cloud à partir d’un dépôt Maven. Pour en savoir plus sur Azure Spring Cloud, passez au tutoriel sur la préparation de votre application au déploiement.

> [!div class="nextstepaction"]
> [Préparer votre application Azure Spring Cloud pour le déploiement](spring-cloud-tutorial-prepare-app-deployment.md)
