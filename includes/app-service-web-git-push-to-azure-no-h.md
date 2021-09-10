---
title: Fichier include
description: Fichier include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d79d02054e21ba359637194d00fee9ac01a6d56
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078346"
---
1. Étant donné que vous déployez la branche `main`, vous devez définir la branche de déploiement par défaut pour votre application App Service sur `main` (voir [Modifier la branche de déploiement](../articles/app-service/deploy-local-git.md#change-deployment-branch)). Dans Cloud Shell, définissez le paramètre d’application `DEPLOYMENT_BRANCH` à l’aide de la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set). 

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DEPLOYMENT_BRANCH='main'
    ```

1. De retour dans la fenêtre de terminal locale, ajoutez un dépôt distant Azure dans votre dépôt Git local. Remplacez *\<deploymentLocalGitUrl-from-create-step>* par l’URL du dépôt Git distant que vous avez enregistrée à partir de [Créer une application web](#create-a-web-app).

    ```bash
    git remote add azure <deploymentLocalGitUrl-from-create-step>
    ```

1. Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application à l’aide de la commande suivante. Quand Git Credential Manager vous invite à entrer vos informations d’identification, veillez à entrer celles que vous avez créées dans la section **Configurer un utilisateur de déploiement**, et non pas celles vous permettant de vous connecter au portail Azure.

    ```bash
    git push azure main
    ```

    L’exécution de cette commande peut prendre quelques minutes. Pendant son exécution, des informations semblables à ce qui suit s’affichent :
