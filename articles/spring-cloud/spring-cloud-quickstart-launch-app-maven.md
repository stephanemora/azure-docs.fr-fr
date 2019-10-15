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
ms.openlocfilehash: 01140e94e8d0cc47570824970801bdd0043324d7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166511"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Démarrage rapide : Lancer une application Azure Spring Cloud à l’aide du plug-in Maven

Le plug-in Maven d’Azure Spring Cloud vous permet de créer et de mettre à jour facilement des applications dans votre service Azure Spring Cloud. En prédéfinissant une configuration, vous pouvez déployer des applications sur votre service Azure Spring Cloud existant. Dans cet article, nous utilisons un exemple d’application appelé PiggyMetrics pour illustrer cette fonctionnalité.

>[!Note]
> Avant de commencer ce guide de démarrage rapide, vérifiez que votre abonnement Azure a accès à Azure Spring Cloud.  Comme il s’agit d’un service en préversion, nous demandons aux clients de nous contacter pour que nous puissions ajouter leur abonnement à notre liste verte.  Si vous souhaitez explorer les fonctionnalités d’Azure Spring Cloud, [remplissez ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
).

>[!TIP]
> Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article.  Les outils Azure les plus courants sont préinstallés, notamment les dernières versions de Git, JDK, Maven et Azure CLI. Si vous êtes connecté à votre abonnement Azure, lancez [Azure Cloud Shell](https://shell.azure.com) à partir de shell.azure.com.  Pour en savoir plus, [lisez notre documentation sur Azure Cloud Shell](../cloud-shell/overview.md).

Pour suivre ce guide de démarrage rapide :

1. [Installez Git](https://git-scm.com/)
2. [Installer JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Installer Maven 3.0 ou ultérieur](https://maven.apache.org/download.cgi)
4. [Installer l’interface de ligne de commande Microsoft Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Souscrire un abonnement Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Installer l’extension Azure CLI

Installer l’extension Azure Spring Cloud pour Azure CLI avec la commande suivante

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Provisionner une instance de service sur le portail Azure

1. Dans un navigateur web, ouvrez le [Portail Azure](https://portal.azure.com) et connectez-vous à votre compte.

1. Recherchez **Azure Spring Cloud** et sélectionnez-le pour accéder à la page de vue d’ensemble. Sélectionnez le bouton **Créer** pour commencer.

1. Remplissez le formulaire en tenant compte des points suivants :
    - Nom du service : spécifiez le nom de votre instance de service.  Les noms doivent comporter entre 4 et 32 caractères, et contenir uniquement des lettres minuscules, des chiffres et des traits d’union.  Le premier caractère du nom du service doit être une lettre, et le dernier doit être une lettre ou un chiffre.
    - Abonnement : sélectionnez l’abonnement à facturer pour cette ressource.  Vérifiez que cet abonnement a été ajouté à notre liste verte pour Azure Spring Cloud.
    - Groupe de ressources : une bonne pratique consiste à créer des groupes de ressources pour les nouvelles ressources.
    - Localisation : sélectionnez l’emplacement de votre instance de service. Parmi les emplacements actuellement pris en charge, citons USA Est, USA Ouest 2, Europe Ouest et Asie Sud-Est.
    
Le déploiement du service nécessite environ 5 minutes.  Une fois le déploiement terminé, la page **Vue d’ensemble** de l’instance de service apparaît.

## <a name="set-up-your-configuration-server"></a>Configurer votre serveur de configuration

1. Accédez à la page **Vue d’ensemble** du service, puis sélectionnez **Config Server**.
1. Dans la section **Dépôt par défaut**, affectez https://github.com/Azure-Samples/piggymetrics à **URI**, affectez « config » à **LABEL**, puis sélectionnez **Appliquer** pour enregistrer vos changements.

## <a name="clone-and-build-the-sample-application-repository"></a>Cloner et générer le dépôt d’exemples d’applications

1. Exécutez la commande suivante pour cloner le dépôt git.

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Exécutez la commande suivante pour changer de répertoire et générer le projet.

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Générer et déployer la configuration Azure Spring Cloud

1. Ajoutez ce qui suit à votre fichier `pom.xml` ou `settings.xml` pour permettre à Maven de fonctionner avec Azure Spring Cloud.

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

1. Exécutez la commande suivante pour générer une configuration.

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    1. Sélectionnez les modules `gateway`, `auth-service` et `account-service`.

    1. Sélectionnez votre abonnement et le cluster de services Azure Spring Cloud.

    1. Dans la liste des projets fournis, entrez le nombre qui correspond à `gateway` pour lui accorder un accès public.
    
    1. Confirmez la configuration.

1. Déployez les applications à l’aide de la commande suivante :

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. Vous pouvez accéder à PiggyMetrics à l’aide de l’URL fournie dans la sortie de la commande précédente.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une application Spring Cloud à partir d’un dépôt Maven.  Pour en savoir plus sur Azure Spring Cloud, passez au tutoriel sur la préparation de votre application pour le déploiement.

> [!div class="nextstepaction"]
> [Préparer votre application Azure Spring Cloud pour le déploiement](spring-cloud-tutorial-prepare-app-deployment.md)
