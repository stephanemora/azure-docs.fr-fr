---
title: Déploiement Git local vers Azure App Service
description: Découvrez comment activer le déploiement Git local vers Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 0f6a0e2fe3aa632137392efe806aaab265eedf10
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435540"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Déploiement Git local vers Azure App Service

Ce guide de procédures vous montre comment déployer votre code sur [Azure App Service](app-service-web-overview.md) depuis un dépôt Git sur votre ordinateur local.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans ce guide de procédures :

* [Installer Git](http://www.git-scm.com/downloads).
* Créez un dépôt Git local comprenant le code que vous souhaitez déployer.

Pour utiliser un dépôt d’exemples à suivre, exécutez la commande suivante dans la fenêtre de terminal locale :

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git-with-kudu-builds"></a>Déployer à partir de Git local avec les builds Kudu

Pour permettre un déploiement Git local pour votre application avec le serveur de build Kudu, le plus simple consiste à utiliser Cloud Shell.

### <a name="create-a-deployment-user"></a>Créer un utilisateur de déploiement

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Activer Git local avec Kudu

Pour permettre un déploiement Git local pour votre application avec le serveur de build Kudu, exécutez [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) dans Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Pour créer une application Git, exécutez [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) dans Cloud Shell avec le paramètre `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

La sortie de la commande `az webapp create` doit ressembler à ceci :

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Déployez votre projet

De retour dans la _fenêtre du terminal local_, ajoutez un référentiel distant Azure dans votre référentiel Git local. Remplacez _\<url>_ par l’URL du Git distant de la section [Activer Git pour votre application](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application à l’aide de la commande suivante. Lorsque vous êtes invité à saisir un mot de passe, veillez à utiliser celui que vous avez créé dans [Configurer un utilisateur de déploiement](#configure-a-deployment-user), et non pas celui vous permettant de vous connecter au portail Azure.

```bash
git push azure master
```

Vous pouvez voir une automation spécifique au runtime dans la sortie, comme MSBuild pour ASP.NET, `npm install` pour Node.js et `pip install` pour Python. 

Accédez à votre application pour vérifier que le contenu a été déployé.

## <a name="deploy-from-local-git-with-vsts-builds"></a>Déployer à partir de Git local avec les builds VSTS

> [!NOTE]
> Pour qu’Azure App Service puisse créer la version nécessaire et libérer des définitions dans votre compte VSTS, votre compte Azure doit avoir le rôle de **Propriétaire** dans votre abonnement Azure.
>

Pour permettre un déploiement Git local pour votre application avec le serveur de build Kudu, accédez à votre application dans le [portail Azure](https://portal.azure.com).

Dans le volet de navigation de gauche de la page de votre application, cliquez sur **Centre de déploiement** > **Git local** > **Continuer**. 

![](media/app-service-deploy-local-git/portal-enable.png)

Cliquez sur **Livraison continue VSTS** > **Continuer**.

![](media/app-service-deploy-local-git/vsts-build-server.png)

Sur la page **Configurer**, configurez un nouveau compte VSTS ou spécifiez un compte existant. Lorsque vous avez terminé, cliquez sur **Continuer**.

> [!NOTE]
> Si vous voulez utiliser un compte VSTS existant qui ne figure pas dans la liste, vous devez [associer le compte VSTS à votre abonnement Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Sur la page **Test**, choisissez si vous voulez activer les tests de charge, puis cliquez sur **Continuer**.

En fonction du [niveau tarifaire](/pricing/details/app-service/plans/) de votre plan App Service, une page proposant un **déploiement intermédiaire** s’affiche parfois. Choisissez si vous voulez activer des emplacements de déploiement, puis cliquez sur **Continuer**.

Sur la page **Synthèse**, vérifiez les options, puis cliquez sur **Terminer**.

Quelques minutes sont nécessaire pour que le compte VSTS soit prêt. Lorsqu’il est prêt, copiez l’URL du référentiel Git dans le centre de déploiement.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

De retour dans la _fenêtre du terminal local_, ajoutez un référentiel distant Azure dans votre référentiel Git local. Remplacez _\<url>_ par l’URL que vous avez obtenue à l’étape précédente.

```bash
git remote add vsts <url>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application à l’aide de la commande suivante. Lorsque vous y êtes invité par Git Credential Manager, connectez-vous à votre compte utilisateur visualstudio.com. Pour découvrir d’autres méthodes d’authentification, consultez [Vue d’ensemble de l’authentification VSTS](/vsts/git/auth-overview?view=vsts).

```bash
git push vsts master
```

Une fois le déploiement terminé, vous pouvez consulter la progression de la génération sur `https://<vsts_account>.visualstudio.com/<project_name>/_build` et la progression du déploiement sur `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Accédez à votre application pour vérifier que le contenu a été déployé.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Résolution des problèmes de déploiement Kudu

Voici les erreurs ou les problèmes couramment rencontrés lors de l’utilisation de Git pour publier une application App Service dans Azure :

---
**Symptôme** : `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Cause** : Cette erreur peut se produire si l’application n’est pas opérationnelle.

**Résolution** : démarrez l’application dans le portail Azure. Le déploiement Git est indisponible quand l’application web est arrêtée.

---
**Symptôme** : `Couldn't resolve host 'hostname'`

**Cause** : Cette erreur peut se produire si les informations d’adresse entrées au moment de la création du dépôt distant « azure » sont incorrectes.

**Résolution** : utilisez la commande `git remote -v` pour répertorier tous les référentiels distants avec l’URL associée. Vérifiez que l'URL du référentiel distant « azure » est correcte. Si nécessaire, supprimez et recréez ce référentiel distant au moyen de l’URL correcte.

---
**Symptôme** : `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Cause** : Cette erreur peut se produire si vous ne spécifiez pas de branche pendant l’opération `git push`, ou si vous n’avez pas défini la valeur `push.default` dans `.gitconfig`.

**Résolution** : Réexécutez `git push`, en spécifiant la branche maîtresse. Par exemple : 

```bash
git push azure master
```

---
**Symptôme** : `src refspec [branchname] does not match any.`

**Cause** : Cette erreur peut se produire si vous tentez d’effectuer une opération Push sur une autre branche que la branche maîtresse sur le dépôt distant « azure ».

**Résolution** : Réexécutez `git push`, en spécifiant la branche maîtresse. Par exemple : 

```bash
git push azure master
```

---
**Symptôme** : `RPC failed; result=22, HTTP code = 5xx.`

**Cause** : Cette erreur peut se produire si vous essayez d’envoyer (push) un dépôt Git volumineux via HTTPS.

**Résolution** : modifiez la configuration git sur l’ordinateur local pour agrandir le postBuffer.

```bash
git config --global http.postBuffer 524288000
```

---
**Symptôme** : `Error - Changes committed to remote repository but your web app not updated.`

**Cause** : Cette erreur peut se produire si vous déployez une application Node.js contenant un fichier _package.json_ spécifiant des modules obligatoires supplémentaires.

**Résolution** : Des messages supplémentaires contenant « npm ERR! » doivent être journalisés avant cette erreur, et peuvent fournir davantage de contexte sur l’échec. Voici les causes connues de cette erreur et le message « npm ERR! » correspondant :

* **Fichier package.json incorrect**: npm ERR! Couldn’t read dependencies.
* **Un module natif qui n’a pas de distribution binaire pour Windows** :

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      Ou
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Ressources supplémentaires

* [Documentation du projet Kudu](https://github.com/projectkudu/kudu/wiki)
* [Déploiement continu vers Azure App Service](app-service-continuous-deployment.md)
* [Exemple : Créer une application web et déployer du code à partir d’un dépôt Git local (Azure CLI)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Exemple : Créer une application web et déployer du code à partir d’un dépôt Git local (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
