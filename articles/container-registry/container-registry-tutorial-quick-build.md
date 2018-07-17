---
title: Didacticiel - Générer des images de conteneur dans le cloud avec Azure Container Registry Build
description: Dans ce didacticiel, vous allez apprendre comment générer une image de conteneur Docker dans Azure avec Azure Container Registry Build (ACR Build), puis la déployer dans Azure Container Instances.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: d86c47c890fd15515c590e06b395ca82f9747ffe
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38461467"
---
# <a name="tutorial-build-container-images-in-the-cloud-with-azure-container-registry-build"></a>Didacticiel - Générer des images de conteneur dans le cloud avec Azure Container Registry Build

**ACR Build** est une suite de fonctionnalités d’Azure Container Registry qui fournit des builds d’images de conteneur Docker rationalisés et efficaces dans Azure. Dans cet article, vous allez apprendre comment utiliser la fonctionnalité de *génération rapide* d’ACR Build. Cette dernière étend votre boucle interne de développement sur le cloud, en prenant en charge la validation de l’exécution des builds et la transmission automatique des images générées à votre registre de conteneurs. Vos images sont générées de manière native dans le cloud, à proximité de votre registre, pour un déploiement plus rapide.

La totalité de votre expertise Dockerfile est directement transférable à ACR Build. Vous n’avez aucune obligation de modifier vos fichiers Dockerfiles pour générer dans le cloud avec ACR Build, simplement la commande que vous exécutez.

Ce didacticiel est la première partie d’une série d’étapes :

> [!div class="checklist"]
> * Récupérer le code source de l'exemple d'application
> * Générer une image de conteneur dans Azure
> * Déployer un conteneur sur Azure Container Instances

Dans les didacticiels suivants, vous apprendrez comment utiliser les tâches de génération d’ACR Build pour les générations automatisée d’images de conteneur lors de la validation du code et la mise à jour des images de base.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous souhaitez utiliser l’interface Azure CLI en local, vous devez avoir installé la version **2.0.32** d’Azure CLI ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau l’interface CLI, consultez l’article [Installer Azure CLI 2.0][azure-cli].

## <a name="prerequisites"></a>Prérequis

### <a name="github-account"></a>Compte GitHub

Si vous n’en disposez pas encore, créez un compte sur https://github.com. Cette suite de didacticiels utilise un référentiel GitHub pour représenter les générations automatisées d’images dans ACR Build.

### <a name="fork-sample-repository"></a>Dupliquer l’exemple de référentiel

Ensuite, utilisez l’IU GitHub pour dupliquer l’exemple de référentiel dans votre compte GitHub. Dans ce didacticiel, vous générez une image de conteneur de la source dans le référentiel, et dans le référentiel suivant, vous transmettez une validation vers votre fourche du référentiel afin de lancer une génération automatisée.

Dupliquez ce référentiel : https://github.com/Azure-Samples/acr-build-helloworld-node

