---
title: Démarrage rapide – Lancer votre application Spring Cloud à partir du code source
description: Dans ce guide de démarrage rapide, vous découvrirez comment lancer votre application Azure Spring Cloud directement à partir de votre code source.
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: jeconnoc
ms.openlocfilehash: 6e18ac6c0fa97125b89ea5760a5d7da6c1545393
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708886"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>Démarrage rapide : Lancer votre application Spring Cloud à partir du code source

Azure Spring Cloud vous permet d’exécuter facilement sur Azure une application de microservices basée sur Spring Cloud.

Azure Spring Cloud vous permet de lancer votre application directement à partir de votre code source Java ou à partir d’un fichier JAR prédéfini. Cet article vous guide tout au long des étapes nécessaires.

Dans ce guide de démarrage rapide, vous découvrez comment :

> [!div class="checklist"]
> * Provisionner une instance de service
> * Définir un serveur de configuration pour une instance
> * Générer localement une application de microservices
> * Déployer chaque microservice
> * Affecter un point de terminaison public pour votre application

## <a name="prerequisites"></a>Prérequis

>[!Note]
> Azure Spring Cloud est actuellement disponible en préversion publique. Les offres en préversion publique permettent aux clients de tester les nouvelles fonctionnalités avant leur publication officielle.  Les fonctionnalités et services en préversion publique ne sont pas destinés à une utilisation en contexte de production.  Pour plus d’informations sur le support offert lors des préversions, consultez notre [FAQ](https://azure.microsoft.com/support/faq/) ou soumettez une [Demande de support](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

Avant de commencer, vérifiez que votre abonnement Azure a les dépendances requises :

1. [Installez Git](https://git-scm.com/)
2. [Installez JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Installez Maven 3.0 ou ultérieur](https://maven.apache.org/download.cgi)
4. [Installer l’interface de ligne de commande Microsoft Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Souscrire à un abonnement Azure](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article.  Les outils Azure les plus courants sont préinstallés, notamment les dernières versions de Git, JDK, Maven et Azure CLI. Si vous êtes connecté à votre abonnement Azure, lancez [Azure Cloud Shell](https://shell.azure.com) à partir de shell.azure.com.  Pour en savoir plus sur Azure Cloud Shell, consultez [notre documentation](../cloud-shell/overview.md).

## <a name="install-the-azure-cli-extension"></a>Installer l’extension Azure CLI

Installez l’extension Azure Spring Cloud pour Azure CLI avec la commande suivante.

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Provisionner une instance de service à l’aide d’Azure CLI

Connectez-vous à Azure CLI et choisissez votre abonnement actif. Veillez à choisir l’abonnement actif qui est dans la liste verte pour Azure Spring Cloud.

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Ouvrez une fenêtre Azure CLI et exécutez les commandes suivantes pour provisionner une instance d’Azure Spring Cloud. Notez que nous indiquons également à Azure Spring Cloud d’attribuer ici un domaine public.

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

Le déploiement de l’instance de service prend environ cinq minutes.

Définissez le nom du groupe de ressources et le nom du cluster par défaut en exécutant les commandes suivantes :

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-spring-cloud-application"></a>Créer l’application Spring Cloud

La commande suivante crée une application Spring Cloud dans votre abonnement.  Elle crée un service Spring Cloud vide sur lequel nous pouvons charger notre application.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Déployer votre application Spring Cloud

Vous pouvez déployer votre application à partir d’un fichier JAR prédéfini ou d’un référentiel Gradle ou Maven.  Vous trouverez ci-dessous des instructions pour chaque cas.

### <a name="deploy-a-built-jar"></a>Déployer un fichier JAR généré

Pour effectuer un déploiement à partir d’un fichier JAR généré sur votre ordinateur local, vérifiez que votre build produit un fichier [fat-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Pour déployer le fichier fat-JAR dans un déploiement actif :

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Pour déployer le fichier fat-JAR dans un déploiement spécifique :

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Déployer à partir de code source

Azure Spring Cloud utilise [kpack](https://github.com/pivotal/kpack) pour générer votre projet.  Vous pouvez utiliser Azure CLI pour charger votre code source, générer votre projet à l’aide de kpack, et le déployer dans l’application cible.

> [!WARNING]
> Le projet doit produire un seul fichier JAR avec une entrée `main-class` dans `MANIFEST.MF` dans `target` (pour les déploiements Maven) ou `build/libs` (pour les déploiements Gradle).  La présence de plusieurs fichiers JAR avec des entrées `main-class` entraînent l’échec du déploiement.

Pour les projets Maven/Gradle à un seul module :

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Pour les projets Maven/Gradle avec plusieurs modules, répétez cette opération pour chaque module :

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Afficher les journaux de déploiement

Passez en revue les journaux de génération kpack à l’aide de la commande suivante :

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> Les journaux kpack affichent uniquement le dernier déploiement si celui-ci a été généré à partir de la source à l’aide de kpack.

## <a name="assign-a-public-endpoint-to-gateway"></a>Affecter un point de terminaison public à la passerelle

1. Ouvrez la page **Tableau de bord de l’application**.
2. Sélectionnez l’application `gateway` pour afficher la page **Détails de l’application**.
3. Sélectionnez **Affecter un domaine** pour affecter un point de terminaison public à la passerelle. Cette opération peut prendre quelques minutes. 
4. Entrez l’adresse IP publique affectée dans votre navigateur pour voir votre application en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

> [!div class="checklist"]
> * Provisionner une instance de service
> * Définir un serveur de configuration pour une instance
> * Générer localement une application de microservices
> * Déployer chaque microservice
> * Modifier les variables d’environnement pour les applications
> * Affecter une adresse IP publique à votre passerelle d’application

> [!div class="nextstepaction"]
> [Préparer votre application Azure Spring Cloud pour le déploiement](spring-cloud-tutorial-prepare-app-deployment.md)
