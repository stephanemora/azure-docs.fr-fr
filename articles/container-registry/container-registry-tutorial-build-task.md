---
title: 'Didacticiel : Automatiser la génération des images de conteneur avec Azure Container Registry Build'
description: Dans ce didacticiel, vous allez découvrir comment configurer une tâche de génération pour déclencher automatiquement la génération des images de conteneur dans le cloud lorsque vous validez le code source dans un référentiel Git.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: fba499441d092f4dce09d13d607dfc5de65d98b2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
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

> [!IMPORTANT]
> ACR Build est actuellement en préversion, et sa prise en charge n’est assurée que par les registres de conteneurs Azure des régions **États-Unis de l’Est** (eastus) et **Europe de l’Ouest** (westeurope). Ces préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous souhaitez utiliser l’interface Azure CLI en local, vous devez avoir installé la version **2.0.32** d’Azure CLI ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau l’interface CLI, consultez l’article [Installation d’Azure CLI 2.0][azure-cli].

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
  "creationDate": "2018-04-18T23:14:45.905395+00:00",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "Github"
  },
  "status": "enabled",
  "tags": null,
  "timeout": null,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}

```

## <a name="test-the-build-task"></a>Tester la tâche de génération

Vous disposez maintenant d’une tâche de génération qui définit votre build. Pour tester la définition de la build, déclenchez une génération manuellement en exécutant la commande [az acr build-task run][az-acr-build-task-run] :

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Par défaut, la commande `az acr build-task run` diffuse en continu la sortie du journal vers votre console lorsque vous exécutez la commande. Ici, la sortie indique que la build **eastus2** a été mise en file d’attente et générée.

```console
$ az acr build-task run --registry mycontainerregistry --name buildhelloworld
Queued a build with build-id: eastus2.
Starting to stream the logs...
Cloning into '/root/acr-builder/src'...
time="2018-04-19T00:06:20Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
ffef1347389a008c9a8bfdf8c6a0ed78b0479894
time="2018-04-19T00:06:20Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-04-19T00:06:20Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:eastus2 ."
Sending build context to Docker daemon  182.8kB
Step 1/5 : FROM node:9-alpine
9: Pulling from library/node
Digest: sha256:bd7b9aaf77ab2ce1e83e7e79fc0969229214f9126ced222c64eab49dc0bdae90
Status: Image is up to date for node:9-alpine
 ---> aa3e171e4e95
Step 2/5 : COPY . /src
 ---> e1c04dc2993b

[...]

6e5e20cbf4a7: Layer already exists
b69680cb4898: Pushed
b54af9b858b7: Pushed
eastus2: digest: sha256:9a7b73d06077ced2a02f7462f53e31a3e51e95ea5544fbcdb01e2fef094da1b6 size: 2423
time="2018-04-19T00:06:51Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloworld:eastus2"
"["mycontainerregistry.azurecr.io/helloworld@sha256:9a7b73d06077ced2a02f7462f53e31a3e51e95ea5544fbcdb01e2fef094da1b6"]"
time="2018-04-19T00:06:51Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:bd7b9aaf77ab2ce1e83e7e79fc0969229214f9126ced222c64eab49dc0bdae90"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloworld
    tag: eastus2
    digest: sha256:9a7b73d06077ced2a02f7462f53e31a3e51e95ea5544fbcdb01e2fef094da1b6
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 6944c6bd0602f96e5fecf56ff8d66e2d268223e3

Build complete
Build ID: eastus2 was successful after 39.789138274s
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
Showing logs for the last updated build...
Build-id: eastus3

[...]

Build complete
Build ID: eastus3 was successful after 30.076988169s
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
Showing logs for the last updated build...
Build-id: eastus4

[...]

Build complete
Build ID: eastus4 was successful after 28.9587031s
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
eastus4     buildhelloworld  Linux       Succeeded  Git Commit  2018-04-20T22:50:27Z  00:00:35
eastus3     buildhelloworld  Linux       Succeeded  Manual      2018-04-20T22:47:19Z  00:00:30
eastus2     buildhelloworld  Linux       Succeeded  Manual      2018-04-20T22:46:14Z  00:00:55
eastus1                                  Succeeded  Manual      2018-04-20T22:38:22Z  00:00:55
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez découvert comment utiliser une tâche de génération pour déclencher automatiquement la génération des images de conteneur dans Azure lorsque vous validez le code source dans un référentiel Git. Passez au didacticiel suivant pour découvrir comment créer des tâches de génération qui déclenchent des générations lorsqu’une image de base d’une image conteneur est mise à jour.

> [!div class="nextstepaction"]
> [Automatiser les générations en fonction de la mise à jour d’une image de base](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
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