![Capture d’écran du bouton de duplication (mis en surbrillance) dans GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Cloner votre fourche

Une fois le référentiel dupliqué, clonez votre fourche et saisissez le répertoire contenant votre clone local.

Clonez le référentiel avec `git`, remplacez **\<your-github-username\>** par votre nom d’utilisateur GitHub :

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Saisissez le répertoire contenant le code source :

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Interpréteur de commandes Bash

Les commandes de cette suite de didacticiels sont formatées pour l’interpréteur de commandes Bash. Si vous préférez utiliser PowerShell, l’invite de commandes ou un autre interpréteur de commandes, il vous faudra éventuellement ajuster en conséquence la continuation de ligne et le format de la variable d’environnement.

## <a name="build-in-azure-with-acr-build"></a>Générer dans Azure avec ACR Build

Maintenant que vous avez enregistré le code source à destination de votre machine, exécutez cette procédure pour créer un registre de conteneurs et générer l’image de conteneur avec ACR Build.

Pour simplifier l’exécution des exemples de commandes, les didacticiels de cette suite utilisent les variables d’environnement de l’interpréteur de commandes. Exécutez la commande suivante pour définir la variable `ACR_NAME`. Remplacez **\<registry-name\>** par un nom unique à associer à votre nouveau registre de conteneurs. Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. Les autres ressources créées dans ce didacticiel étant basées sur ce nom, vous devriez avoir besoin de modifier uniquement cette première variable.

```azurecli-interactive
ACR_NAME=<registry-name>
```

Avec cette variable d’environnement de registre de conteneurs renseignée, vous devriez être en mesure de copier-coller le reste des commandes dans le didacticiel, sans modifier aucune valeur. Exécutez les commandes suivantes afin de créer un groupe de ressources et un registre de conteneurs :

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Maintenant que vous disposez d’un registre, utilisez ACR Build pour générer une image de conteneur à partir de l’exemple de code. Exécutez la commande [az acr build][az-acr-build] afin d’effectuer une *Génération rapide* :

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
```

La sortie de la commande [az acr build][az-acr-build] est similaire à ce qui suit. Vous avez accès au chargement du code source (le « contexte ») dans Azure, et aux détails de l’opération `docker build` exécutée dans le cloud par ACR Build. Dans la mesure où ACR Build utilise `docker build` pour générer vos images, il n’est pas nécessaire de modifier vos fichiers Dockerfiles pour commencer à utiliser ACR Build.

```console
$ az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
Sending build context (4.909 KiB) to ACR.
Queued a build with build ID: aa1
Waiting for a build agent...
Sending build context to Docker daemon  22.53kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 0c4814714938
Step 3/5 : RUN cd /src && npm install
 ---> Running in 4f77ce7b330d
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.096s
Removing intermediate container 4f77ce7b330d
 ---> e0eb24339e07
Step 4/5 : EXPOSE 80
 ---> Running in 872bd29edbc7
Removing intermediate container 872bd29edbc7
 ---> 22ba8d8ffb4e
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 1a40c05c4122
Removing intermediate container 1a40c05c4122
 ---> 0a9a4b74fb53
Successfully built 0a9a4b74fb53
Successfully tagged mycontainerregistry.azurecr.io/helloacrbuild:v1
time="2018-05-10T19:10:20Z" level=info msg="Running command docker push mycontainerregistry.azurecr.io/helloacrbuild:v1"
The push refers to repository [mycontainerregistry.azurecr.io/helloacrbuild]
d2b301f7ef94: Preparing
d0e0f2bb8747: Preparing
26b0c207c4a9: Preparing
917e7cdebc8b: Preparing
9dfa40a0da3b: Preparing
26b0c207c4a9: Pushed
d2b301f7ef94: Pushed
d0e0f2bb8747: Pushed
9dfa40a0da3b: Pushed
917e7cdebc8b: Pushed
v1: digest: sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd size: 1366
time="2018-05-10T19:11:07Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloacrbuild:v1"
"["mycontainerregistry.azurecr.io/helloacrbuild@sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd"]"
time="2018-05-10T19:11:07Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloacrbuild
    tag: v1
    digest: sha256:78d7980b4c80a078192bd4749c27eeae56421079606ed7b7d8ae84dbb04193fd
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3

Build complete
Build ID: aa1 was successful after 52.522222729s
```

À proximité de la fin de la sortie, ACR Build affiche les dépendances découvertes pour votre image. Ce faisant, ACR Build est en mesure d’automatiser les générations d’images sur les mises à jour d’images de base, comme lors de la mise à jour d’une image avec les correctifs du système d’exploitation ou de l’infrastructure. Plus loin dans cette suite de didacticiels, vous allez en savoir plus sur la prise en charge d’ACR Build pour les mises à jour d’images de base.

## <a name="deploy-to-azure-container-instances"></a>Déployer vers Azure Container Instances

ACR Build transfère automatiquement les images générées vers votre registre par défaut, ce qui vous permet de les déployer immédiatement.

Dans cette section, vous créez une instance Azure Key Vault et un principal du service, puis déployez le conteneur vers Azure Container Instances (ACI) à l’aide des informations d’identification du principal.

### <a name="configure-registry-authentication"></a>Configurer l’authentification du registre

L’ensemble des scénarios de production doivent utiliser les [principaux du service][service-principal-auth] pour accéder à un registre de conteneurs Azure. Les principaux de service vous permettent de fournir un contrôle d’accès basé sur des rôles à vos images conteneur. Par exemple, vous pouvez configurer un principal de service avec uniquement un accès d’extraction à un registre.

#### <a name="create-key-vault"></a>Création d’un coffre de clés

Si vous n’avez pas encore un coffre dans [Azure Key Vault](/azure/key-vault/), créez-en un avec Azure CLI à l’aide des commandes suivantes.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-service-principal-and-store-credentials"></a>Créer un principal de service et stocker les informations d’identification

Vous devez maintenant créer un principal de service et stocker ses informations d’identification dans votre coffre de clés.

Utilisez la commande [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] pour créer le principal du service, et [az keyvault secret set][az-keyvault-secret-set] pour stocker le **mot de passe** du principal de service dans le coffre :

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

L’argument `--role` dans la commande précédente configure le principal de service avec le rôle *lecteur*, ce qui lui accorde uniquement un accès d’extraction au registre. Pour accorder les accès push et pull (envoi et tirage), changez l’argument `--role` par *collaborateur*.

Ensuite, stockez le *appId* du principal de service dans le coffre, qui est le **nom d’utilisateur** que vous passez à Azure Container Registry pour l’authentification :

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Vous avez créé un coffre de clés Azure et il contient deux secrets :

* `$ACR_NAME-pull-usr` : l’ID de principal du service, pour une utilisation comme **nom d’utilisateur** du registre de conteneurs.
* `$ACR_NAME-pull-pwd` : le mot de passe du principal du service, pour une utilisation comme **mot de passe** du registre de conteneurs.

Vous pouvez maintenant référencer ces secrets par nom lorsque vous ou vos applications et services extrayez des images du registre.

### <a name="deploy-container-with-azure-cli"></a>Déployer le conteneur avec Azure CLI

Maintenant que les informations d’identification du principal de service sont stockées en tant que secrets de Azure Key Vault, vos applications et vos services peuvent les utiliser pour accéder à votre registre privé.

Exécutez la commande [az container create][az-container-create] suivante pour déployer une instance de conteneur. La commande utilise les informations d’identification du principal du service stockées dans Azure Key Vault pour s’authentifier sur votre registre de conteneurs.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-build \
    --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-build-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

La valeur `--dns-name-label` doit être unique dans Azure, afin que la commande précédente ajoute le nom de votre registre de conteneurs à l’étiquette de nom DNS du conteneur. La sortie pour la commande affiche le nom de domaine complet du conteneur (FQDN), par exemple :

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-build \
>     --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-build-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
-------------------------------------------
acr-build-1175.eastus.azurecontainer.io
```

Consignez le nom de domaine complet du conteneur, car vous allez vous en servir dans la section suivante.

### <a name="verify-deployment"></a>Vérifier le déploiement

Pour contrôler le processus de démarrage du conteneur, exécutez la commande [az container attach][az-container-attach] :

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-build
```

La sortie `az container attach` affiche dans un premier temps le statut du conteneur quand il extrait l’image et démarre, puis lie les éléments STDOUT et STDERR de votre console locale à ceux du conteneur.

```console
$ az container attach --resource-group $RES_GROUP --name acr-build
Container 'acr-build' is in state 'Waiting'...
Container 'acr-build' is in state 'Running'...
(count: 1) (last timestamp: 2018-04-03 19:45:37+00:00) pulling image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Successfully pulled image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Created container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Started container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413

Start streaming logs:
Server running at http://localhost:80
```

Lorsque `Server running at http://localhost:80` s’affiche, accédez au nom de domaine complet du conteneur dans votre navigateur afin d’afficher l’application en cours d’exécution :

![Capture d’écran de votre exemple d’application affichée dans le navigateur][quick-build-02-browser]

Pour dissocier votre console du conteneur, appuyez sur `Control+C`.

## <a name="clean-up-resources"></a>Supprimer les ressources

Arrêtez l’instance de conteneur avec la commande [az container delete][az-container-delete] :

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-build
```

Pour supprimer *toutes* les ressources créées dans ce didacticiel, notamment le registre de conteneurs, le coffre de clés et le principal du service, exécutez les commandes suivantes. Ces ressources sont utilisées dans le [prochain didacticiel](container-registry-tutorial-build-task.md) de la suite, vous avez donc intérêt à les conserver si vous comptez passer directement à ce prochain didacticiel.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez testé votre boucle interne avec une génération rapide, configurez une **tâche de génération** afin de déclencher des générations d’images de conteneurs lorsque vous validez le code source sur un référentiel Git :

> [!div class="nextstepaction"]
> [Déclencher des générations automatiques avec des tâches de génération](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
