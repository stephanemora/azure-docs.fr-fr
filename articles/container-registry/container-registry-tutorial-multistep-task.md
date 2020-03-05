---
title: Tutoriel - Tâche ACR à plusieurs étapes
description: Dans ce tutoriel, vous allez découvrir comment configurer une tâche Azure Container Registry Task pour déclencher automatiquement un workflow à plusieurs étapes afin de créer, exécuter et envoyer (push) des images de conteneur dans le cloud lorsque vous validez le code source dans un référentiel Git.
ms.topic: tutorial
ms.date: 05/09/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 7d84770f1b945ff47cb4e9118d9c342e67118722
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249917"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Tutoriel : exécuter un workflow de conteneur à plusieurs étapes dans le cloud lorsque vous validez le code source

En plus d'une [tâche rapide](container-registry-tutorial-quick-task.md), ACR Tasks prend en charge les workflows à plusieurs étapes avec plusieurs conteneurs qui peuvent se déclencher automatiquement lorsque vous validez le code source dans un référentiel Git. 

Dans ce tutoriel, vous allez apprendre à utiliser des exemples de fichiers YAML pour définir les tâches à plusieurs étapes permettant de générer, d'exécuter et d'envoyer (push) une ou plusieurs images de conteneur vers un registre lorsque vous validez le code source. Pour créer une tâche qui automatise une seule génération d'image lors de la validation du code, consultez [Tutoriel : automatiser la génération des images de conteneur dans le cloud lorsque vous validez le code source](container-registry-tutorial-build-task.md). Pour une vue d’ensemble d’ACR Tasks, consultez [Automatiser les mises à jour correctives du système d’exploitation et de l'infrastructure avec ACR Tasks](container-registry-tasks-overview.md).

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Définir une tâche à plusieurs étapes à l’aide d’un fichier YAML
> * Créer une tâche
> * Ajouter éventuellement des informations d’identification à la tâche pour activer l’accès à un autre registre
> * Tester la tâche
> * Affichage de l’état de la tâche
> * Déclencher la tâche avec une validation de code

