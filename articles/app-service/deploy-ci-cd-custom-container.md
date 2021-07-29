---
title: CI/CD vers des conteneurs personnalisés
description: Configurez le déploiement continu sur un conteneur Windows ou Linux personnalisé dans Azure App Service.
keywords: azure app service, linux, docker, acr, oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 26515528a809b36be8d13d19f7baf99b5b2f897f
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110783300"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Déploiement continu avec des conteneurs personnalisés dans Azure App Service

Dans ce didacticiel, vous allez configurer le déploiement continu d’une image conteneur personnalisée à partir des référentiels [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) managés ou du [Docker Hub](https://hub.docker.com).

## <a name="1-go-to-deployment-center"></a>1. Accéder au centre de déploiement

Dans le [portail Azure](https://portal.azure.com), accédez à la page de gestion de votre application App Service.

Dans le menu de gauche, cliquez sur **Centre de déploiement** > **Paramètres**. 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. Choisir une source de déploiement

**Choisir**  la source de déploiement dépend de votre scénario :
- **Container Registry** installe CI/CD entre votre registre de conteneurs et App Service.
- L’option **GitHub Actions** est bien adaptée si vous conservez le code source de votre image conteneur dans GitHub. Déclenché par de nouvelles validations dans votre référentiel GitHub, l’action de déploiement peut exécuter `docker build` et `docker push` directement dans votre registre de conteneurs, puis mettre à jour votre application App Service pour exécuter la nouvelle image. Pour plus d’informations, consultez [Fonctionnement de CI/CD avec GitHub Actions](#how-cicd-works-with-github-actions).
- Pour configurer CI/CD avec **Azure Pipelines**, consultez [Déployer un conteneur d’application Web Azure à partir d’Azure pipelines](/azure/devops/pipelines/targets/webapp-on-container-linux).

> [!NOTE]
> Pour une application Docker Compose, sélectionnez **Container Registry**.

Si vous choisissez GitHub Actions, **cliquez** sur **Autoriser** et suivez les invites d’autorisation. Si vous avez déjà autorisé GitHub, vous pouvez le déployer à partir d’un référentiel d’utilisateur différent en cliquant sur **Modifier le compte**.

Après avoir autorisé votre compte Azure auprès de GitHub, **sélectionnez** l’**organisation**, le **référentiel** et la **branche** à partir desquels vous allez effectuer le déploiement.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. Configurer les paramètres du Registre
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. Configurer les paramètres du Registre

Pour déployer une application multiconteneur (Docker Compose), **sélectionnez** **Docker Compose** dans **Type de conteneur**.

Si vous ne voyez pas la liste déroulante **Type de conteneur**, faites défiler la page jusqu’à **Source**, puis **sélectionnez** **Container Registry**.
::: zone-end

Dans **Source du Registre**, **sélectionnez** l’emplacement de votre registre de conteneurs. S’il ne s’agit ni de Azure Container Registry ni de Docker Hub, **sélectionnez** **Registre privé**.

::: zone pivot="container-linux"
> [!NOTE]
> Si votre application multiconteneur (Docker Compose) utilise plusieurs images privées, assurez-vous que les images privées se trouvent dans le même registre privé et qu’elles sont accessibles avec les mêmes informations d’identification utilisateur. Si votre application multiconteneur utilise uniquement des images publiques, **sélectionnez** **Docker Hub**, même si certaines images ne se trouvent pas dans Docker Hub.
::: zone-end  

Suivez les étapes suivantes en sélectionnant l’onglet correspondant à votre choix.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

La liste déroulante **Registre** affiche les registres dans le même abonnement que votre application. **Sélectionnez** le registre souhaité.

> [!NOTE]
>  - Si vous souhaitez utiliser des identités gérées pour verrouiller l’accès à ACR, suivez ce guide :
>    - [Comment utiliser des identités gérées affectées par le système avec App Service et Azure Container Registry](https://github.com/Azure/app-service-linux-docs/blob/master/HowTo/use_system-assigned_managed_identities.md)
>    - [Comment utiliser des identités gérées affectées par l’utilisateur avec App Service et Azure Container Registry](https://github.com/Azure/app-service-linux-docs/blob/master/HowTo/use_user-assigned_managed_identities.md)
>  - Pour effectuer un déploiement à partir d’un registre dans un autre abonnement, **sélectionnez** plutôt **Registre privé** dans **Source du Registre**.
>   

::: zone pivot="container-windows"
**Sélectionnez** l’**image** et l’**étiquette** à déployer. Si vous le souhaitez, **tapez** la commande de démarrge dans le **Fichier de démarrage**. 
::: zone-end
::: zone pivot="container-linux"
Suivez l’étape suivante en fonction du **type de conteneur** :
- Pour **Docker Compose**, **sélectionnez** le registre de vos images privées. **Cliquez** sur **Choisir un fichier** pour charger votre [fichier Docker Compose](https://docs.docker.com/compose/compose-file/) ou **collez** simplement le contenu de votre fichier Docker Compose dans **Configuration**.
- Pour **Conteneur unique**, **sélectionnez** l’**image** et l’**étiquette** à déployer. Si vous le souhaitez, **tapez** la commande de démarrge dans le **Fichier de démarrage**. 
::: zone-end

App Service ajoute la chaîne du **Fichier de démarrage** à [la fin de la commande `docker run` (en tant que segment `[COMMAND] [ARG...]`)](https://docs.docker.com/engine/reference/run/) lors du démarrage de votre conteneur.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
Dans **Accès au référentiel**, **indiquez** si l’image à déployer est publique ou privée.
::: zone-end
::: zone pivot="container-linux"
Dans **Accès au référentiel**, **indiquez** si l’image à déployer est publique ou privée. Pour une application Docker Compose avec une ou plusieurs images privées, **sélectionnez** **Privée**.
::: zone-end

Si vous sélectionnez une image privée, **indiquez** la **connexion** (nom d’utilisateur) et le **mot de passe** du compte Docker.

::: zone pivot="container-windows"
**Indiquez** le nom de l’image et de l’étiquette dans **Nom et étiquette complets de l’image**, en les séparant par le signe `:` (par exemple, `nginx:latest`). Si vous le souhaitez, **tapez** la commande de démarrage dans le **Fichier de démarrage**. 
::: zone-end
::: zone pivot="container-linux"
Suivez l’étape suivante en fonction du **type de conteneur** :
- Pour **Docker Compose**, **sélectionnez** le registre de vos images privées. **Cliquez** sur **Choisir un fichier** pour charger votre [fichier Docker Compose](https://docs.docker.com/compose/compose-file/) ou **collez** simplement le contenu de votre fichier Docker Compose dans **Configuration**.
- Pour **Conteneur unique**, **indiquez** le nom de l’image et de l’étiquette dans **Nom et étiquette complets de l’image**, en les séparant par le signe `:` (par exemple, `nginx:latest`). Si vous le souhaitez, **tapez** la commande de démarrage dans le **Fichier de démarrage**. 
::: zone-end

App Service ajoute la chaîne du **Fichier de démarrage** à [la fin de la commande `docker run` (en tant que segment `[COMMAND] [ARG...]`)](https://docs.docker.com/engine/reference/run/) lors du démarrage de votre conteneur.

# <a name="private-registry"></a>[Registre privé](#tab/private)

Dans **URL du serveur**, **tapez** l’URL du serveur, en commençant par **https://** .

Dans **Connexion** et **Mot de passe**, **tapez** vos informations d’identification de connexion pour votre registre privé.

::: zone pivot="container-windows"
**Indiquez** le nom de l’image et de l’étiquette dans **Nom et étiquette complets de l’image**, en les séparant par le signe `:` (par exemple, `nginx:latest`). Si vous le souhaitez, **tapez** la commande de démarrage dans le **Fichier de démarrage**. 
::: zone-end
::: zone pivot="container-linux"
Suivez l’étape suivante en fonction du **type de conteneur** :
- Pour **Docker Compose**, **sélectionnez** le registre de vos images privées. **Cliquez** sur **Choisir un fichier** pour charger votre [fichier Docker Compose](https://docs.docker.com/compose/compose-file/) ou **collez** simplement le contenu de votre fichier Docker Compose dans **Configuration**.
- Pour **Conteneur unique**, **indiquez** le nom de l’image et de l’étiquette dans **Nom et étiquette complets de l’image**, en les séparant par le signe `:` (par exemple, `nginx:latest`). Si vous le souhaitez, **tapez** la commande de démarrage dans le **Fichier de démarrage**. 
::: zone-end

App Service ajoute la chaîne du **Fichier de démarrage** à [la fin de la commande `docker run` (en tant que segment `[COMMAND] [ARG...]`)](https://docs.docker.com/engine/reference/run/) lors du démarrage de votre conteneur.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. Activer CI/CD
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. Activer CI/CD
::: zone-end

App Service prend en charge l’intégration de CI/CD avec Azure Container Registry et Docker Hub. Pour l’activer, **sélectionnez** **Activé** dans **Déploiement continu**.

::: zone pivot="container-linux"
> [!NOTE]
> Si vous sélectionnez **GitHub Actions** dans **Source**, vous n’avez pas cette option, car CI/CD est géré directement par GitHub Actions. Au lieu de cela, vous voyez la section **Configuration du workflow**, dans laquelle vous pouvez **cliquer** sur **Aperçu du fichier** pour inspecter le fichier de workflow. Azure valide ce fichier dans le référentiel GitHub sélectionné afin de gérer les tâches de génération et de déploiement. Pour plus d’informations, consultez [Fonctionnement de CI/CD avec GitHub Actions](#how-cicd-works-with-github-actions).
::: zone-end

Quand vous activez cette option, App Service ajoute un webhook à votre référentiel dans Azure Container Registry ou Docker Hub. Votre référentiel publie sur ce webhook chaque fois que l’image sélectionnée est mise à jour avec `docker push`. Le webhook provoque le redémarrage et l’exécution de votre application App Service `docker pull` pour récupérer l’image mise à jour.

**Pour les autres registres privés**, vous pouvez effectuer une publication manuelle sur le webhook ou en tant qu’étape dans un pipeline CI/CD. Dans **URL du webhook**, **cliquez** sur le bouton **Copier** pour accéder à l’URL du webhook.

::: zone pivot="container-linux"
> [!NOTE]
> La prise en charge des applications multiconteneurs (Docker Compose) est limitée : 
> - Pour Azure Container Registry, App Service crée un webhook dans le registre sélectionné et définit ce dernier comme étendue. Un `docker push` vers n’importe quel référentiel du registre (y compris ceux qui ne sont pas référencés par votre fichier Docker Compose) déclenche le redémarrage de l’application. Vous souhaiterez peut-être [modifier le webhook](../container-registry/container-registry-webhook.md) pour l’adapter à une étendue plus étroite.
> - Docker Hub ne prend pas en charge les webhooks au niveau du Registre. Vous devez **ajouter** les webhooks manuellement aux images spécifiées dans votre fichier Docker Compose.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. Enregistrez vos paramètres
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. Enregistrez vos paramètres
::: zone-end

**Cliquez** sur **Enregistrer**.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>Fonctionnement de CI/CD avec GitHub Actions

Si vous choisissez **GitHub Actions** dans **Source** (voir [Choisir une source de déploiement](#2-choose-deployment-source)), App Service configure CI/CD comme suit :

- Dépose un fichier de workflow GitHub Actions dans votre référentiel GitHub pour gérer les tâches de génération et de déploiement vers App Service.
- Ajoute les informations d’identification de votre registre privé en tant que secrets GitHub. Le fichier de workflow généré exécute l’action [Azure/docker-login](https://github.com/Azure/docker-login) pour se connecter à votre registre privé, puis exécute `docker push` pour le déploiement.
- Ajoute le profil de publication de votre application en tant que secret GitHub. Le fichier de flux de travail généré utilise ce secret pour s’authentifier auprès d’App Service, puis exécute l’action [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) pour configurer l’image mise à jour, ce qui déclenche un redémarrage de l’application en vue de l’extraction de l’image mise à jour.
- Capture des informations à partir des [journaux d’exécution de workflow](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) et les affiche sous l’onglet **Journaux** dans le **Centre de déploiement** de votre application.

Vous pouvez personnaliser le fournisseur de build GitHub Actions comme suit :

- Personnalisez le fichier de workflow après qu’il a été généré dans votre référentiel GitHub. Pour plus d’informations, consultez [Syntaxe de workflow pour GitHub Actions](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Assurez-vous simplement que le workflow se termine par l’action [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) pour déclencher le redémarrage de l’application.
- Si la branche sélectionnée est protégée, vous pouvez toujours prévisualiser le fichier de workflow sans enregistrer la configuration, puis l’ajouter manuellement, ainsi que les secrets GitHub requis, dans votre référentiel. Cette méthode ne permet pas l’intégration des journaux avec le portail Azure.
- Plutôt qu’un profil de publication, effectuez le déploiement à l’aide d’un [principal de service](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) dans Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>S’authentifier avec un principal de service

Cette configuration facultative remplace l’authentification par défaut par les profils de publication dans le fichier de workflow généré.

**Générez** un principal de service à l’aide de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) dans [Azure CLI](/cli/azure/). Dans l’exemple suivant, remplacez *\<subscription-id>* , *\<group-name>* et *\<app-name>* par vos propres valeurs. **Enregistrez** l’intégralité de la sortie JSON pour l’étape suivante, y compris le niveau supérieur `{}`.

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> Pour des raisons de sécurité, accordez l’accès minimal requis au principal du service. L’étendue dans l’exemple précédent est limitée à l’application App Service spécifique, et non à l’ensemble du groupe de ressources.

Dans [GitHub](https://github.com/), **accédez** à votre référentiel, **sélectionnez** **Paramètres > Secrets > Ajouter un nouveau secret**. **Collez** l’intégralité de la sortie JSON de la commande Azure CLI dans le champ de valeur du secret. **Nommez** le secret comme `AZURE_CREDENTIALS`.

Dans le fichier de workflow généré par le **Centre de déploiement**, **modifiez** l’étape `azure/webapps-deploy` avec du code similaire à l’exemple suivant :

```yaml
- name: Sign in to Azure 
# Use the GitHub secret you added
- uses: azure/login@v1
    with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
- name: Deploy to Azure Web App
# Remove publish-profile
- uses: azure/webapps-deploy@v2
    with:
    app-name: '<app-name>'
    slot-name: 'production'
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>Automatiser avec CLI

Pour configurer le registre de conteneurs et l’image Docker, **exécutez** [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set).

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[Registre privé](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
Pour configurer une application multiconteneur (Docker Compose), **préparez** un fichier Docker Compose localement, puis **exécutez** [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set) avec le paramètre `--multicontainer-config-file`. Si votre fichier Docker Compose contient des images privées, **ajoutez** des paramètres `--docker-registry-server-*`, comme indiqué dans l’exemple précédent.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Pour configurer CI/CD à partir du registre de conteneurs sur votre application, **exécutez** la commande [az webapp deployment container config](/cli/azure/webapp/deployment/container#az_webapp_deployment-container-config) avec le paramètre `--enable-cd`. La commande génère l’URL du webhook, mais vous devez créer manuellement le webhook dans votre registre séparément. L’exemple suivant active CI/CD sur votre application, puis utilise l’URL du webhook dans la sortie pour créer le webhook dans Azure Container Registry.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>Plus de ressources

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Créer une application web .NET Core dans App Service sur Linux](quickstart-dotnetcore.md)
* [Démarrage rapide : Exécuter un conteneur personnalisé sur App Service](quickstart-custom-container.md)
* [FAQ pour App Service sur Linux](faq-app-service-linux.md)
* [Configurer des conteneurs personnalisés](configure-custom-container.md)
* [Flux de travail d’actions à déployer sur Azure](https://github.com/Azure/actions-workflow-samples)
