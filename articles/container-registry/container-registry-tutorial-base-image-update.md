---
title: Tutoriel – Déclencher la génération d’images lors de la mise à jour d’images de base
description: Dans ce didacticiel, vous allez découvrir comment configurer une tâche Azure Container Registry pour déclencher automatiquement la génération des images conteneur dans le cloud lorsqu’une image de base est mise à jour.
ms.topic: tutorial
ms.date: 08/12/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: b89bf0364165822368647b4c5b773bf422902aec
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456133"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Didacticiel : Automatiser la génération des images conteneur quand une image de base est mise à jour dans un registre de conteneurs Azure 

ACR Tasks prend en charge l’exécution des générations automatisées lorsque l’image de base d’un conteneur est mise à jour, par exemple lorsque vous retouchez le système d’exploitation ou l’infrastructure d’application dans l’une de vos images de base. Dans ce didacticiel, vous allez découvrir comment créer une tâche dans ACR Tasks qui déclenche une génération dans le cloud lorsque l’image de base d’un conteneur a été envoyée vers votre registre.

Dans ce didacticiel, le dernier de la série, vous allez apprendre à :

> [!div class="checklist"]
> * Générer l’image de base
> * Créer une tâche de génération d’une image d’application
> * Mettre à jour l’image de base pour déclencher la génération d’une tâche d’image d’application
> * Afficher la tâche déclenchée
> * Vérifier l’image d’application mise à jour

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous souhaitez utiliser l’interface Azure CLI en local, vous devez avoir installé la version **2.0.46** d’Azure CLI ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau l’interface CLI, consultez l’article [Installer Azure CLI 2.0][azure-cli].

## <a name="prerequisites"></a>Prérequis

### <a name="complete-the-previous-tutorials"></a>Terminer les didacticiels précédents

Pour suivre ce didacticiel, vous devez avoir déjà effectué les étapes des deux premiers didacticiels de la série, dans lesquels vous avez appris à :

* Créer un registre de conteneurs Azure
* Dupliquer l’exemple de référentiel
* Cloner l’exemple de référentiel
* Créer un jeton d’accès personnel GitHub

Si ce n’est déjà fait, complétez les deux premiers didacticiels avant de continuer :

