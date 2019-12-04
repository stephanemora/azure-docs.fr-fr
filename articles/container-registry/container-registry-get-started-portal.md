---
title: Guide de démarrage rapide - Créer un registre dans le portail
description: Apprenez rapidement à créer un registre de conteneurs Docker privé dans Azure Container Registry avec le portail Azure.
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c5e6de0fdb7d0e46fa9be28483fd894289baf1a3
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455223"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Démarrage rapide : Créer un registre de conteneurs privé à l’aide du portail Azure

Un registre Azure Container Registry est un registre Docker privé dans Azure, dans lequel vous pouvez stocker et gérer vos images conteneurs Docker privées. Dans ce guide de démarrage rapide, vous allez créer un registre de conteneurs à partir du portail Azure. Vous allez ensuite utiliser des commandes Docker pour envoyer (push) une image conteneur dans le registre, puis tirer (pull) et exécuter l’image à partir de votre registre.

Pour vous connecter au registre et utiliser des images conteneur, ce guide de démarrage rapide vous demande d’exécuter Azure CLI (version 2.0.55 ou ultérieure recommandée). Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli].

Docker doit également être installé en local. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Sélectionnez **Créer une ressource** > **Conteneurs** > **Container Registry**.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-01]

Entrez les valeurs **Nom du registre** et **Groupe de ressources**. Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. Pour ce guide de démarrage rapide, créez un groupe de ressources dans l’emplacement `West US` nommé `myResourceGroup`, et sélectionnez « De base » pour **SKU**. Sélectionnez **Créer** pour déployer l’instance ACR.

![Créer un registre de conteneurs dans le portail Azure][qs-portal-03]

Dans ce guide de démarrage rapide, vous allez créer un registre*De base*. Il s’agit d’une option à coût optimisé pour les développeurs qui apprennent à se servir d’Azure Container Registry. Pour plus d’informations sur les niveaux de service disponibles, consultez [Références SKU des registres de conteneurs][container-registry-skus].

Quand le message **Déploiement réussi** s’affiche, sélectionnez le registre de conteneurs dans le portail. 

![Vue d’ensemble du registre de conteneurs dans le portail Azure][qs-portal-05]

Notez la valeur du **Serveur de connexion**. Vous en aurez besoin dans les étapes suivantes quand vous utiliserez votre registre avec Azure CLI et Docker.

## <a name="log-in-to-registry"></a>Se connecter au registre

Avant d’extraire et de transmettre des images conteneur, vous devez vous connecter à l’instance ACR. Ouvrez un interpréteur de commandes dans votre système d’exploitation, puis utilisez la commande [az acr login][az-acr-login] dans Azure CLI.

```azurecli
az acr login --name <acrName>
```

Une fois l’opération terminée, la commande renvoie `Login Succeeded`. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Répertorier les images conteneur

Pour lister les images de votre registre, accédez à ce dernier dans le portail, puis sélectionnez **Référentiels** et le référentiel que vous avez créé avec `docker push`.

Dans cet exemple, nous sélectionnons le référentiel **hello-world**, et nous pouvons voir l’image étiquetée `v1` sous **ÉTIQUETTES**.

![Lister les images conteneur dans le portail Azure][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer vos ressources, accédez au groupe de ressources **myResourceGroup** dans le portail. Une fois le groupe de ressources chargé, cliquez sur **Supprimer le groupe de ressources** pour supprimer le groupe de ressources, le registre de conteneurs et les images conteneur stockées à cet endroit.

![Supprimer un groupe de ressources dans le portail Azure][qs-portal-08]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un registre de conteneurs Azure avec le portail Azure, envoyé (push) une image conteneur, puis tiré (pull) et exécuté l’image à partir du registre. Passez à présent au tutoriel sur Azure Container Registry (ACR) pour approfondir vos connaissances.

> [!div class="nextstepaction"]
> [Tutoriels sur Azure Container Registry][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
