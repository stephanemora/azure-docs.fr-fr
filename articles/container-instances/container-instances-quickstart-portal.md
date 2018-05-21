---
title: 'Démarrage rapide : créer votre premier conteneur Azure Container Instances avec le portail Azure'
description: Dans le cadre de ce guide de démarrage rapide, vous allez utiliser le portail Azure pour déployer un conteneur dans Azure Container Instances.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6aa6fb27b2aa7c8b9614e5812fadc629b1e185f8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Démarrage rapide : créer son premier conteneur dans Azure Container Instances

Azure Container Instances facilite la création et la gestion de conteneurs Docker dans Azure, sans avoir à approvisionner les machines virtuelles ou à adopter un service de niveau supérieur. Dans le cadre de ce guide démarrage rapide, vous utilisez le portail Azure pour créer un conteneur dans Azure et pour l’exposer sur Internet avec un nom de domaine complet. Après avoir configuré quelques paramètres, vous voyez ceci dans votre navigateur :

![L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur][aci-portal-07]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][azure-free-account] avant de commencer.

## <a name="create-a-container-instance"></a>Créer une instance de conteneur

Sélectionnez **Créer une ressource** > **Conteneurs** > **Container Instances**.

![Commencer à créer une instance de conteneur dans le portail Azure][aci-portal-01]

Entrez les valeurs suivantes dans les zones de texte **Nom du conteneur**, **Image de conteneur** et **Groupe de ressources**. Conservez les autres valeurs par défaut, puis cliquez sur **OK**.

* Nom du conteneur : `mycontainer`
* Image du conteneur : `microsoft/aci-helloworld`
* Groupe de ressources : `myResourceGroup`

![Configuration des paramètres de base pour une nouvelle instance de conteneur dans le portail Azure][aci-portal-03]

Vous pouvez créer des conteneurs Windows et Linux dans Azure Container Instances. Pour ce guide de démarrage rapide, laissez le paramètre par défaut **Linux** pour déployer l’image Linux `microsoft/aci-helloworld`.

Sous **Configuration**, spécifiez une **Étiquette du nom DNS** pour votre conteneur. Le nom doit être unique au sein de la région Azure où vous créez l’instance de conteneur. Votre conteneur sera publiquement accessible avec `<dns-name-label>.<region>.azurecontainer.io`.

Conservez les valeurs par défaut des autres paramètres dans **Configuration**, puis cliquez sur **OK** pour valider la configuration.

![Configuration d’une nouvelle instance de conteneur dans le portail Azure][aci-portal-04]

Une fois la validation terminée, un résumé des paramètres de votre conteneur s’affiche. Sélectionnez **OK** pour envoyer votre demande de déploiement de conteneur.

![Résumé des paramètres d’une nouvelle instance de conteneur dans le portail Azure][aci-portal-05]

Quand le déploiement commence, une vignette apparaît sur le tableau de bord de votre portail, indiquant que le déploiement est en cours. Une fois le déploiement terminé, la vignette montre votre nouvelle instance de conteneur.

![Progression de la création d’une nouvelle instance de conteneur dans le portail Azure][aci-portal-08]

Sélectionnez l’instance de conteneur **mycontainer** pour afficher ses propriétés. Prenez note du **nom de domaine complet** de l’instance de conteneur, ainsi que de son **état**.

![Vue d’ensemble du groupe de conteneurs dans le portail Azure][aci-portal-06]

Une fois que son **État** est *En cours d’exécution*, accédez au nom de domaine complet du conteneur dans votre navigateur.

![L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur][aci-portal-07]

Félicitations ! En configurant simplement quelques paramètres, vous avez déployé une application accessible publiquement dans Azure Container Instances.

## <a name="view-container-logs"></a>Afficher les journaux d’un conteneur

Il est utile d’afficher les journaux d’une instance de conteneur pour résoudre des problèmes liés au conteneur ou à l’application qu’il exécute.

Pour afficher les journaux du conteneur, sous **PARAMÈTRES** , sélectionnez **Conteneurs**, puis **Journaux**. Vous devez normalement voir la requête HTTP GET générée quand vous affichez l’application dans votre navigateur.

![Journaux du conteneur dans le portail Azure][aci-portal-11]

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous avez terminé avec le conteneur, sélectionnez **Vue d’ensemble** pour l’instance de conteneur *mycontainer*, puis sélectionnez **Supprimer**.

![Suppression de l’instance de conteneur dans le portail Azure][aci-portal-09]

Cliquez sur **Oui** quand la boîte de dialogue de confirmation apparaît.

![Supprimer la confirmation d’une instance de conteneur dans le portail Azure][aci-portal-10]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une instance de conteneur Azure à partir d’une image dans le registre du Hub Docker public. Si vous voulez créer une image conteneur par vous-même et la déployer dans Azure Container Instances à partir d’un registre de conteneurs Azure privé, passez au tutoriel Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/