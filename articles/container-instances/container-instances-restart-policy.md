---
title: Utilisez des stratégies de redémarrage avec des tâches conteneurisées dans Azure Container Instances.
description: Découvrez comment utiliser Azure Container Instances pour exécuter des tâches jusqu’à complétion, telles que des tâches de génération, de test ou de rendu d’image.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/21/2019
ms.author: danlep
ms.openlocfilehash: ef34985e7897aa751275231a28c6031d6c9747b0
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369960"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Exécuter des tâches conteneurisées avec des stratégies de redémarrage

La facilité et la vitesse de déploiement des conteneurs d’Azure Container Instances en font une plateforme incontournable pour les tâches à exécution unique, comme les tâches de génération, de test et de rendu d’image, dans une instance de conteneur.

À l’aide d’une stratégie de redémarrage configurable, vous pouvez spécifier l’arrêt de vos conteneurs lorsque leurs processus sont terminés. Étant donné que les instances de conteneur sont facturées à la seconde, vous êtes facturé uniquement pour les ressources de calcul utilisées au moment où le conteneur qui exécute votre tâche est exécuté.

Les exemples présentés dans cet article utilisent l’interface de ligne de commande Azure. Vous devez disposer d’Azure CLI version 2.0.21 ou ultérieure [en local][azure-cli-install], ou utiliser l’interface de ligne de commande dans [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Stratégie de redémarrage des conteneurs

Lorsque vous créez un [groupe de conteneurs](container-instances-container-groups.md) dans Azure Container Instances, vous pouvez spécifier l’un des trois paramètres de stratégie de redémarrage disponibles.

| Stratégie de redémarrage   | Description |
| ---------------- | :---------- |
| `Always` | Les conteneurs du groupe de conteneurs sont toujours redémarrés. Il s’agit du paramètre appliqué **par défaut** lorsqu’aucune stratégie de redémarrage n’est spécifiée au moment de la création du conteneur. |
| `Never` | Les conteneurs du groupe de conteneurs ne sont jamais redémarrés. Les conteneurs sont exécutés au maximum une fois. |
| `OnFailure` | Les conteneurs du groupe de conteneurs sont redémarrés uniquement en cas d’échec des processus qui y sont exécutés (lorsque ceux-ci se terminent par un code de sortie différent de zéro). Les conteneurs sont exécutés au moins une fois. |

## <a name="specify-a-restart-policy"></a>Spécifier une stratégie de redémarrage

La façon dont vous spécifiez une stratégie de redémarrage dépend de la manière dont vous créez vos instances de conteneurs (par exemple, avec l’interface de ligne de commande Azure, avec les applets de commande Azure PowerShell ou dans le portail Azure). Dans l’interface de ligne de commande Azure, spécifiez le paramètre `--restart-policy` lorsque vous appelez [az container create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Exemple d’exécution jusqu’à achèvement

Pour afficher la stratégie de redémarrage en action, créez une instance de conteneur à partir de Microsoft [aci-wordcount] [ aci-wordcount-image] de l’image, puis spécifiez le `OnFailure` stratégie de redémarrage. Cet exemple de conteneur exécute un script Python qui, par défaut, analyse le texte de [Hamlet](http://shakespeare.mit.edu/hamlet/full.html) de Shakespeare, écrit les 10 mots les plus fréquents dans STDOUT, puis se termine.

Exécutez l’exemple de conteneur avec la commande [az container create][az-container-create] suivante :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances démarre le conteneur, puis l’arrête lorsque l’exécution de son application (ou de son script, dans ce cas) est terminée. Quand Azure Container Instances arrête un conteneur dont la stratégie de redémarrage est `Never` ou `OnFailure`, l’état du conteneur est défini sur **Terminé**. Vous pouvez vérifier l’état du conteneur à l’aide de la commande [az container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Exemple de sortie :

```bash
"Terminated"
```

Lorsque l’état de l’exemple de conteneur est *Terminé*, vous pouvez voir la sortie de sa tâche en consultant les journaux d’activité du conteneur. Exécutez la commande [az container logs][az-container-logs] pour afficher la sortie du script :

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Sortie :

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

Cet exemple montre la sortie du script envoyé à STDOUT. Toutefois, il est possible que vos tâches en conteneur écrivent leur sortie dans un stockage persistant, en vue d’une récupération ultérieure. Par exemple, vers un [partage de fichiers Azure](container-instances-mounting-azure-files-volume.md).

## <a name="manually-stop-and-start-a-container-group"></a>Arrêter et démarrer un groupe de conteneurs manuellement

Quelle que soit la stratégie de redémarrage configurée pour un [groupe de conteneurs](container-instances-container-groups.md), vous souhaiterez peut-être arrêter ou démarrer manuellement un groupe de conteneurs.

* **Arrêter** - Vous pouvez arrêter manuellement un groupe de conteneurs en cours d’exécution à tout moment - par exemple, en utilisant la commande [az container stop][az-container-stop]. Pour certaines charges de travail de conteneur, vous pouvez vouloir arrêter un groupe de conteneurs après une période définie afin de réduire les coûts. 

  L’arrêt d’un groupe de conteneurs se termine et recycle les conteneurs du groupe, mais ne préserve pas l’état du conteneur. 

* **Démarrer** - Quand un groupe de conteneurs est arrêté - soit parce que les conteneurs s’arrêtent d’eux-mêmes, soit parce que vous avez arrêté manuellement le groupe - vous pouvez utiliser l’[API de démarrage de conteneur](/rest/api/container-instances/containergroups/start) ou le portail Azure pour démarrer manuellement les conteneurs dans le groupe. Si l’image d’un conteneur est mise à jour, une nouvelle image est extraite. 

  Le démarrage d’un groupe de conteneurs commence un nouveau déploiement avec la même configuration de conteneur. Cette action peut vous aider à réutiliser rapidement une configuration de groupe de conteneurs connue qui fonctionne comme prévu. Vous n’avez pas besoin de créer un nouveau groupe de conteneurs pour exécuter la même charge de travail.

* **Redémarrer** - Vous pouvez redémarrer un groupe de conteneurs en cours d’exécution - par exemple, en utilisant la commande [az container restart][az-container-restart]. Cette action redémarre tous les conteneurs du groupe de conteneurs. Si l’image d’un conteneur est mise à jour, une nouvelle image est extraite. 

  Le redémarrage d’un groupe de conteneurs est utile lorsque vous souhaitez résoudre un problème de déploiement. Par exemple, si une limitation temporaire des ressources empêche le bon fonctionnement de vos conteneurs, le redémarrage du groupe peut résoudre le problème.

Après que vous avez démarré ou redémarré manuellement un groupe de conteneurs, le groupe de conteneurs fonctionne conformément à la stratégie de redémarrage configurée.

## <a name="configure-containers-at-runtime"></a>Configurer des conteneurs au moment de l’exécution

Lorsque vous créez une instance de conteneur, vous pouvez définir ses **variables d’environnement**, et spécifier la **ligne de commande** personnalisée à exécuter une fois le conteneur démarré. Vous pouvez utiliser ces paramètres dans vos programmes de traitement par lots en vue d’appliquer à chacun des conteneurs une configuration spécifique aux tâches.

## <a name="environment-variables"></a>Variables d’environnement

Définissez des variables d’environnement dans votre conteneur pour permettre la configuration dynamique de l’application ou du script exécutés par le conteneur. Cela revient à définir l’argument de ligne de commande `--env` sur `docker run`.

Par exemple, vous pouvez modifier le comportement du script dans l’exemple de conteneur en spécifiant les variables d’environnement suivantes lorsque vous créez l’instance de conteneur :

*NumWords* : Nombre de mots envoyés à STDOUT.

*MinLength* : Nombre minimal de caractères dans un mot pour que celui-ci soit comptabilisé. Cela vous permet d’ignorer les mots communs tels que « de » et « le ».

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Si vous spécifiez `NumWords=5` et `MinLength=8` pour les variables d’environnement du conteneur, les journaux d’activité du conteneur doivent afficher une sortie différente. Une fois que l’état du conteneur est *Terminé* (utilisez `az container show` pour vérifier son état), affichez ses journaux d’activité pour voir la nouvelle sortie :

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Sortie :

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```



## <a name="command-line-override"></a>Remplacement de la ligne de commande

Spécifiez une ligne de commande lorsque vous créez une instance de conteneur pour remplacer la ligne de commande intégrée à l’image de conteneur. Cela revient à définir l’argument de ligne de commande `--entrypoint` sur `docker run`.

Vous pouvez demander à l’exemple de conteneur d’analyser du texte autre que celui *d’Hamlet* en spécifiant une autre ligne de commande. Le script Python exécuté par le conteneur (*wordcount.py*) accepte une URL comme argument, et traite le contenu de cette page au lieu de celui défini par défaut.

Par exemple, pour déterminer les 3 mots à cinq lettres les plus utilisés dans *Roméo et Juliette* :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Là encore, lorsque l’état du conteneur est *Terminé*, affichez les journaux d’activité du conteneur pour consulter la sortie :

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Sortie :

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Étapes suivantes

### <a name="persist-task-output"></a>Conserver les sorties des tâches

Pour plus d’informations sur la conservation de la sortie de vos conteneurs qui s’exécutent jusqu’à achèvement, consultez [Montage d’un partage de fichiers Azure avec Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
[azure-cli-install]: /cli/azure/install-azure-cli
