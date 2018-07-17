---
title: 'Didacticiel : Automatiser la génération des images de conteneur en fonction de la mise à jour d’une image de base avec Azure Container Registry Build'
description: Dans ce didacticiel, vous allez découvrir comment configurer une tâche de génération pour déclencher automatiquement la génération des images de conteneur dans le cloud lorsqu’une image de base est mise à jour.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: a302cdcf94baa869e55262c4cd380fc05bf64299
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38461603"
---
# <a name="tutorial-automate-image-builds-on-base-image-update-with-azure-container-registry-build"></a>Didacticiel : Automatiser la génération des images en fonction de la mise à jour d’une image de base avec Azure Container Registry Build

ACR Build prend en charge l’exécution des générations automatisées lorsque l’image de base d’un conteneur est mise à jour, par exemple lorsque vous retouchez le système d’exploitation ou l’infrastructure d’application dans l’une de vos images de base. Dans ce didacticiel, vous allez découvrir comment créer une tâche de génération dans ACR Build qui déclenche une génération dans le cloud lorsque l’image de base d’un conteneur a été envoyée vers votre registre.

Dans ce didacticiel, le dernier de la série, vous allez apprendre à :

> [!div class="checklist"]
> * Générer l’image de base
> * Créer une tâche de génération d’une image d’application
> * Mettre à jour l’image de base pour déclencher la génération d’une image d’application
> * Afficher la génération déclenchée
> * Vérifier l’image d’application mise à jour

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous souhaitez utiliser l’interface Azure CLI en local, vous devez avoir installé la version **2.0.32** d’Azure CLI ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau l’interface CLI, consultez l’article [Installer Azure CLI 2.0][azure-cli].

## <a name="prerequisites"></a>Prérequis

### <a name="complete-previous-tutorials"></a>Terminer les didacticiels précédents

Pour suivre ce didacticiel, vous devez avoir déjà effectué les étapes des deux premiers didacticiels de la série, dans lesquels vous avez appris à :

* Créer un registre de conteneurs Azure
* Dupliquer l’exemple de référentiel
* Cloner l’exemple de référentiel
* Créer un jeton d’accès personnel GitHub

Si ce n’est déjà fait, complétez les deux premiers didacticiels avant de continuer :

[Générer des images de conteneur dans le cloud avec Azure Container Registry Build](container-registry-tutorial-quick-build.md)

[Automatiser la génération des images de conteneur avec Azure Container Registry Build](container-registry-tutorial-build-task.md)

### <a name="configure-environment"></a>Configurer l’environnement

Renseignez ces variables d’environnement shell avec des valeurs appropriées pour votre environnement. Cela n’est pas impératif, mais facilite un peu l’exécution des commandes multilignes de l’interface de ligne de commande Azure de ce didacticiel. Si vous ne les renseignez pas, vous devrez remplacer manuellement chaque valeur à chaque fois qu’elle apparaît dans les exemples de commandes.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Images de base

