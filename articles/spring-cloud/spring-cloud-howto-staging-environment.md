---
title: Configurer un environnement de préproduction dans Azure Spring Cloud | Microsoft Docs
description: Découvrir comment utiliser le déploiement bleu-vert avec Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 991a335207fc29cef7b243d7e520dd5f62ff691f
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226104"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configurer un environnement intermédiaire dans Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java

Cet article explique comment configurer un déploiement intermédiaire à l’aide du modèle de déploiement bleu-vert dans Azure Spring Cloud. Le déploiement bleu/vert est un modèle de livraison continue Azure DevOps qui consiste à garder en service une version existante (bleue) pendant le déploiement d’une nouvelle version (verte). Cet article vous montre également comment faire passer ce déploiement intermédiaire en production sans modifier directement le déploiement de production.

## <a name="prerequisites"></a>Prérequis

* Instance Azure Spring Cloud avec **niveau tarifaire** *Standard*.
* Une application en cours d’exécution.  Consultez [Démarrage rapide : Déployer votre première application Azure Spring Cloud](spring-cloud-quickstart.md).
* [Extension asc](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview) Azure CLI

Si vous voulez utiliser une autre application pour cet exemple, vous devez effectuer un changement simple dans une partie publique de l’application.  Ce changement permet de différencier votre déploiement de préproduction de la production.

>[!TIP]
> Azure Cloud Shell est un interpréteur de commandes interactif gratuit, qui vous permet d’exécuter les instructions de cet article.  Les outils Azure les plus courants sont préinstallés, notamment les dernières versions de Git, JDK, Maven et Azure CLI. Si vous êtes connecté à votre abonnement Azure, lancez votre [Azure Cloud Shell](https://shell.azure.com).  Pour en savoir plus, consultez [Vue d’ensemble d’Azure Cloud Shell](../cloud-shell/overview.md).

Pour configurer un environnement intermédiaire dans Azure Spring Cloud, suivez les instructions des sections suivantes.

## <a name="install-the-azure-cli-extension"></a>Installer l’extension Azure CLI

Installez l’extension Azure Spring Cloud pour Azure CLI à l’aide de la commande suivante :

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>Afficher les applications et les déploiements

Affichez les applications déployées à l’aide des procédures suivantes.

1. Accédez à votre instance Azure Spring Cloud dans le portail Azure.

1. Dans le volet de navigation gauche, ouvrez **Déploiements**.

    [ ![Deployment-deprecate](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. Ouvrez le panneau « Applications » pour afficher les applications de votre instance de service.

    [ ![Applications - Tableau de bord](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Vous pouvez cliquer sur une application et afficher les détails.

    [ ![Applications - Vue d’ensemble](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Ouvrez le panneau **Déploiements** pour voir tous les déploiements de l’application. La grille de déploiement indique si le déploiement est en production ou en préproduction.

    [ ![Tableau de bord des déploiements](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Vous pouvez cliquer sur le nom du déploiement pour afficher sa vue d’ensemble. En l’occurrence, l’unique déploiement est nommé *Par défaut*.

    [ ![Vue d’ensemble des déploiements](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>Créer un déploiement intermédiaire

1. Dans votre environnement de développement local, apportez une petite modification à votre application. Vous pourrez ainsi différencier facilement les deux déploiements. Exécutez la commande suivante pour générer le package jar : 

    ```console
    mvn clean package -DskipTests
    ```

1. Créez un déploiement avec Azure CLI et donnez-lui le nom de déploiement intermédiaire « green ».

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app <appName> -n green --jar-path gateway/target/gateway.jar
    ```

1. Une fois le déploiement CLI terminé, accédez à la page de l’application à partir du **Tableau de bord de l’application** et examinez toutes vos instances sous l’onglet **Déploiements** à gauche.

   [ ![Tableau de bord des déploiements après le déploiement de « green »](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> L’état de découverte est *OUT_OF_SERVICE* pour que le trafic ne soit pas acheminé vers ce déploiement avant la fin de la vérification.

## <a name="verify-the-staging-deployment"></a>Vérifier le déploiement de préproduction

Pour vérifier que le développement en préproduction « green » fonctionne :
1. Accédez à **Déploiements**, puis cliquez sur le **déploiement en préproduction** `green`.
1. Dans la page **Vue d’ensemble**, cliquez sur le **Point de terminaison de test**.
1. Cette action ouvre la build en préproduction, qui affiche vos modifications.

>[!TIP]
> * Vérifiez que votre point de terminaison de test se termine par une barre oblique (/) pour garantir le bon chargement du fichier CSS.  
> * Si votre navigateur vous demande d’entrer des informations d’identification de connexion pour afficher la page, utilisez [URL Decode](https://www.urldecoder.org/) pour décoder votre point de terminaison de test. URL Decode retourne une URL au format « https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green ».  Utilisez-le pour accéder à votre point de terminaison.

>[!NOTE]    
> Les paramètres du serveur de configuration s’appliquent à votre environnement intermédiaire et à la production. Par exemple, si vous définissez *somepath* comme chemin du contexte (`server.servlet.context-path`) de votre passerelle d’application dans le serveur de configuration, le chemin de votre déploiement vert devient : « https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/... ».
 
 Si vous visitez votre passerelle d’application publique à ce stade, vous devriez voir l’ancienne page sans votre nouveau changement.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Définir le déploiement vert comme environnement de production

1. Après avoir vérifié votre changement dans votre environnement intermédiaire, vous pouvez l’envoyer (push) en production. Revenez à **Gestion du déploiement**, puis sélectionnez l’application qui se trouve en `Production`.

1. Cliquez sur les points de suspension après l’**État de l’inscription** et définissez la build de production sur `staging`.

   [ ![Déploiements - Définition du déploiement en préproduction](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Revenez à la page **Gestion du déploiement**. Définissez le déploiement `green` sur `production`. Une fois le paramétrage terminé, l’état de votre déploiement `green` doit afficher *En service*. Il s’agit désormais de la build de production en cours d’exécution.

   [ ![Déploiements - Résultat de la définition du déploiement en préproduction](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. L’URL de l’application doit afficher vos modifications.

>[!NOTE]
> Une fois le déploiement vert défini comme environnement de production, le déploiement précédent devient le déploiement intermédiaire.

## <a name="modify-the-staging-deployment"></a>Modifier le déploiement de préproduction

Si vous n’êtes pas satisfait de votre changement, vous pouvez modifier le code de votre application, générer un nouveau package jar et le charger dans votre déploiement Vert à l’aide d’Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Supprimer le déploiement intermédiaire

Pour supprimer votre déploiement intermédiaire du port Azure, accédez à la page de votre déploiement intermédiaire et sélectionnez le bouton **Supprimer**.

Vous pouvez également supprimer votre déploiement intermédiaire à partir d’Azure CLI en exécutant la commande suivante :

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Étapes suivantes

* [CI/CD pour Azure Spring Cloud](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)
