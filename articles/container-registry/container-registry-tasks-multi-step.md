---
title: Automatiser la compilation, les tests et les correctifs d’images à l’aide des tâches multiétapes d’Azure Container Registry
description: Présentation des tâches multiétapes, une fonctionnalité d’ACR Tasks dans Azure Container Registry qui assure des flux de travail basés sur des tâches permettant de compiler, de tester et de corriger des images de conteneurs dans le cloud.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: danlep
ms.openlocfilehash: cdabafc4f70b08076820e7e0d39300b3eb0bc1e7
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856715"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Exécuter des tâches de compilation, de test et de correction multiétapes dans les tâches ACR

Les tâches multiétapes renforcent la capacité d’ACR Tasks à compiler et envoyer des images en une fois grâce à des flux de travail à plusieurs étapes avec plusieurs conteneurs. Utilisez des tâches multiétapes pour compiler et envoyer plusieurs images, en série ou en parallèle, puis exécutez ces images sous forme de commandes dans une même exécution de tâche. Chaque étape définit une opération de compilation ou d’envoi d’image de conteneur. Elle peut également définir l’exécution d’un conteneur. Chaque étape d’une tâche multiétapes utilise un conteneur comme environnement d’exécution.

> [!IMPORTANT]
> Si vous avez créé précédemment des tâches pendant la préversion avec la commande `az acr build-task`, ces tâches doivent être recréées à l’aide de la commande [az acr task][az-acr-task].

Par exemple, vous pouvez exécuter une tâche avec des étapes qui automatisent ce qui suit :

1. Compiler une image d’application web
1. Exécuter le conteneur d’application web
1. Compiler une image test d’application web
1. Exécuter le conteneur test d’application web qui effectue des tests sur le conteneur d’application en cours d’exécution
1. Si les tests réussissent, compilez un package d’archivage du graphique Helm
1. Effectuer un `helm upgrade` à l’aide du nouveau package d’archivage du graphique Helm

Toutes les étapes sont effectuées dans Azure en déchargeant les ressources de calcul d’Azure et en vous évitant la gestion de l’infrastructure. Outre votre registre de conteneurs Azure, vous ne payez que pour les ressources que vous utilisez. Pour en savoir plus sur la tarification, consultez la section **Compiler un conteneur** dans [Tarification Azure Container Registry][pricing].

> [!IMPORTANT]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Les préversions sont à votre disposition, à la condition d’accepter les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="common-task-scenarios"></a>Scénarios de tâches courants

Les tâches multiétapes permettent de réaliser des scénarios comme les suivants :

* Compiler, baliser et envoyer une ou plusieurs images de conteneurs, en série ou en parallèle.
* Exécuter et capturer les résultats des tests unitaires et de la couverture du code.
* Exécuter et capturer des tests fonctionnels. ACR Tasks prend en charge l’exécution de plusieurs conteneurs en exécutant toute une série de requêtes entre eux.
* Effectuer une exécution basée sur des tâches, notamment des étapes avant/après la compilation de l’image d’un conteneur.
* Déployer un ou plusieurs conteneurs à l’aide du moteur de déploiement de votre choix dans votre environnement cible.

## <a name="multi-step-task-definition"></a>Définition des tâches multiétapes

Dans ACR Tasks, une tâche multiétapes est définie comme une série d’étapes dans un fichier YAML. Chaque étape peut spécifier des dépendances en fonction de la réussite d’une ou de plusieurs étapes précédentes. Les types d’étape de tâches suivants sont disponibles :

* [`build`](container-registry-tasks-reference-yaml.md#build) : Compiler une ou plusieurs images de conteneurs à l’aide de la syntaxe `docker build` usuelle, en série ou en parallèle.
* [`push`](container-registry-tasks-reference-yaml.md#push) : Envoyer les images compilées dans un registre de conteneurs. Les registres privés comme Azure Container Registry sont pris en charge, de même que le hub Docker public.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd) : Exécuter un conteneur de manière à ce qu’il fonctionne comme une fonction dans le cadre de la tâche à exécuter. Vous pouvez transférer des paramètres au conteneur `[ENTRYPOINT]`, et spécifier des propriétés comme env, detach, et d’autres paramètres `docker run` usuels. Le type d’étape `cmd` permet d’effectuer des tests unitaires et fonctionnels en exécutant les conteneurs en simultané.

Les tâches multiétapes peuvent être aussi simples que compiler et envoyer une image unique :

```yaml
version: 1.0-preview-1
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Ou plus complexe, comme cette tâche qui comprend les étapes de compilation, de test, de package Helm et de déploiement Helm :

```yaml
version: 1.0-preview-1
steps:
  - id: build-web
    build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
    when: ["-"]
  - id: build-tests
    build -t {{.Run.Registry}}/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["{{.Run.Registry}}/helloworld:{{.Run.ID}}"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
  - id: funcTests
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
    env: ["host=helloworld:80"]
  - cmd: {{.Run.Registry}}/functions/helm package --app-version {{.Run.ID}} -d ./helm ./helm/helloworld/
  - cmd: {{.Run.Registry}}/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image={{.Run.Registry}}/helloworld:{{.Run.ID}}
```

## <a name="run-a-sample-task"></a>Exécuter un exemple de tâche

Les tâches prennent en charge l’exécution manuelle, appelée « exécution rapide », et l’exécution automatisée lors de la validation Git ou de la mise à jour de l’image de base.

Pour exécuter une tâche, vous devez d’abord définir ses étapes dans un fichier YAML, puis exécuter la commande Azure CLI [az acr run][az-acr-run].

Voici un exemple de commande Azure CLI qui exécute une tâche à l’aide d’un exemple de fichier YAML de tâches. Ses étapes compilent, puis envoient une image. Mettez à jour `\<acrName\>` avec le nom de votre propre registre de conteneurs Azure avant d’exécuter la commande.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Lorsque vous exécutez la tâche, la sortie doit montrer la progression de chaque étape définie dans le fichier YAML. Dans la sortie suivante, les étapes apparaissent comme `acb_step_0` et `acb_step_1`.

```console
$ az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Pour en savoir plus sur les compilations automatiques lors de la validation Git ou de la mise à jour de l’image de base, consultez les articles [Automatiser des générations d’images](container-registry-tutorial-build-task.md) et [Mettre à jour les générations d’images de base ](container-registry-tutorial-base-image-update.md) du didacticiel.

## <a name="preview-feedback"></a>Commentaires de la préversion

Bien que la fonction de tâches multiétapes d’ACR Tasks se trouve en préversion, nous vous invitons à nous faire part de vos commentaires. Plusieurs canaux de commentaires sont disponibles :

* [Problèmes](https://aka.ms/acr/issues) : afficher les bogues et les problèmes existants, et en enregistrer de nouveaux
* [VoixUtilisateur](https://aka.ms/acr/uservoice) : voter sur les demandes de fonctionnalités existantes ou créer de nouvelles requêtes
* [Discuter](https://aka.ms/acr/feedback) : participer à une discussion sur Azure Container Registry avec la communauté Stack Overflow

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez ici des références et des exemples de tâches multiétapes :

* [Tâches de référence](container-registry-tasks-reference-yaml.md) : types d’étapes de tâches, leurs propriétés et leur utilisation.
* [Exemples de tâches] [ task-examples] : exemple `task.yaml` pour plusieurs scénarios, simples et complexes.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr/run#az-acr-run
[az-acr-task]: /cli/azure/acr#az-acr-task