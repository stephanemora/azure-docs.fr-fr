---
title: Utiliser une ligne de commande de départ dans Azure Container Instances
description: Remplacer le point d’entrée configuré dans une image de conteneur lorsque vous déployez une instance de conteneur Azure
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: da94a4c79694f511d41e5c8dda8c786fc7049726
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64569639"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Définir la ligne de commande dans une instance de conteneur pour remplacer l’opération de ligne de commande par défaut

Lorsque vous créez une instance de conteneur, si vous le souhaitez spécifier une commande pour remplacer l’instruction de ligne de commande par défaut intégrée à l’image de conteneur. Ce comportement est similaire à la `--entrypoint` argument de ligne de commande pour `docker run`.

Telles que la définition [variables d’environnement](container-instances-environment-variables.md) pour les instances de conteneur, en spécifiant une ligne de commande de départ est utile pour les traitements par lots où vous avez besoin préparer chaque conteneur dynamiquement avec une configuration spécifique à une tâche.

## <a name="command-line-guidelines"></a>Instructions de ligne de commande

* Par défaut, la ligne de commande spécifie un *unique processus qui démarre sans un interpréteur de commandes* dans le conteneur. Par exemple, la ligne de commande peut exécuter un script Python ou un fichier exécutable. 

* Pour exécuter plusieurs commandes, commencer votre ligne de commande en définissant un environnement d’interface qui est pris en charge dans le système d’exploitation du conteneur. Exemples :

  |Système d’exploitation  |Shell par défaut  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Suivez les conventions de l’interpréteur de commandes pour combiner plusieurs commandes à exécuter dans la séquence.

* Selon la configuration du conteneur, vous devrez peut-être définir un chemin d’accès complet à l’exécutable de ligne de commande ou des arguments.

* Définir un appropriées [stratégie de redémarrage](container-instances-restart-policy.md) pour l’instance de conteneur, selon que la ligne de commande spécifie une tâche longue ou une tâche d’exécution unique. Par exemple, une stratégie de redémarrage de `Never` ou `OnFailure` est recommandée pour une tâche d’exécution unique. 

* Si vous avez besoin d’informations sur le point d’entrée par défaut définie dans une image de conteneur, utilisez la [image docker inspecter](https://docs.docker.com/engine/reference/commandline/image_inspect/) commande.

## <a name="command-line-syntax"></a>Syntaxe de la ligne de commande

La syntaxe de ligne de commande varie en fonction de l’API Azure ou l’outil utilisé pour créer les instances. Si vous spécifiez un environnement d’interpréteur de commandes, également observer les conventions de syntaxe de commande de l’interpréteur de commandes.

* [création du conteneur AZ] [ az-container-create] commande : Passer une chaîne avec le `--command-line` paramètre. Exemple : `--command-line "python myscript.py arg1 arg2"`).

* [Nouveau-AzureRmContainerGroup] [ new-azurermcontainergroup] applet de commande Azure PowerShell : Passer une chaîne avec le `-Command` paramètre. Exemple : `-Command "echo hello"`.

* Portail Azure : Dans le **remplacement de la commande** une propriété de la configuration du conteneur, fournir une liste séparée par des virgules de chaînes, sans guillemets. Exemple : `python, myscript.py, arg1, arg2`). 

* Modèle Resource Manager ou le fichier YAML ou un des kits de développement logiciel Azure : Spécifiez la propriété de ligne de commande en tant que tableau de chaînes. Exemple : tableau JSON `["python", "myscript.py", "arg1", "arg2"]` dans un modèle Resource Manager. 

  Si vous êtes familiarisé avec [Dockerfile](https://docs.docker.com/engine/reference/builder/) syntaxe, ce format est semblable à la *exec* formulaire de l’instruction de CMD.

### <a name="examples"></a>Exemples

|    |  Azure CLI   | Portail | Modèle | 
| ---- | ---- | --- | --- |
| Commande unique | `--command-line "python myscript.py arg1 arg2"` | **Remplacement de la commande**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Plusieurs commandes | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Remplacement de la commande**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Exemple Azure CLI

Par exemple, modifier le comportement de la [microsoft/aci-wordcount] [ aci-wordcount] image de conteneur, qui analyse le texte de Shakespeare *Hamlet* pour rechercher les plus fréquemment mots. Au lieu d’analyser *Hamlet*, vous pouvez définir une ligne de commande qui pointe vers une source de texte différente.

Pour afficher la sortie de la [microsoft/aci-wordcount] [ aci-wordcount] conteneur lorsqu’il analyse le texte par défaut, l’exécuter par le code suivant [créer de conteneur az] [ az-container-create] commande. Aucune ligne de commande de démarrage n’est spécifié, la commande de conteneur par défaut s’exécute. À titre d’illustration, cet exemple définit [variables d’environnement](container-instances-environment-variables.md) pour trouver les 3 mots qui sont au moins cinq lettres :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Une fois que l’état du conteneur affiche en tant que *Terminated* (utilisez [show de conteneur az] [ az-container-show] pour vérifier l’état), afficher le journal avec [journaux du conteneur az] [ az-container-logs] pour afficher la sortie.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Sortie :

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Configurez maintenant un deuxième exemple de conteneur pour analyser le texte différent en spécifiant une autre ligne de commande. Le script Python exécuté par le conteneur, *wordcount.py*, accepte une URL en tant qu’argument et traite le contenu de cette page au lieu de la valeur par défaut.

Par exemple, pour déterminer la partie supérieure 3 mots qui sont au moins cinq lettres long dans *Roméo et Juliette*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Là encore, lorsque l’état du conteneur est *Terminé*, affichez les journaux d’activité du conteneur pour consulter la sortie :

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Sortie :

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Étapes suivantes

Les scénarios basés sur des tâches, comme un jeu de données volumineux avec plusieurs conteneurs, de traitement par lots peuvent tirer parti des lignes de commande personnalisées lors de l’exécution. Pour plus d’informations sur l’exécution des conteneurs basés sur des tâches, consultez [exécuter des tâches en conteneur avec les stratégies de redémarrage](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
