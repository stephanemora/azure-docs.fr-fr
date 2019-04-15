---
title: Guide de démarrage rapide – Déployer un conteneur Docker sur Azure Container Instances – Portail
description: Dans ce guide de démarrage rapide, vous utilisez le portail Azure pour déployer rapidement une application web conteneurisée qui s’exécute dans une instance de conteneur Azure isolé.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: f4d232d4d6043ede3979db67e5cd35130d931bef
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369443"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Démarrage rapide : Déployer un instance de conteneur dans Azure à l’aide du portail Azure

Utilisez Azure Container Instances pour exécuter, facilement et rapidement, des conteneurs Docker serverless dans Azure. Déployez une application sur une instance de conteneur à la demande lorsque vous n’avez pas besoin d’une plateforme d’orchestration de conteneur complète telle qu’Azure Kubernetes Service.

Dans cette procédure de démarrage rapide, vous utilisez le portail Azure pour déployer un conteneur Docker isolé et mettre son application à disposition avec un nom de domaine complet (FQDN). Après avoir configuré quelques paramètres et déployé le conteneur, vous pouvez accéder à l’application en cours d’exécution :

![Application déployée dans Azure Container Instances affichée dans le navigateur][aci-portal-07]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][azure-free-account] avant de commencer.

## <a name="create-a-container-instance"></a>Créer une instance de conteneur

Sélectionnez **Créer une ressource** > **Conteneurs** > **Container Instances**.

![Commencer à créer une instance de conteneur dans le portail Azure][aci-portal-01]

Entrez les valeurs suivantes dans les zones de texte **Nom du conteneur**, **Image de conteneur** et **Groupe de ressources**. Conservez les autres valeurs par défaut, puis cliquez sur **OK**.

* Nom du conteneur : `mycontainer`
* Image du conteneur : `mcr.microsoft.com/azuredocs/aci-helloworld`
* Groupe de ressources : **Créer** > `myResourceGroup`

![Configuration des paramètres de base pour une nouvelle instance de conteneur dans le portail Azure][aci-portal-03]

Pour ce guide de démarrage rapide, conservez le paramètre par défaut de **Public** afin de déployer l’image Microsoft publique `aci-helloworld`. Cette image contient une petite application web écrite en Node.js qui sert une page HTML statique.

Sous **Configuration**, spécifiez une **Étiquette du nom DNS** pour votre conteneur. Le nom doit être unique au sein de la région Azure dans laquelle vous créez l’instance de conteneur. Votre conteneur sera publiquement accessible avec `<dns-name-label>.<region>.azurecontainer.io`. Si vous recevez un message d’erreur « Étiquette de nom DNS indisponible », essayez d’utiliser une autre étiquette de nom DNS.

Conservez les valeurs par défaut des autres paramètres dans **Configuration**, puis cliquez sur **OK** pour valider la configuration.

![Configuration d’une nouvelle instance de conteneur dans le portail Azure][aci-portal-04]

Une fois la validation terminée, un résumé des paramètres de votre conteneur s’affiche. Sélectionnez **OK** pour envoyer votre demande de déploiement de conteneur.

![Résumé des paramètres d’une nouvelle instance de conteneur dans le portail Azure][aci-portal-05]

Quand le déploiement commence, une notification s’affiche, indiquant que le déploiement est en cours. Une autre notification s’affiche lorsque le groupe de conteneurs a été déployé.

![Progression de la création d’une nouvelle instance de conteneur dans le portail Azure][aci-portal-08]

Ouvrez la vue d’ensemble du groupe de conteneurs en accédant à **Groupes de ressources** > **myResourceGroup** > **mycontainer**. Prenez note du **nom de domaine complet** de l’instance de conteneur, ainsi que de son **état**.

![Vue d’ensemble du groupe de conteneurs dans le portail Azure][aci-portal-06]

Une fois que son **État** est *En cours d’exécution*, accédez au nom de domaine complet du conteneur dans votre navigateur.

![L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur][aci-portal-07]

Félicitations ! En configurant simplement quelques paramètres, vous avez déployé une application accessible publiquement dans Azure Container Instances.

## <a name="view-container-logs"></a>Afficher les journaux d’activité du conteneur

Il est utile d’afficher les journaux d’activité d’une instance de conteneur pour résoudre des problèmes liés au conteneur ou à l’application qu’il exécute.

Pour afficher les journaux d’activité du conteneur, sous **Paramètres**, sélectionnez **Conteneurs**, puis **Journaux d’activité**. Vous devez normalement voir la requête HTTP GET générée quand vous affichez l’application dans votre navigateur.

![Journaux d’activité du conteneur dans le portail Azure][aci-portal-11]

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous avez terminé avec le conteneur, sélectionnez **Vue d’ensemble** pour l’instance de conteneur *mycontainer*, puis sélectionnez **Supprimer**.

![Suppression de l’instance de conteneur dans le portail Azure][aci-portal-09]

Cliquez sur **Oui** quand la boîte de dialogue de confirmation apparaît.

![Supprimer la confirmation d’une instance de conteneur dans le portail Azure][aci-portal-10]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une instance de conteneur Azure à partir d’une image Microsoft publique. Si vous voulez créer une image conteneur et la déployer à partir d’un registre de conteneurs Azure privé, passez au didacticiel Azure Container Instances.

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