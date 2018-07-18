---
title: 'Didacticiel : Automatiser la génération des images de conteneur avec Azure Container Registry Build'
description: Dans ce didacticiel, vous allez découvrir comment configurer une tâche de génération pour déclencher automatiquement la génération des images de conteneur dans le cloud lorsque vous validez le code source dans un référentiel Git.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 71ea0f489df6969f0916ac14d187e10a90a520cd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722709"
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>Didacticiel : Automatiser la génération des images de conteneur avec Azure Container Registry Build

Outre la [génération rapide](container-registry-tutorial-quick-build.md), ACR Build prend en charge la génération automatisée des images de conteneur Docker avec la *tâche de génération*. Dans ce didacticiel, vous allez utiliser Azure CLI pour créer une tâche de génération pour déclencher automatiquement la génération des images dans le cloud lorsque vous validez le code source dans un référentiel Git.

Dans ce didacticiel, la deuxième partie de la série :

> [!div class="checklist"]
> * Créer une tâche de génération
> * Tester la tâche de génération
> * Afficher l’état de la build
> * Déclencher la tâche de génération avec une validation de code

Pour suivre ce didacticiel, vous devez avoir déjà effectué les étapes du [didacticiel précédent](container-registry-tutorial-quick-build.md). Si ce n’est déjà fait, effectuez les étapes de la section [Conditions préalables](container-registry-tutorial-quick-build.md#prerequisites) du didacticiel précédent avant de continuer.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous souhaitez utiliser l’interface Azure CLI en local, vous devez avoir installé la version **2.0.32** d’Azure CLI ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau l’interface CLI, consultez l’article [Installation d’Azure CLI][azure-cli].

## <a name="prerequisites"></a>Prérequis

### <a name="get-sample-code"></a>Obtenir un exemple de code

Pour suivre ce didacticiel, vous devez avoir déjà effectué les étapes du [didacticiel précédent](container-registry-tutorial-quick-build.md) et avoir dupliqué et cloné l’exemple de référentiel. Si ce n’est déjà fait, effectuez les étapes de la section [Conditions préalables](container-registry-tutorial-quick-build.md#prerequisites) du didacticiel précédent avant de continuer.

### <a name="container-registry"></a>Registre de conteneurs

Pour effectuer ce didacticiel, vous devez disposer d’un registre de conteneurs Azure dans votre abonnement Azure. Si vous avez besoin d’un registre, consultez le [didacticiel précédent](container-registry-tutorial-quick-build.md) ou [Démarrage rapide : Créer un registre de conteneurs à l’aide de l’interface de ligne de commande Azure](container-registry-get-started-azure-cli.md).

## <a name="build-task"></a>Tâche de génération

Une tâche de génération définit les propriétés d’une génération automatisée, notamment l’emplacement du code source de l’image conteneur et l’événement qui a déclenché la génération. Lorsqu’un événement défini dans la tâche de génération se produit, par exemple une validation dans un référentiel Git, ACR Build lance une génération d’image de conteneur dans le cloud. Par défaut, ACR Build envoie une image générée dans le registre de conteneurs Azure spécifié dans la tâche.

Pour le moment, ACR Build prend en charge les déclencheurs de tâche de génération suivants :

* Validation dans un référentiel Git
* Mise à jour d’images de base

## <a name="create-a-build-task"></a>Créer une tâche de génération

Dans cette section, vous allez commencer par créer un jeton d’accès personnel GitHub à utiliser avec ACR Build. Vous allez ensuite créer une tâche de génération qui déclenche une génération lorsque le code est validé dans votre duplication du référentiel.

### <a name="create-a-github-personal-access-token"></a>Créer un jeton d’accès personnel GitHub

Pour déclencher une génération basée sur une validation dans un référentiel Git, ACR Build a besoin d’un jeton d’accès personnel pour accéder au référentiel. Suivez ces étapes pour générer un jeton d’accès personnel dans GitHub :

1. Accédez à la page de création du jeton d’accès personnel sur GitHub à l’adresse https://github.com/settings/tokens/new.
1. Entrez une brève **description** du jeton, par exemple, « Démo tâche ACR Build ».
1. Sous **référentiel**, cochez les cases **repo:status** et **public_repo**.

   ![Capture d’écran de la page de génération du jeton d’accès personnel dans GitHub][build-task-01-new-token]

1. Sélectionnez le bouton **Générer un jeton** (vous pouvez être invité à confirmer votre mot de passe).
1. Copiez et enregistrez le jeton généré dans un **emplacement sécurisé** (vous utiliserez ce jeton lorsque vous définirez une tâche de génération dans la section suivante).

   ![Capture d’écran du jeton d’accès personnel généré dans GitHub][build-task-02-generated-token]

### <a name="create-the-build-task"></a>Créer la tâche de génération

Maintenant que vous avez terminé les étapes requises pour permettre à ACR Build de lire l’état de validation et de créer des Webhooks dans un référentiel, vous pouvez créer une tâche de génération qui déclenche une génération d’image de conteneur lors de la validation dans le référentiel.

Tout d’abord, renseignez ces variables d’environnement shell avec des valeurs appropriées pour votre environnement. Cela n’est pas impératif, mais facilite un peu l’exécution des commandes multilignes de l’interface de ligne de commande Azure de ce didacticiel. Si vous ne les renseignez pas, vous devrez remplacer manuellement chaque valeur à chaque fois qu’elle apparaît dans les exemples de commandes.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

À présent, créez la tâche de génération en exécutant la commande [az acr build-task create][az-acr-build-task-create] suivante :

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

Cette tâche de génération spécifie que tout code temporel est validé dans la branche *maîtresse* du référentiel spécifié par `--context` et qu’ACR Build va générer l’image conteneur à partir du code de cette branche. L’argument `--image` spécifie une valeur paramétrable de `{{.Build.ID}}` pour la partie « version » de la balise de l’image, garantissant ainsi que l’image générée est en corrélation avec une build spécifique et qu’elle est référencée de façon unique.

La sortie d’une commande [az acr build-task create][az-acr-build-task-create] réussie se présente ainsi :

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-05-10T19:34:48.086776+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/mycontainerregistry/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "properties": {
    "additionalProperties": {
      "imageName": null
    },
    "baseImageDependencies": null,
    "baseImageTrigger": "Runtime",
    "branch": "master",
    "buildArguments": [],
    "contextPath": null,
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Build.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "provisioningState": "Succeeded",
    "type": "Docker"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "mycontainerregistry",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "GitHub"
  },
  "status": "Enabled",
  "tags": null,
  "timeout": 3600,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}
```

## <a name="test-the-build-task"></a>Tester la tâche de génération

Vous disposez maintenant d’une tâche de génération qui définit votre build. Pour tester la définition de la build, déclenchez une génération manuellement en exécutant la commande [az acr build-task run][az-acr-build-task-run] :

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Par défaut, la commande `az acr build-task run` diffuse en continu la sortie du journal vers votre console lorsque vous exécutez la commande. Ici, la sortie indique que la build **aa2** a été mise en file d’attente et générée.

```console
$ az acr build-task run --registry $ACR_NAME --name buildhelloworld
Queued a build with build ID: aa2
Waiting for a build agent...
time="2018-05-10T19:37:17Z" level=info msg="Running command git clone https://x-access-token:*************@github.com/gituser/acr-build-helloworld-node /root/acr-builder/src"
Cloning into '/root/acr-builder/src'...
time="2018-05-10T19:37:17Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
920f16cfafa36d0bc3f397c3dd48185a03499404
time="2018-05-10T19:37:17Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-05-10T19:37:17Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:aa2 ."
Sending build context to Docker daemon  209.9kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 48a7735fa94e

[...]

26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
aa2: digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89 size: 1367
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistrtyy.azurecr.io/helloworld:aa2"
"["mycontainerregistrtyy.azurecr.io/helloworld@sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89"]"
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistrtyy.azurecr.io
    repository: helloworld
    tag: aa2
    digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 920f16cfafa36d0bc3f397c3dd48185a03499404

Build complete
Build ID: aa2 was successful after 46.491407373s
```

## <a name="view-build-status"></a>Afficher l’état de la build

Il peut s’avérer utile d’afficher l’état d’une génération en cours que vous n’avez pas déclenchée manuellement. Exemple : lors de la résolution des problèmes de générations déclenchées par les validations de code source. Dans cette section, vous allez déclencher une génération manuelle, mais supprimer le comportement par défaut de diffusion en continu du journal de génération vers votre console. Ensuite, vous utiliserez la commande `az acr build-task logs` pour surveiller la génération en cours.

Tout d’abord, déclenchez manuellement une génération comme vous l’avez fait précédemment, mais spécifiez l’argument `--no-logs` pour supprimer la journalisation dans votre console :

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

Ensuite, utilisez la commande `az build-task logs` pour afficher le journal de la génération en cours d’exécution :

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

Le journal de la génération en cours d’exécution est diffusé en continu vers votre console et doit ressembler à la sortie suivante (il est présenté ici tronqué) :

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa3

[...]

Build complete
Build ID: aa3 was successful after 1m14.26397548s
```

## <a name="trigger-a-build-with-a-commit"></a>Déclencher une génération avec une validation

Maintenant que vous avez testé la tâche de génération en l’exécutant manuellement, déclenchez-la automatiquement avec une modification du code source.

Vérifiez tout d’abord que le répertoire contenant votre clone local du [référentiel][sample-repo] est actif :

```azurecli-interactive
cd acr-build-helloworld-node
```

Exécutez alors les commandes suivantes pour créer, valider et envoyer un nouveau fichier vers votre duplication du référentiel sur GitHub :

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

Vous pouvez être invité à fournir vos informations d’identification GitHub lorsque vous exécutez la commande `git push`. Indiquez votre nom d’utilisateur GitHub et entrez le jeton d’accès personnel (PAT) que vous avez créé précédemment pour le mot de passe.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Une fois que vous avez envoyé une validation vers votre référentiel, le Webhook créé par ACR Build déclenche et démarre une génération dans Azure Container Registry. Affichez les journaux de la génération en cours d’exécution pour vérifier et surveiller l’avancement de la génération :

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

La sortie est similaire à ce qui suit, affichant la génération en cours d’exécution (ou exécutée en dernier lieu) :

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa4

[...]

Build complete
Build ID: aa4 was successful after 39.164385024s
```

## <a name="list-builds"></a>Répertorier les générations

Pour afficher la liste des générations effectuées par ACR Build pour votre registre, exécutez la commande [az acr build-task list-builds][az-acr-build-task-list-builds] :

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

La sortie de la commande doit ressembler à ceci : Les générations exécutées par ACR Build sont affichées, et « Validation Git » s’affiche dans la colonne DÉCLENCHEUR de la génération la plus récente :

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez découvert comment utiliser une tâche de génération pour déclencher automatiquement la génération des images de conteneur dans Azure lorsque vous validez le code source dans un référentiel Git. Passez au didacticiel suivant pour découvrir comment créer des tâches de génération qui déclenchent des générations lorsqu’une image de base d’une image conteneur est mise à jour.

> [!div class="nextstepaction"]
> [Automatiser les générations en fonction de la mise à jour d’une image de base](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