Les fichiers Dockerfile qui définissent la plupart des images de conteneur spécifient une image parente, souvent appelée *image de base*, sur laquelle les autres images sont basées. Les images de base contiennent en général le système d’exploitation, par exemple [Alpine Linux][base-alpine] ou [Windows Nano Server][base-windows], sur lequel le reste des couches du conteneur est appliqué. Elles peuvent également inclure des infrastructures d’application comme [Node.js][base-node] ou [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Mises à jour d’images de base

Une image de base est souvent mise à jour par le chargé de maintenance des images pour y inclure de nouvelles fonctionnalités ou améliorations relatives au système d’exploitation ou à l’infrastructure. Les correctifs de sécurité constituent une autre cause courante conduisant à une mise à jour de l’image de base.

Lorsqu’une image de base est mise à jour, vous voyez la nécessité de régénérer des images de conteneur basées sur celle-ci dans votre registre pour inclure les nouvelles fonctionnalités et les nouveaux correctifs. ACR Build vous permet de générer automatiquement des images lorsque l’image de base d’un conteneur est mise à jour.

### <a name="base-image-update-scenario"></a>Scénario de mise à jour d’une image de base

Ce didacticiel vous guide dans un scénario de mise à jour d’une image de base. [L’exemple de code][code-sample] comprend deux fichiers Dockerfile : une image d’application et une image spécifiée comme image de base. Dans les sections suivantes, vous allez créer une tâche ACR Build qui déclenche automatiquement une génération de l’image d’application lorsqu’une nouvelle version de l’image de base est envoyée vers le registre de conteneurs.

[Dockerfile-app][dockerfile-app] : petite application web Node.js qui restitue une page web statique, affichant la version de Node.js sur laquelle elle est basée. La chaîne de version est simulée, dans le sens où elle affiche le contenu d’une variable d’environnement, `NODE_VERSION`, qui est définie dans l’image de base.

[Dockerfile-base][dockerfile-base] : image spécifiée par `Dockerfile-app` comme son image de base. Elle est elle-même basée sur une image [Nœud][base-node] et inclut la variable d’environnement `NODE_VERSION`.

Dans les sections suivantes, vous allez créer une tâche de génération, mettre à jour la valeur `NODE_VERSION` dans le fichier Dockerfile d’image de base, puis utiliser ACR Build pour générer l’image de base. Lorsque ACR Build envoie la nouvelle image de base dans le registre, il déclenche automatiquement une génération de l’image de l’application. Si vous le souhaitez, vous pouvez exécuter l’image de conteneur d’application en local pour voir les différentes chaînes de version dans les images générées.

## <a name="build-base-image"></a>Générer une image de base

Commencez par générer l’image de base avec une *génération rapide* d’ACR Build. Comme indiqué dans le [premier didacticiel](container-registry-tutorial-quick-build.md) de la série, cela génère non seulement l’image, mais l’envoie vers le registre de conteneurs si la génération est réussie.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-build-task"></a>Créer une tâche de génération

Ensuite, créez une tâche de génération à l’aide de la commande [az acr build-task create][az-acr-build-task-create] :

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --build-arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

Cette tâche de génération est similaire à celle qui a été créée dans le [didacticiel précédent](container-registry-tutorial-build-task.md). Elle demande à ACR Build de déclencher une génération d’image lorsque les validations sont envoyées vers le référentiel spécifié par `--context`.

Là où elle diffère dans son comportement, c’est qu’elle déclenche également une génération de l’image lorsque son *image de base* est mise à jour. Le fichier Dockerfile spécifié par l’argument `--file`, [Dockerfile-app][dockerfile-app], prend en charge la spécification d’une image dans le même registre que son image de base :

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Lorsque vous exécutez une tâche de génération, ACR Build détecte les dépendances d’une image. Si l’image de base spécifiée dans l’instruction `FROM` se trouve dans le même registre, elle ajoute un hook pour garantir la régénération de cette image à chaque mise à jour de son image de base.

> [!NOTE]
> Pendant la préversion, ACR Build prend en charge le déclenchement d’une génération d’image dérivée uniquement si l’image de base et l’image la référençant se trouvent dans le même registre de conteneurs Azure.

## <a name="build-application-container"></a>Générer un conteneur d’application

Pour permettre à ACR Build de déterminer et de suivre les dépendances d’une image conteneur (incluant son image de base), vous **devez** d’abord déclencher sa tâche de génération **au moins une fois**.

Utilisez la commande [az acr build-task run][az-acr-build-task-run] pour déclencher manuellement la tâche de génération et générer l’image de l’application :

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Une fois la génération terminée, prenez note de **l’ID de génération** (par exemple, « aa6 ») si vous souhaitez terminer l’étape facultative suivante.

### <a name="optional-run-application-container-locally"></a>Facultatif : exécuter localement un conteneur d’application

Si vous travaillez localement (pas dans Cloud Shell) et si vous avez installé Docker, exécutez le conteneur pour voir l’application affichée dans un navigateur web avant de régénérer son image de base. Si vous utilisez Cloud Shell, ignorez cette section (Cloud Shell ne prend en charge ni `az acr login` ni `docker run`).

Tout d’abord, connectez-vous au registre de conteneurs à l’aide de la commande [az acr login][az-acr-login] :

```azurecli
az acr login --name $ACR_NAME
```

À présent, exécutez localement le conteneur avec `docker run`. Remplacez **\<build-id\>** par l’ID de génération trouvé dans la sortie de l’étape précédente (par exemple, « aa6 »).

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Accédez à http://localhost:8080 dans le navigateur ; vous devez voir le numéro de version Node.js affiché dans la page web, comme dans l’exemple suivant. Dans une étape ultérieure, vous allez augmenter le numéro de version en ajoutant un « a » à la chaîne de version.

![Capture d’écran de votre exemple d’application affichée dans le navigateur][base-update-01]

## <a name="list-builds"></a>Répertorier les générations

Dressez la liste des générations effectuées par ACR Build pour le registre :

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Si vous avez terminé le didacticiel précédent (sans supprimer le registre), vous devez pouvoir observer une sortie similaire à ce qui suit. Prenez note du nombre de générations et du dernier ID de génération afin de pouvoir comparer la sortie après avoir mis à jour l’image de base dans la section suivante.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa6         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual      2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="update-base-image"></a>Mettre à jour l’image de base

Ici, vous allez simuler un correctif d’infrastructure dans l’image de base. Modifiez **Dockerfile-base** et ajoutez un « a » après le numéro de version défini dans `NODE_VERSION` :

```Dockerfile
ENV NODE_VERSION 9.11.1a
```

Exécutez une génération rapide dans ACR Build pour générer l’image de base modifiée. Prenez note de **l’ID de génération** dans la sortie.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Lorsque la génération est terminée et qu’ACR Build a envoyé la nouvelle image de base dans le registre, il déclenche une génération de l’image de l’application. Quelques minutes peuvent être nécessaires à la tâche ACR Build créée précédemment pour déclencher la génération d’image de l’application, car elle doit détecter l’image de base qui vient d’être terminée et envoyée.

## <a name="list-builds"></a>Répertorier les générations

Maintenant que vous avez mis à jour l’image de base, répertoriez à nouveau vos générations pour les comparer à la liste précédente. Si dans un premier temps, la sortie ne diffère pas, exécutez régulièrement la commande pour afficher la nouvelle génération dans la liste.

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Le résultat ressemble à ce qui suit. Le DÉCLENCHEUR de la dernière génération exécutée doit être « Mise à jour de l’image », ce qui indique que la tâche de génération a été lancée par la génération rapide de l’image de base.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ------------  --------------------  ----------
aa8         buildhelloworld  Linux       Succeeded  Image Update  2018-05-10T20:09:52Z  00:00:45
aa7                          Linux       Succeeded  Manual        2018-05-10T20:09:17Z  00:00:40
aa6         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual        2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit    2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual        2018-05-10T19:10:14Z  00:00:55
```

Si vous souhaitez effectuer l’étape facultative suivante de l’exécution du conteneur nouvellement généré pour voir le numéro de version mis à jour, prenez note de la valeur **ID DE GÉNÉRATION** de la génération déclenchée par la mise à jour de l’image (dans la sortie précédente, il s’agit de « aa8 »).

### <a name="optional-run-newly-built-image"></a>Facultatif : exécuter l’image qui vient d’être générée

Si vous travaillez localement (pas dans Cloud Shell) et si vous avez installé Docker, exécutez la nouvelle image de l’application une fois sa génération terminée. Remplacez `<build-id>` par l’ID DE GÉNÉRATION obtenu à l’étape précédente. Si vous utilisez Cloud Shell, ignorez cette section (Cloud Shell ne prend pas en charge `docker run`).

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Accédez à http://localhost:8081 dans le navigateur ; vous devez voir le numéro de version Node.js mis à jour (avec le « a ») affiché dans la page web :

![Capture d’écran de votre exemple d’application affichée dans le navigateur][base-update-02]

Il est important de noter que si vous avez mis à jour votre image de **base** avec un nouveau numéro de version, l’image **d’application** générée en dernier affiche la nouvelle version. ACR Build a choisi la modification que vous avez apportée à l’image de base et régénéré automatiquement l’image de l’application.

## <a name="clean-up-resources"></a>Supprimer les ressources

Pour supprimer toutes les ressources créées dans cette série de didacticiels, notamment le registre de conteneurs, l’instance de conteneur, le coffre de clés et le principal du service, exécutez les commandes suivantes :

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez découvert comment utiliser une tâche de génération pour déclencher automatiquement la génération des images de conteneur lorsqu’une image de base a été mise à jour. À présent, vous allez découvrir l’authentification du registre de conteneurs.

> [!div class="nextstepaction"]
> [Authentification dans Azure Container Registry](container-registry-authentication.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png