[Générer des images de conteneur dans le cloud avec Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Automatiser la génération des images de conteneur avec Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>Configurer l’environnement

Renseignez ces variables d’environnement shell avec des valeurs appropriées pour votre environnement. Cette étape n’est pas strictement obligatoire, mais facilite un peu l’exécution des commandes multilignes de l’interface de ligne de commande Azure de ce didacticiel. Si vous ne renseignez pas ces variables d’environnement, vous devrez remplacer manuellement chaque valeur à chaque fois qu’elle apparaît dans les exemples de commandes.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Images de base

Les fichiers Dockerfile qui définissent la plupart des images de conteneur spécifient une image parente, souvent appelée *image de base*, sur laquelle les autres images sont basées. Les images de base contiennent en général le système d’exploitation, par exemple [Alpine Linux][base-alpine] ou [Windows Nano Server][base-windows], sur lequel le reste des couches du conteneur est appliqué. Elles peuvent également inclure des infrastructures d’application comme [Node.js][base-node] ou [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Mises à jour d’images de base

Une image de base est souvent mise à jour par le chargé de maintenance des images pour y inclure de nouvelles fonctionnalités ou améliorations relatives au système d’exploitation ou à l’infrastructure. Les correctifs de sécurité constituent une autre cause courante conduisant à une mise à jour de l’image de base.

Lorsqu’une image de base est mise à jour, vous voyez la nécessité de régénérer des images de conteneur basées sur celle-ci dans votre registre pour inclure les nouvelles fonctionnalités et les nouveaux correctifs. ACR Tasks vous permet de générer automatiquement des images lorsque l’image de base d’un conteneur est mise à jour.

### <a name="tasks-triggered-by-a-base-image-update"></a>Tâches déclenchées par la mise à jour d’une image de base

* Pour les générations d’images à partir d’un fichier Dockerfile, une tâche ACR détecte les dépendances sur les images de base aux emplacements suivants :

  * Le registre de conteneurs Azure dans lequel s’exécute la tâche
  * Un autre registre de conteneurs Azure dans la même région 
  * Un référentiel public dans Docker Hub 
  * Un référentiel public dans le registre de conteneurs Microsoft

   Si l’image de base spécifiée dans l’instruction `FROM` dans l’un de ces emplacements, la tâche ACR ajoute un hook pour garantir la régénération de l’image à chaque mise à jour de son image de base.

* Actuellement, les tâches ACR suivent uniquement les mises à jour des images de base pour les images d’application (*runtime*). Elles ne suivent pas les mises à jour des images de base pour les images intermédiaires (*au moment de la génération*) utilisées dans des Dockerfiles multiétapes.  

* Lorsque vous créez une tâche ACR avec la commande [az acr task create][az-acr-task-create], par défaut la tâche est *activée* se déclencher lors d’une mise à jour de l’image de base. Cela signifie que la propriété `base-image-trigger-enabled` est définie sur True. Si vous voulez désactiver ce comportement dans une tâche, mettez à jour la propriété sur False. Par exemple, exécutez la commande suivante [az acr task update][az-acr-task-update] :

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* Pour permettre à une tâche ACR de déterminer et de suivre les dépendances d’une image conteneur (incluant son image de base), vous devez d’abord déclencher sa tâche **au moins une fois**. Par exemple, déclenchez la tâche manuellement en utilisant la commande [az acr task run][az-acr-task-run].

* Pour déclencher une tâche lors de la mise à jour de l’image de base, cette dernière doit posséder une balise *stable*, telle que `node:9-alpine`. Cette catégorisation est typique pour une image de base qui est mise à jour avec des correctifs de système d’exploitation et de framework vers une dernière version stable. Si l’image de base est mis à jour avec une nouvelle balise de version, elle ne déclenche pas de tâche. Pour plus d’informations, consultez la catégorisation d’image, voir [Conseils sur les meilleures pratiques](container-registry-image-tag-version.md). 

### <a name="base-image-update-scenario"></a>Scénario de mise à jour d’une image de base

Ce didacticiel vous guide dans un scénario de mise à jour d’une image de base. [L’exemple de code][code-sample] comprend deux fichiers Dockerfile : une image d’application et une image spécifiée comme image de base. Dans les sections suivantes, vous allez créer une tâche ACR Tasks qui déclenche automatiquement une génération de l’image d’application lorsqu’une nouvelle version de l’image de base est envoyée vers le même registre de conteneurs.

[Dockerfile-app][dockerfile-app] : Une petite application web Node.js qui restitue une page web statique en affichant la version de Node.js sur laquelle elle est basée. La chaîne de version est simulée : elle affiche le contenu d’une variable d’environnement, `NODE_VERSION`, qui est définie dans l’image de base.

[Dockerfile-base][dockerfile-base] : Image spécifiée par `Dockerfile-app` comme son image de base. Elle est elle-même basée sur une image [Nœud][base-node] et inclut la variable d’environnement `NODE_VERSION`.

Dans les sections suivantes, vous allez créer une tâche, mettre à jour la valeur `NODE_VERSION` dans le fichier Dockerfile d’image de base, puis utiliser ACR Tasks pour générer l’image de base. Lorsqu’ACR Tasks envoie la nouvelle image de base dans le registre, il déclenche automatiquement une génération de l’image de l’application. Si vous le souhaitez, vous pouvez exécuter l’image de conteneur d’application en local pour voir les différentes chaînes de version dans les images générées.

Dans ce tutoriel, votre tâche ACR génère et envoie (push) une image conteneur d’application spécifiée dans un Dockerfile. ACR Tasks peut également exécuter des [tâches multiétapes](container-registry-tasks-multi-step.md) à l’aide d’un fichier YAML pour définir les étapes permettant de générer, d’envoyer en mode push et éventuellement de tester plusieurs conteneurs.

## <a name="build-the-base-image"></a>Générer l’image de base

Commencez par générer l’image de base avec une *tâche rapide* d’ACR Tasks. Comme indiqué dans le [premier didacticiel](container-registry-tutorial-quick-task.md) de la série, ce processus génère non seulement l’image, mais l’envoie vers le registre de conteneurs si la génération est réussie.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Créer une tâche

Créez ensuite une tâche à l’aide de la commande [az acr task create][az-acr-task-create] :

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Si vous avez créé précédemment des tâches pendant la préversion avec la commande `az acr build-task`, ces tâches doivent être recréées à l’aide de la commande [az acr task][az-acr-task].

Cette tâche est similaire à la tâche rapide créée dans le [didacticiel précédent](container-registry-tutorial-build-task.md). Elle demande à ACR Tasks de déclencher une génération d’image lorsque les validations sont envoyées vers le référentiel spécifié par `--context`. Alors que le Dockerfile utilisé pour construire l’image dans le tutoriel précédent spécifie une image de base publique (`FROM node:9-alpine`), le Dockerfile dans cette tâche, [Dockerfile-app][dockerfile-app], spécifie une image de base dans le même registre :

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Cette configuration facilite la simulation d’un correctif de framework dans l’image de base ultérieurement dans ce tutoriel.

## <a name="build-the-application-container"></a>Générer le conteneur d’application

Utilisez la commande [az acr task run][az-acr-task-run] pour déclencher manuellement la tâche et générer l’image de l’application. Cette étape permet de s’assurer que la tâche suit la dépendance de l’image de l’application par rapport à l’image de base.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Une fois la tâche terminée, prenez note de l’**ID d’exécution** (par exemple, « da6 ») si vous souhaitez terminer l’étape facultative suivante.

### <a name="optional-run-application-container-locally"></a>Facultatif : Exécuter localement un conteneur d’application

Si vous travaillez localement (pas dans Cloud Shell) et si vous avez installé Docker, exécutez le conteneur pour voir l’application affichée dans un navigateur web avant de régénérer son image de base. Si vous utilisez Cloud Shell, ignorez cette section (Cloud Shell ne prend en charge ni `az acr login` ni `docker run`).

Tout d’abord, connectez-vous au registre de conteneurs à l’aide de la commande [az acr login][az-acr-login] :

```azurecli
az acr login --name $ACR_NAME
```

À présent, exécutez localement le conteneur avec `docker run`. Remplacez **\<run-id\>** par l’ID d’exécution trouvé dans la sortie de l’étape précédente (par exemple, « da6 »). Cet exemple nomme le conteneur `myapp` et inclut le paramètre `--rm` pour supprimer le conteneur lorsque vous l’arrêtez.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Accédez à `http://localhost:8080` dans le navigateur ; vous devez voir le numéro de version Node.js affiché dans la page web, comme dans l’exemple suivant. Dans une étape ultérieure, vous allez augmenter le numéro de version en ajoutant un « a » à la chaîne de version.

![Capture d’écran de votre exemple d’application affichée dans le navigateur][base-update-01]

Pour arrêter et supprimer le conteneur, exécutez la commande suivante :

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Répertorier les builds

Répertoriez ensuite les exécutions de tâche effectuées par ACR Tasks pour votre registre à l’aide de la commande [az acr task list-runs][az-acr-task-list-runs] :

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Si vous avez terminé le didacticiel précédent (sans supprimer le registre), vous devez pouvoir observer une sortie similaire à ce qui suit. Prenez note du nombre d’exécutions de tâches et du dernier ID d’exécution afin de pouvoir comparer la sortie après avoir mis à jour l’image de base dans la section suivante.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>Mettre à jour l’image de base

Ici, vous allez simuler un correctif d’infrastructure dans l’image de base. Modifiez **Dockerfile-base** et ajoutez un « a » après le numéro de version défini dans `NODE_VERSION` :

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

Exécutez une tâche rapide dans ACR Tasks pour générer l’image de base modifiée. Prenez note de l’**ID d’exécution** dans la sortie.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Lorsque la génération est terminée et qu’ACR Tasks a envoyé la nouvelle image de base dans le registre, il déclenche une génération de l’image de l’application. Quelques minutes peuvent être nécessaires à la tâche créée précédemment pour déclencher la génération d’image de l’application, car elle doit détecter l’image de base qui vient d’être générée et envoyée.

## <a name="list-updated-build"></a>Répertorier le build mis à jour

Maintenant que vous avez mis à jour l’image de base, répertoriez à nouveau vos exécutions de tâches pour les comparer à la liste précédente. Si dans un premier temps, la sortie ne diffère pas, exécutez régulièrement la commande pour afficher la nouvelle exécution de tâche dans la liste.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Le résultat ressemble à ce qui suit. Le DÉCLENCHEUR de la dernière génération exécutée doit être « Mise à jour de l’image », ce qui indique que la tâche a été lancée par la tâche rapide de l’image de base.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

Si vous souhaitez effectuer l’étape facultative suivante de l’exécution du conteneur nouvellement généré pour voir le numéro de version mis à jour, prenez note de la valeur **ID D’EXÉCUTION** de la génération déclenchée par la mise à jour de l’image (dans la sortie précédente, il s’agit de « da8 »).

### <a name="optional-run-newly-built-image"></a>Facultatif : Exécuter l’image qui vient d’être générée

Si vous travaillez localement (pas dans Cloud Shell) et si vous avez installé Docker, exécutez la nouvelle image de l’application une fois sa génération terminée. Remplacez `<run-id>` par l’ID D’EXÉCUTION obtenu à l’étape précédente. Si vous utilisez Cloud Shell, ignorez cette section (Cloud Shell ne prend pas en charge `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Accédez à http://localhost:8081 dans le navigateur ; vous devez voir le numéro de version Node.js mis à jour (avec le « a ») affiché dans la page web :

![Capture d’écran de votre exemple d’application affichée dans le navigateur][base-update-02]

Il est important de noter que si vous avez mis à jour votre image de **base** avec un nouveau numéro de version, l’image **d’application** générée en dernier affiche la nouvelle version. ACR Tasks a choisi la modification que vous avez apportée à l’image de base et régénéré automatiquement l’image de l’application.

Pour arrêter et supprimer le conteneur, exécutez la commande suivante :

```bash
docker stop updatedapp
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer toutes les ressources créées dans cette série de didacticiels, notamment le registre de conteneurs, l’instance de conteneur, le coffre de clés et le principal du service, exécutez les commandes suivantes :

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez découvert comment utiliser une tâche pour déclencher automatiquement la génération des images de conteneur lorsqu’une image de base a été mise à jour. À présent, passez au tutoriel suivant pour découvrir comment déclencher des tâches selon une planification définie.

> [!div class="nextstepaction"]
> [Exécuter une tâche selon une planification](container-registry-tasks-scheduled.md)

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
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
