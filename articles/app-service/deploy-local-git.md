---
title: Déployer à partir d’un dépôt Git local
description: Découvrez comment activer le déploiement Git local vers Azure App Service L’une des façons les plus simples de déployer du code à partir de votre ordinateur local.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e0dc9093503cab92a71517a21a8788814d16cbbe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772862"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Déploiement Git local vers Azure App Service

Ce guide pratique vous montre comment déployer votre application sur [Azure App Service](overview.md) depuis un référentiel Git sur votre ordinateur local.

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans ce guide de procédures :

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Installer Git](https://www.git-scm.com/downloads).

- Obtenez un référentiel Git local comprenant le code que vous souhaitez déployer. Pour télécharger un exemple de référentiel, exécutez la commande suivante dans la fenêtre de terminal locale :
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Configuration d’un utilisateur de déploiement

Consultez [Configurer les informations d’identification de déploiement pour Azure App Service](deploy-configure-credentials.md). Vous pouvez utiliser les informations d’identification de portée utilisateur ou les informations d’identification de portée application.

## <a name="create-a-git-enabled-app"></a>Créer une application compatible Git

Si vous disposez déjà d’une application App Service et que vous souhaitez configurer le déploiement Git local pour celle-ci, consultez plutôt [Configurer une application existante](#configure-an-existing-app).

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Exécutez [`az webapp create`](/cli/azure/webapp#az_webapp_create) avec l'option `--deployment-local-git`. Par exemple :

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

La sortie contient une URL, telle que : `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Utilisez cette URL pour déployer votre application à l’étape suivante.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Exécutez [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) à partir de la racine de votre référentiel Git. Par exemple :

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Lorsque vous exécutez cette cmdlet à partir d’un répertoire qui est un référentiel Git, elle crée automatiquement un dépôt distant Git dans votre application App Service pour vous, intitulé `azure`.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Dans le portail, vous devez d’abord créer une application, puis configurer son déploiement. Consultez [Configurer une application existante](#configure-an-existing-app).

-----

## <a name="configure-an-existing-app"></a>Configurer une application existante

Si vous n’avez pas encore créé d’application, consultez plutôt [Créer une application compatible Git](#create-a-git-enabled-app).

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Exécutez [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_local_git). Par exemple :

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

La sortie contient une URL, telle que : `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Utilisez cette URL pour déployer votre application à l’étape suivante.

> [!TIP]
> Cette URL contient le nom d’utilisateur de déploiement de portée utilisateur. Si vous le souhaitez, vous pouvez plutôt [utiliser les informations d’identification de portée application](deploy-configure-credentials.md#appscope). 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Définissez le `scmType` de votre application en exécutant la cmdlet [Set-AzResource](/powershell/module/az.resources/set-azresource).

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Dans le [portail Azure](https://portal.azure.com), accédez à la page de gestion de votre application.

1. Dans le menu gauche, sélectionnez **Centre de déploiement** > **Paramètres**. Sélectionnez **Git local** dans **Source**, puis cliquez sur **Enregistrer**.

    ![Montre comment activer le déploiement Git local pour App Service dans le portail Azure](./media/deploy-local-git/enable-portal.png)

1. Dans la section Git local, copiez l'**URI de Git Clone** pour plus tard. Cet URI ne contient aucune information d’identification.

-----

## <a name="deploy-the-web-app"></a>Déployer l’application web

1. Dans une fenêtre de terminal local, remplacez le répertoire par la racine de votre référentiel Git et ajoutez un dépôt distant Git en utilisant l’URL que vous avez obtenue à partir de votre application. Si la méthode que vous avez choisie ne vous donne pas d’URL, utilisez `https://<app-name>.scm.azurewebsites.net/<app-name>.git` avec le nom de votre application dans `<app-name>`.
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Si vous avez [créé une application Git dans PowerShell à l’aide de New-AzWebApp](#create-a-git-enabled-app), le dépôt distant est déjà créé pour vous.
   
1. Effectuez une transmission de type push vers Azure avec `git push azure master`. 
   
1. Dans la fenêtre **Gestionnaire des informations d’identification Git**, entrez vos [informations d’identification pour l’étendue de l’utilisateur ou de l’application](#configure-a-deployment-user), et non les informations d’identification de votre connexion Azure.

    Si l’URL de votre dépôt distant Git contient déjà le nom d’utilisateur et le mot de passe, vous ne serez pas invité à le faire. 
   
1. Passez en revue la sortie. Vous pouvez voir une automation spécifique au runtime, comme MSBuild pour ASP.NET, `npm install` pour Node.js et `pip install` pour Python. 
   
1. Dans le portail Azure, accédez à votre application pour vérifier que le contenu a été déployé.

## <a name="troubleshoot-deployment"></a>Résoudre les problèmes de déploiement

Les messages d'erreur suivants peuvent s'afficher lorsque vous utilisez Git pour publier une application App Service dans Azure :

|Message|Cause|Résolution
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|L’application n’est pas opérationnelle.|Démarrez l’application dans le portail Azure. Le déploiement Git n'est pas disponible lorsque l’application web est arrêtée.|
|`Couldn't resolve host 'hostname'`|Les informations d’adresse du référentiel «Azure» distant ne sont pas correctes.|Utilisez la commande `git remote -v` pour répertorier tous les référentiels distants avec l’URL associée. Vérifiez que l'URL du référentiel distant « azure » est correcte. Si nécessaire, supprimez et recréez ce référentiel distant au moyen de l’URL correcte.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Vous n’avez pas spécifié de branche pendant `git push` ou vous n'avez pas défini la valeur `push.default` dans `.gitconfig`.|Réexécutez `git push`, en spécifiant la branche primaire : `git push azure main`.|
|`Error - Changes committed to remote repository but deployment to website failed.`|Vous avez envoyé (push) une branche locale qui ne correspond pas à la branche de déploiement d’applications sur « Azure ».|Vérifiez que la branche actuelle est `master`. Pour modifier la branche par défaut, utilisez le paramètre d’application `DEPLOYMENT_BRANCH`.|
|`src refspec [branchname] does not match any.`|Vous avez tenté d’effectuer une transmission de type push sur une autre branche que la branche primaire du référentiel distant « azure ».|Réexécutez `git push`, en spécifiant la branche primaire : `git push azure main`.|
|`RPC failed; result=22, HTTP code = 5xx.`|Cette erreur peut se produire si vous essayez d’envoyer (push) un dépôt Git volumineux via HTTPS.|Modifiez la configuration Git sur l’ordinateur local pour agrandir le `postBuffer`. Par exemple : `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Vous avez déployé une application Node.js contenant un fichier _package.json_ spécifiant des modules obligatoires supplémentaires.|Examinez les messages d'erreur `npm ERR!` préalables à cette erreur pour plus de contexte sur l’échec. Voici les causes connues de cette erreur et les messages `npm ERR!` correspondants :<br /><br />**Fichier package.json incorrect**: `npm ERR! Couldn't read dependencies.`<br /><br />**Un module natif n’a pas de distribution binaire pour Windows** :<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />or <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Ressources supplémentaires

- [Serveur de build App Service (documentation Project Kudu)](https://github.com/projectkudu/kudu/wiki)
- [Déploiement continu vers Azure App Service](deploy-continuous-deployment.md)
- [Exemple : Créer une application web et déployer du code à partir d’un référentiel Git local (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Exemple : Créer une application web et déployer du code à partir d’un référentiel Git local (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
