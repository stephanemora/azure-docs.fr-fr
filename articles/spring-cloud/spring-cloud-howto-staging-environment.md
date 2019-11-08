---
title: Configurer un environnement de préproduction dans Azure Spring Cloud | Microsoft Docs
description: Découvrir comment utiliser le déploiement bleu-vert avec Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 24ce4dee04e4daf3eaee4144f8dc56de5867bbca
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607206"
---
# <a name="how-to-set-up-a-staging-environment"></a>Guide pratique pour configurer un environnement de préproduction

Cet article vous montre comment tirer parti d’un déploiement de préproduction à l’aide du modèle de déploiement bleu-vert dans Azure Spring Cloud. Il montre également comment faire passer ce déploiement de préproduction en production sans modifier directement le déploiement de production.

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous avez déjà déployé l’application PiggyMetrics à partir de notre [tutoriel sur le lancement d’une application](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics comprend trois applications : « Gateway », « account-service » et « auth-service ».  

Si vous souhaitez utiliser une autre application pour cet exemple, vous devez effectuer un changement simple dans une partie publique de l’application.  Ce changement permet de différencier votre déploiement de préproduction de la production.

>[!TIP]
> Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article.  Les outils Azure les plus courants sont préinstallés, notamment les dernières versions de Git, JDK, Maven et Azure CLI. Si vous êtes connecté à votre abonnement Azure, lancez [Azure Cloud Shell](https://shell.azure.com) à partir de shell.azure.com.  Pour en savoir plus, [lisez notre documentation sur Azure Cloud Shell](../cloud-shell/overview.md).

Pour effectuer ce qui est décrit dans cet article :


## <a name="install-the-azure-cli-extension"></a>Installer l’extension Azure CLI

Installer l’extension Azure Spring Cloud pour Azure CLI avec la commande suivante

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Afficher tous les déploiements

Accédez à votre instance de service dans le portail Azure et sélectionnez **Gestion du déploiement** pour afficher tous les déploiements. Vous pouvez sélectionner chaque déploiement pour obtenir des détails supplémentaires.

## <a name="create-a-staging-deployment"></a>Créer un déploiement intermédiaire

1. Dans votre environnement de développement local, apportez une petite modification à l’application passerelle de Piggy Metric. Par exemple, changez la couleur dans `gateway/src/main/resources/static/css/launch.css`. Vous pourrez ainsi différencier facilement les deux déploiements. Exécutez la commande suivante pour générer le package jar : 

    ```azurecli
    mvn clean package
    ```

1. Créez un déploiement avec Azure CLI et donnez-lui le nom de déploiement de préproduction « green ».

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Une fois le déploiement terminé, accédez à la page de la passerelle à partir du **Tableau de bord de l’application** et examinez toutes vos instances sous l’onglet **Instances d’application** à gauche.
  
> [!NOTE]
> L’état de découverte est « OUT_OF_SERVICE » pour que le trafic ne soit pas acheminé vers ce déploiement avant la fin de la vérification.

## <a name="verify-the-staging-deployment"></a>Vérifier le déploiement de préproduction

1. Revenez à la page **Gestion du déploiement** et sélectionnez votre nouveau déploiement. L’état du déploiement doit indiquer **En cours d’exécution**. Le bouton « Affecter un domaine/Annuler l’affectation d’un domaine » est désactivé puisqu’il s’agit d’un environnement de préproduction.

1. Dans la page **Vue d’ensemble**, vous devez voir un **Point de terminaison de test**. Copiez-le et collez-le dans une nouvelle page de navigateur. La nouvelle page Piggy Metrics doit s’afficher.

>[!TIP]
> * Vérifiez que votre point de terminaison de test se termine par « / » pour garantir le bon chargement de CSS.  
> * Si votre navigateur vous demande d’entrer des informations d’identification de connexion pour afficher la page, utilisez [URL Decode](https://www.urldecoder.org/) pour décoder votre point de terminaison de test. URL Decode retourne une URL au format « https://\<nom_utilisateur>:\<mot_de_passe>@\<nom_cluster>.test.azureapps.io/gateway/green ».  Utilisez-la pour accéder à votre point de terminaison.

>[!NOTE]    
> Les paramètres du serveur de configuration s’appliquent à votre environnement de préproduction et à la production. Par exemple, si vous définissez *somepath* comme chemin du contexte (`server.servlet.context-path`) de votre passerelle d’application dans le serveur de configuration, le chemin à votre déploiement vert devient alors : « https://\<nom_utilisateur>:\<mot_de_passe>@\<nom_cluster>.test.azureapps.io/gateway/green/somepath/... ».
 
 Si vous visitez votre passerelle d’application publique à ce stade, vous devriez voir l’ancienne page sans votre nouveau changement.
    
## <a name="set-the-green-as-production-deployment"></a>Définir le déploiement vert comme déploiement de production

1. Après avoir vérifié votre changement dans votre environnement de préproduction, vous pouvez l’envoyer (push) en production. Revenez à **Gestion du déploiement** et cochez la case avant l’application « gateway ».
2. Sélectionnez « Définir le déploiement ».
3. Sélectionnez « Vert » dans le menu « DÉPLOIEMENT DE PRODUCTION », puis **Appliquer**
4. Accédez à la page **Vue d’ensemble** de votre application de passerelle. Si vous avez déjà attribué un domaine à votre application de passerelle, l’URL apparaît dans la page **Vue d’ensemble**. Accédez à l’URL pour voir la page Piggy Metrics modifiée.

>[!NOTE]
> Une fois le déploiement vert défini comme environnement de production, le déploiement précédent devient le déploiement de préproduction.

## <a name="modify-the-staging-deployment"></a>Modifier le déploiement de préproduction

Si vous n’êtes pas satisfait de votre changement, vous pouvez modifier le code de votre application, générer un nouveau package jar et le charger dans votre déploiement Green à l’aide d’Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>Supprimer un déploiement de préproduction

Pour supprimer votre déploiement de préproduction du port Azure, accédez à la page de votre déploiement de préproduction et sélectionnez le bouton **Supprimer**.

Vous pouvez également supprimer votre déploiement de préproduction à partir d’Azure CLI avec la commande suivante :

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