Pour suivre ce didacticiel, vous devez avoir déjà effectué les étapes du [didacticiel précédent](container-registry-tutorial-quick-task.md). Si ce n’est déjà fait, effectuez les étapes de la section [Conditions préalables](container-registry-tutorial-quick-task.md#prerequisites) du didacticiel précédent avant de continuer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous voulez utiliser l’interface Azure CLI en local, vous devez avoir installé la version **2.0.62** d’Azure CLI ou ultérieure, et être connecté avec [az login][az-login]. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau l’interface CLI, consultez l’article [Installer Azure CLI 2.0][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Créer une tâche à plusieurs étapes

Maintenant que vous avez terminé les étapes requises pour permettre à ACR Tasks de lire l’état de validation et de créer des webhooks dans un référentiel, créez une tâche à plusieurs étapes qui déclenche la création, l'exécution et l'envoi (push) d'une image de conteneur.

### <a name="yaml-file"></a>Fichier YAML

Vous définissez les étapes d’une tâche à plusieurs étapes dans un [fichier YAML](container-registry-tasks-reference-yaml.md). Le premier exemple de tâche à plusieurs étapes de ce tutoriel est défini dans le fichier `taskmulti.yaml`, qui se trouve à la racine du référentiel GitHub que vous avez cloné :

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Cette tâche à plusieurs étapes effectue ce qui suit :

1. Elle exécute une étape `build` pour générer une image à partir du fichier Dockerfile du répertoire de travail. L'image cible `Run.Registry`, le registre où la tâche est exécutée, et est marquée avec un ID d'exécution ACR Tasks unique. 
1. Elle exécute une étape `cmd` pour exécuter l’image dans un conteneur temporaire. Cet exemple démarre un conteneur à exécution longue en arrière-plan, renvoie l’ID de conteneur, puis arrête le conteneur. Dans un scénario réel, vous pouvez inclure des étapes pour tester la bonne exécution du conteneur.
1. À l'étape `push`, elle envoie (push) l’image générée dans le registre d’exécution.

### <a name="task-command"></a>Commande de la tâche

Tout d’abord, renseignez ces variables d’environnement shell avec des valeurs appropriées pour votre environnement. Cette étape n’est pas strictement obligatoire, mais facilite un peu l’exécution des commandes multilignes de l’interface de ligne de commande Azure de ce didacticiel. Si vous ne renseignez pas ces variables d’environnement, vous devrez remplacer manuellement chaque valeur à chaque fois qu’elle apparaît dans les exemples de commandes.

[![Lancement de l’incorporation](https://shell.azure.com/images/launchcloudshell.png "Lancement d’Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Maintenant, créez la tâche en exécutant la commande [az acr task create][az-acr-task-create] suivante :

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Cette tâche spécifie que tout code temporel est validé dans la branche *maîtresse* du référentiel spécifié par `--context` et qu’ACR Tasks va exécuter la tâche à plusieurs étapes à partir du code de cette branche. Le fichier YAML spécifié par `--file` à la racine du référentiel définit les étapes. 

La sortie d’une commande [az acr task create][az-acr-task-create] réussie se présente ainsi :

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>Tester le workflow à plusieurs étapes

Pour tester la tâche à plusieurs étapes, déclenchez-la manuellement en exécutant la commande [az acr task run][az-acr-task-run] :

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Par défaut, la commande `az acr task run` diffuse en continu la sortie du journal vers votre console lorsque vous exécutez la commande. La sortie indique la progression de l’exécution de chaque étape de la tâche. La sortie ci-dessous est condensée pour afficher les principales étapes.

```console
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Déclencher une génération avec une validation

Maintenant que vous avez testé la tâche en l’exécutant manuellement, déclenchez-la automatiquement avec une modification du code source.

Vérifiez d’abord que vous êtes dans répertoire contenant votre clone local du [dépôt][sample-repo] :

```console
cd acr-build-helloworld-node
```

Exécutez alors les commandes suivantes pour créer, valider et envoyer un nouveau fichier vers votre duplication du référentiel sur GitHub :

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Vous pouvez être invité à fournir vos informations d’identification GitHub lorsque vous exécutez la commande `git push`. Indiquez votre nom d’utilisateur GitHub et entrez le jeton d’accès personnel (PAT) que vous avez créé précédemment pour le mot de passe.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Une fois que vous avez envoyé (push) une validation vers votre référentiel, le webhook créé par ACR Tasks déclenche et démarre la tâche dans Azure Container Registry. Affichez les journaux d’activité de la tâche en cours d’exécution pour vérifier et surveiller l’avancement de la génération :

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

La sortie est similaire à ce qui suit, affichant la tâche en cours d’exécution (ou exécutée en dernier lieu) :

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Répertorier les générations

Pour afficher la liste des exécutions de tâche effectuées par ACR Tasks pour votre registre, exécutez la commande [az acr task list-runs][az-acr-task-list-runs] :

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

La sortie de la commande doit ressembler à ceci : Les exécutions d’ACR Tasks sont affichées, et « Validation Git » s’affiche dans la colonne DÉCLENCHEUR de la dernière tâche :

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Créer une tâche à plusieurs étapes avec plusieurs registres

Par défaut, ACR Tasks est autorisé à envoyer (push) ou extraire des images du registre dans lequel la tâche s’exécute. Vous souhaiterez peut-être exécuter une tâche à plusieurs étapes qui cible un ou plusieurs registres en plus du registre d’exécution. Par exemple, vous serez peut-être amené à générer des images dans un registre, puis à les stocker avec des balises différentes dans un second registre accessible par un système de production. Cet exemple vous montre comment créer une telle tâche et fournir les informations d’identification pour un autre registre.

Si vous ne disposez pas d’un second registre, créez-en un pour les besoins de cet exemple. Si vous avez besoin d’un registre, consultez le [didacticiel précédent](container-registry-tutorial-quick-task.md) ou le [Démarrage rapide : Créer un registre de conteneur à l’aide de l’interface de ligne de commande Azure](container-registry-get-started-azure-cli.md).

Pour créer la tâche, vous devez disposer du nom du serveur de connexion au registre, qui se présente comme suit : *mycontainerregistrydate.azurecr.io* (en minuscules). Dans cet exemple, vous utilisez le second registre pour stocker les images marquées par date de génération.

### <a name="yaml-file"></a>Fichier YAML

Le second exemple de tâche à plusieurs étapes de ce tutoriel est défini dans le fichier `taskmulti-multiregistry.yaml`, qui se trouve à la racine du référentiel GitHub que vous avez cloné :

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Cette tâche à plusieurs étapes effectue ce qui suit :

1. Elle exécute deux étapes `build` pour générer des images à partir du fichier Dockerfile du répertoire de travail :
    * La première cible `Run.Registry`, le registre où la tâche est exécutée, et est marquée avec un ID d'exécution ACR Tasks. 
    * La seconde cible le registre identifié par la valeur `regDate`, que vous définissez lorsque vous créez la tâche (ou fournissez via un fichier `values.yaml` externe transmis à `az acr task create`). Cette image est marquée avec la date d’exécution.
1. Elle exécute une étape `cmd` pour exécuter un des conteneurs générés. Cet exemple démarre un conteneur à exécution longue en arrière-plan, renvoie l’ID de conteneur, puis arrête le conteneur. Dans un scénario réel, vous pouvez tester la bonne exécution du conteneur.
1. Dans une étape `push`, elle envoie (push) les images générées, la première au registre d’exécution, la seconde au registre identifié par `regDate`.

### <a name="task-command"></a>Commande de la tâche

En utilisant les variables d’environnement shell définies précédemment, créez la tâche en exécutant la commande [az acr task create][az-acr-task-create] suivante. Remplacez le nom de votre registre par *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Ajouter les informations d’identification de la tâche

Pour envoyer (push) les images dans le registre identifié par la valeur `regDate`, utilisez la commande [az acr task credential add][az-acr-task-credential-add] afin d’ajouter les informations d’identification de ce registre à la tâche.

Pour cet exemple, nous vous recommandons de créer un [principal de service](container-registry-auth-service-principal.md) avec accès au registre limité au rôle *AcrPush*. Pour créer le principal de service, consultez ce [script Azure CLI](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Transmettez l’ID d’application du principal de service et le mot de passe dans la commande `az acr task credential add` suivante :

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

L’interface CLI renvoie le nom du serveur de connexion au registre que vous avez ajouté.

### <a name="test-the-multi-step-workflow"></a>Tester le workflow à plusieurs étapes

Comme dans l’exemple précédent, pour tester la tâche à plusieurs étapes, déclenchez-la manuellement en exécutant la commande [az acr task run][az-acr-task-run]. Pour déclencher la tâche avec une validation au référentiel Git, consultez la section [Déclencher une génération avec une validation](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Par défaut, la commande `az acr task run` diffuse en continu la sortie du journal vers votre console lorsque vous exécutez la commande. Comme précédemment, la sortie indique la progression de l’exécution de chaque étape de la tâche. La sortie est condensée pour montrer les principales étapes.

Sortie :

```console
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez découvert comment créer des tâches à plusieurs étapes avec plusieurs conteneurs qui se déclenchent automatiquement lorsque vous validez le code source dans un référentiel Git. Pour les fonctionnalités avancées des tâches à plusieurs étapes, y compris l’exécution d'étapes parallèles et dépendantes, consultez la [documentation YAML ACR Tasks](container-registry-tasks-reference-yaml.md). Passez au didacticiel suivant pour découvrir comment créer des tâches qui déclenchent des générations lorsqu’une image de base d’une image conteneur est mise à jour.

> [!div class="nextstepaction"]
> [Automatiser les générations en fonction de la mise à jour d’une image de base](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
