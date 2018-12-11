---
title: 'Didacticiel : surveiller et mettre à jour des machines virtuelles Linux dans Azure | Microsoft Docs'
description: Avec ce didacticiel, vous allez apprendre à surveiller les diagnostics de démarrage et les métriques de performances, et à gérer les mises à jour de package sur une machine virtuelle Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c271efceacab7f310b8e08a28d101f653c73a186
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868546"
---
# <a name="tutorial-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Didacticiel : surveiller et mettre à jour une machine virtuelle Linux dans Azure

Pour vérifier que vos machines virtuelles dans Azure fonctionnent correctement, vous pouvez consulter les diagnostics de démarrage et les métriques de performances, et gérer les mises à jour de package. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer les diagnostics de démarrage sur la machine virtuelle
> * Afficher les diagnostics de démarrage
> * Afficher les métriques de l’hôte
> * Activer l’extension Diagnostics sur la machine virtuelle
> * Afficher les métriques de la machine virtuelle
> * Créer des alertes en fonction des métriques des diagnostics
> * Gérer les mises à jour de package
> * Surveiller les modifications et l’inventaire
> * Configurer la surveillance avancée

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.30 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Créer une machine virtuelle

Pour voir les diagnostics et les métriques en action, vous avez besoin d’une machine virtuelle. Tout d’abord, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroupMonitor* à l’emplacement *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Créez maintenant une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az-vm-create). L’exemple suivant crée une machine virtuelle nommée *myVM* et génère des clés SSH si elles n’existent pas déjà dans *~/.ssh/* :

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Activer les diagnostics de démarrage

Au démarrage des machines virtuelles Linux, l’extension Diagnostics de démarrage capture la sortie du démarrage et la stocke dans le stockage Azure. Ces données peuvent être utilisées pour résoudre les problèmes de démarrage des machines virtuelles. Les diagnostics de démarrage ne sont pas activés automatiquement quand vous créez une machine virtuelle Linux à l’aide d’Azure CLI.

Avant d’activer les diagnostics de démarrage, vous devez créer un compte de stockage pour stocker les journaux de démarrage. Les comptes de stockage doivent avoir un nom global unique, comprenant entre 3 et 24 caractères, et contenant seulement des chiffres et des lettres minuscules. Créez un compte de stockage avec la commande [az storage account create](/cli/azure/storage/account#az-storage-account-create). Dans cet exemple, une chaîne aléatoire est utilisée pour créer un nom de compte de stockage unique.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Lors de l’activation des diagnostics de démarrage, l’URI vers le conteneur de stockage d’objets blob est nécessaire. La commande suivante interroge le compte de stockage et retourne cet URI. La valeur de l’URI est stockée dans une variable nommée *bloburi*, qui est utilisé à l’étape suivante.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Activez maintenant les diagnostics de démarrage avec la commande [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). La valeur de `--storage` est l’URI de l’objet blob collecté à l’étape précédente.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Afficher les diagnostics de démarrage

Quand les diagnostics de démarrage sont activés, chaque fois que vous arrêtez et que vous démarrez la machine virtuelle, des informations sur le processus de démarrage sont écrites dans un fichier journal. Pour cet exemple, désallouez d’abord la machine virtuelle avec la commande [az vm deallocate](/cli/azure/vm#az-vm-deallocate) comme suit :

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Démarrez maintenant la machine virtuelle avec la commande [az vm start]( /cli/azure/vm#az-vm-stop) comme suit :

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Vous pouvez obtenir les données des diagnostics de démarrage pour *myVM* avec la commande [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) comme suit :

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Afficher les métriques de l’hôte

Une machine virtuelle Linux a un hôte dédié dans Azure qui interagit avec elle. Les métriques sont automatiquement collectées pour l’hôte et peuvent être visualisées dans le portail Azure comme suit :

1. Dans le portail Azure, sélectionnez **Groupes de ressources**, choisissez **myResourceGroupMonitor** puis sélectionnez **myVM** dans la liste des ressources.
1. Pour voir comment la machine virtuelle hôte fonctionne, sélectionnez **Métriques** dans la fenêtre de la machine virtuelle, puis sélectionnez une des métriques de l’*[hôte]* sous **Métriques disponibles**.

    ![Afficher les métriques de l’hôte](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Installer l’extension Diagnostics

Les métriques de base de l’hôte sont disponibles, mais pour voir des métriques plus précises et spécifiques à la machine virtuelle, vous devez installer l’extension Diagnostics Azure sur la machine virtuelle. L’extension Diagnostics Azure permet de récupérer des données supplémentaires de surveillance et de diagnostic auprès de la machine virtuelle. Vous pouvez voir ces métriques de performances et créer des alertes basées sur le fonctionnement de la machine virtuelle. L’extension Diagnostics est installée via le portail Azure comme suit :

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroupMonitor** puis sélectionnez **myVM** dans la liste des ressources.
1. Sélectionnez **Paramètres de diagnostic**. Dans le menu déroulant *Sélectionner un compte de stockage*, si ce n’est pas déjà fait, choisissez le compte *mydiagdata [1234]* créé dans la section précédente.
1. Cliquez sur le bouton **Activer la surveillance d’invités**.

    ![Afficher les métriques de diagnostic](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Afficher les métriques de la machine virtuelle

Vous pouvez afficher les métriques de la machine virtuelle de la même façon que vous avez affiché le mesures de la machine virtuelle hôte :

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroupMonitor** puis sélectionnez **myVM** dans la liste des ressources.
1. Pour voir comment la machine virtuelle fonctionne, cliquez sur **Métriques** dans la fenêtre de la machine virtuelle puis sélectionnez une des métriques de diagnostic *[Guest]* sous **Métriques disponibles**.

    ![Afficher les métriques de la machine virtuelle](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Créez des alertes

Vous pouvez créer des alertes en fonction de métriques de performances spécifiques. Les alertes peuvent être utilisées par exemple pour notifier que l’utilisation moyenne de l’UC dépasse un certain seuil ou que l’espace disque disponible est inférieur à une certaine quantité. Les alertes sont affichées dans le portail Azure ou peuvent être envoyées par e-mail. Vous pouvez également déclencher des runbooks Azure Automation ou Azure Logic Apps en réponse aux alertes générées.

L’exemple suivant crée une alerte pour l’utilisation moyenne de l’UC.

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroupMonitor** puis sélectionnez **myVM** dans la liste des ressources.
2. Sélectionnez **Alertes (classique)**, puis choisissez **Ajouter une alerte métrique (classique)** en haut de la fenêtre d’alerte.
3. Spécifiez un **Nom** pour votre alerte, comme *myAlertRule*
4. Pour déclencher une alerte quand le pourcentage d’UC dépasse 1,0 pendant cinq minutes, laissez toutes les autres valeurs par défaut sélectionnées.
5. Si vous le souhaitez, cochez la case *Envoyer un e-mail aux propriétaires, aux contributeurs et aux lecteurs* pour envoyer la notification par e-mail. L’action par défaut est de présenter une notification dans le portail.
6. Cliquez sur le bouton **OK**.

## <a name="manage-package-updates"></a>Gérer les mises à jour de package

La gestion des mises à jour vous permet de gérer les mises à jour et les correctifs pour vos machines virtuelles Linux Azure.
Directement à partir de votre machine virtuelle, vous pouvez rapidement évaluer l’état des mises à jour disponibles, planifier l’installation des mises à jour nécessaires et passer en revue les résultats des déploiements pour vérifier que les mises à jour ont été appliquées correctement à la machine virtuelle.

Pour plus d’informations sur les prix, consultez [Tarification Automation pour la gestion des mises à jour](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Activer la gestion des mises à jour

Activer la gestion des mises à jour pour votre machine virtuelle :

1. Sur le côté gauche de l’écran, sélectionnez **Machines virtuelles**.
2. Sélectionnez une machine virtuelle dans la liste.
3. Dans l’écran de la machine virtuelle, dans la section **Opérations**, sélectionnez **Gestion des mises à jour**. L’écran **Activer la gestion des mises à jour** s’ouvre.

Une validation est effectuée pour déterminer si la gestion des mises à jour est activée pour cette machine virtuelle.
La validation inclut la vérification de l’existence d’un espace de travail Log Analytics et d’un compte Automation lié, et si la solution est dans l’espace de travail.

Un espace de travail [Log Analytics](../../log-analytics/log-analytics-overview.md) est utilisé pour collecter les données générées par les fonctionnalités et les services, comme la gestion des mises à jour.
L’espace de travail fournit un emplacement unique permettant de consulter et d’analyser les données provenant de plusieurs sources.
Pour effectuer une action supplémentaire sur les machines virtuelles qui nécessitent des mises à jour, Azure Automation vous permet d’exécuter des runbooks sur les machines virtuelles, par exemple télécharger et appliquer des mises à jour.

Le processus de validation vérifie également que la machine virtuelle est configurée avec le Microsoft Monitoring Agent (MMA) et un runbook Worker hybride Automation.
Cet agent est utilisé pour communiquer avec la machine virtuelle et pour obtenir des informations sur l’état des mises à jour.

Choisissez l’espace de travail Log Analytics et le compte Automation, puis cliquez sur **Activer** pour activer la solution. L’activation de la solution prend jusqu’à 15 minutes.

Si l’intégration n’identifie pas l’un des prérequis suivants, il est automatiquement ajouté :

* Espace de travail [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automation](../../automation/automation-offering-get-started.md)
* Un [worker runbook hybride](../../automation/automation-hybrid-runbook-worker.md) est activé sur la machine virtuelle

L’écran **Gestion des mises à jour** s’ouvre. Configurez l’emplacement, l’espace de travail Log Analytics et le compte Automation à utiliser, puis cliquez sur **Activer**. Si les champs sont grisés, cela signifie qu’une autre solution d’automatisation est activée pour la machine virtuelle, et les mêmes espace de travail et compte Automation doivent être utilisés.

![Activer la solution de gestion des mises à jour](./media/tutorial-monitoring/manage-updates-update-enable.png)

L’activation de la solution peut prendre jusqu’à 15 minutes. Pendant ce temps, vous ne devez pas fermer la fenêtre du navigateur. Une fois la solution activée, des informations sur les mises à jour manquantes sur la machine virtuelle sont envoyées à Log Analytics. Entre 30 minutes et 6 heures peuvent être nécessaires pour que les données soient disponibles pour l’analyse.

### <a name="view-update-assessment"></a>Afficher l’évaluation des mises à jour

Une fois la **gestion des mises à jour** activée, l’écran **Gestion des mises à jour** apparaît. Une fois l’évaluation des mises à jour terminée, vous pouvez afficher une liste des mises à jour manquantes sous l’onglet **Mises à jour manquantes**.

 ![Afficher l’état des mises à jour](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Planifier un déploiement de mises à jour

Pour installer les mises à jour, planifiez un déploiement qui suit votre fenêtre de planification et de maintenance des versions. Vous pouvez choisir les types de mises à jour à inclure dans le déploiement. Par exemple, vous pouvez inclure des mises à jour critiques ou de sécurité et exclure des correctifs cumulatifs.

Pour planifier un nouveau déploiement de mises à jour pour la machine virtuelle, sélectionnez **Planifier le déploiement de la mise à jour** en haut de l’écran **Gestion des mises à jour**. Dans l’écran **Nouveau déploiement de mises à jour**, spécifiez les informations suivantes :

* **Nom** : spécifiez un nom unique pour identifier le déploiement de mises à jour.
* **Classification de mise à jour** : sélectionnez les types de logiciels que le déploiement de mises à jour incluait dans le déploiement. Les types de classification sont les suivants :
  * Mises à jour critiques et de sécurité
  * Autres mises à jour
* **Updates to Exclude** (Mises à jour à exclure) : vous pouvez fournir une liste de noms de package qui doivent être ignorés pendant le déploiement de la mise à jour. Les noms de package acceptent les caractères génériques (par exemple, \*noyau\*).

  ![Écran Paramètres de planification des mises à jour](./media/tutorial-monitoring/manage-updates-exclude-linux.png)

* **Paramètres de planification** : vous pouvez accepter la date et l’heure par défaut, qui est de 30 minutes après l’heure actuelle, ou spécifier un moment différent.
  Vous pouvez également spécifier si le déploiement se produit une seule fois ou configurer une planification périodique. Pour définir une planification périodique, sélectionnez l’option Périodique sous Périodicité.

  ![Écran Paramètres de planification des mises à jour](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Fenêtre de maintenance (en minutes)**  : spécifiez la période de temps pendant laquelle le déploiement des mises à jour doit se produire. Cela permet de garantir que les modifications sont effectuées pendant les fenêtres de maintenance que vous avez définies.

Une fois que vous avez terminé la configuration de la planification, cliquez sur le bouton **Créer**. Vous revenez ensuite au tableau de bord des états.
Notez que le tableau **Planifié** montre la planification de déploiement que vous avez créée.

> [!WARNING]
> Pour les mises à jour nécessitant un redémarrage, la machine virtuelle est automatiquement redémarrée.

### <a name="view-results-of-an-update-deployment"></a>Afficher les résultats d’un déploiement de mises à jour

Une fois que le déploiement planifié démarre, vous pouvez voir l’état de ce déploiement sous l’onglet **Déploiements des mises à jour** dans l’écran **Gestion des mises à jour**.
S’il est en cours d’exécution, son état est **En cours d’exécution**. Une fois le déploiement terminé, s’il est réussi, l’état passe à **Réussi**.
S’il existe un échec avec une ou plusieurs mises à jour dans le déploiement, l’état est **Échec partiel**.
Sélectionnez le déploiement des mises à jour terminé pour voir le tableau de bord dédié à ce déploiement de mises à jour.

![Tableau de bord des états de déploiement des mises à jour pour un déploiement spécifique](./media/tutorial-monitoring/manage-updates-view-results.png)

Dans la vignette **Résultats des mises à jour**, vous pouvez voir un récapitulatif du nombre total de mises à jour et les résultats du déploiement sur la machine virtuelle.
Dans le tableau de droite se trouve une répartition détaillée de chaque mise à jour et les résultats de l’installation, qui peut être une des valeurs suivantes :

* **Aucune tentative effectuée** : la mise à jour n’a pas été installée, car le temps disponible était insuffisant d’après la durée définie pour la fenêtre de maintenance.
* **Réussi** : la mise à jour a réussi
* **Échec** : la mise à jour a échoué

Pour afficher toutes les entrées de journal créées par le déploiement, sélectionnez **Tous les journaux**.

Cliquez sur la vignette **Sortie** pour voir le flux des tâches du runbook chargé de gérer le déploiement des mises à jour sur la machine virtuelle cible.

Pour afficher les informations détaillées sur les erreurs du déploiement, sélectionnez **Erreurs**.

## <a name="monitor-changes-and-inventory"></a>Surveiller les modifications et l’inventaire

Vous pouvez collecter et afficher l’inventaire des logiciels, fichiers, démons Linux, services Windows et des clés de registre Windows présents sur vos ordinateurs. Le suivi des configurations de vos machines peut vous aider à identifier les problèmes opérationnels au sein de votre environnement et à mieux comprendre l’état de vos machines.

### <a name="enable-change-and-inventory-management"></a>Activer la gestion des modifications et de l’inventaire

Activez la gestion des modifications et de l’inventaire pour votre machine virtuelle :

1. Sur le côté gauche de l’écran, sélectionnez **Machines virtuelles**.
2. Sélectionnez une machine virtuelle dans la liste.
3. Sur l’écran de la machine virtuelle, dans la section **Opérations**, sélectionnez **Inventaire** ou **Suivi des modifications**. L’écran **Enable Change Tracking and Inventory** (Activer le suivi des modifications et inventaire) s’ouvre.

Configurez l’emplacement, l’espace de travail Log Analytics et le compte Automation à utiliser, puis cliquez sur **Activer**. Si les champs sont grisés, cela signifie qu’une autre solution d’automatisation est activée pour la machine virtuelle, et les mêmes espace de travail et compte Automation doivent être utilisés. Même si les solutions sont distinctes dans le menu, elles forment une même solution. Le fait d’en activer une active les deux pour votre machine virtuelle.

![Activer le suivi des modifications et de l’inventaire](./media/tutorial-monitoring/manage-inventory-enable.png)

Une fois la solution activée, un délai peut être nécessaire pour la collecte de l’inventaire sur la machine virtuelle avant l’affichage de données.

### <a name="track-changes"></a>Suivi des modifications

Sur votre machine virtuelle, sélectionnez **Suivi des modifications** sous **OPÉRATIONS**. Cliquez sur **Modifier les paramètres**, la page **Suivi des modifications** s’affiche. Sélectionnez le type de paramètre que vous souhaitez suivre, puis cliquez sur **+ Ajouter** pour configurer les paramètres. L’option disponible pour Linux est **Fichiers Linux**

Pour plus d’informations sur Change Tracking, consultez [Dépanner les modifications apportées à votre environnement](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Afficher l’inventaire

Sur votre machine virtuelle, sélectionnez **Inventaire** sous **OPÉRATIONS**. Dans l’onglet **Logiciels**, un tableau dresse la liste des logiciels détectés. Les informations détaillées sur chaque enregistrement de logiciel sont visibles dans le tableau. Ces détails incluent le nom du logiciel, la version, l’éditeur, l’heure de la dernière actualisation.

![Afficher l’inventaire](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Surveiller les journaux d’activité et les modifications

À partir de la page **Suivi des modifications** sur votre machine virtuelle, sélectionnez **Manage Activity Log Connection** (Gérer la connexion du journal d’activité). Cette tâche ouvre la page **Journal des activités Azure**. Sélectionnez **Connexion** pour connecter le suivi des modifications au journal des activités Azure pour votre machine virtuelle.

Une fois ce paramètre activé, accédez à la page **Vue d’ensemble** de votre machine virtuelle, puis sélectionnez **Arrêter** pour l’arrêter. Lorsque vous y êtes invité, sélectionnez **Oui** pour arrêter la machine virtuelle. Celle-ci étant libérée, sélectionnez **Démarrer** pour redémarrer votre machine virtuelle.

Le fait d’arrêter et de démarrer une machine virtuelle enregistre un événement dans son journal d’activité. Revenez à la page **Suivi des modifications**. Sélectionnez l’onglet **Événements** au bas de la page. Après un certain temps, les événements apparaissent dans le graphique et le tableau. Chaque événement peut être sélectionné pour en afficher le détail.

![Afficher les modifications dans le journal d’activité](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Le graphique affiche les modifications qui se sont produites au fil du temps. Après avoir ajouté une connexion au journal d’activité, le graphique linéaire dans la partie supérieure affiche les événements du journal des activités Azure. Chacune des lignes des graphiques à barres représente un autre type de modification dont il est possible d’effectuer le suivi. Ces types sont les démons Linux, les fichiers et les logiciels. L’onglet Modification présente le détail des modifications indiquées dans la visualisation, par ordre décroissant, c’est-à-dire le plus récent en premier.

## <a name="advanced-monitoring"></a>Surveillance avancée

Vous pouvez effectuer un suivi plus avancé de votre machine virtuelle à l’aide de solutions telles que Update Management et Change and Inventory fournies par [Azure Automation](../../automation/automation-intro.md).

Lorsque vous avez accès à l’espace de travail Log Analytics, vous pouvez trouver la clé de l’espace de travail et l’identificateur de l’espace de travail en sélectionnant **Paramètres avancés** sous **PARAMÈTRES**. Remplacez \<workspace-key\> et \<workspace-id\> par les valeurs correspondant à votre espace de travail Log Analytics et utilisez ensuite **az vm extension set** pour ajouter l’extension à la machine virtuelle :

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

Après quelques minutes, la nouvelle machine virtuelle s’affiche dans l’espace de travail Log Analytics.

![Log Analytics](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré, examiné et géré les mises à jour pour une machine virtuelle. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Activer les diagnostics de démarrage sur la machine virtuelle
> * Afficher les diagnostics de démarrage
> * Afficher les métriques de l’hôte
> * Activer l’extension Diagnostics sur la machine virtuelle
> * Afficher les métriques de la machine virtuelle
> * Créer des alertes en fonction des métriques des diagnostics
> * Gérer les mises à jour de package
> * Surveiller les modifications et l’inventaire
> * Configurer la surveillance avancée

Passez au didacticiel suivant pour découvrir plus d’informations sur Azure Security Center.

> [!div class="nextstepaction"]
> [Gérer la sécurité des machines virtuelles](./tutorial-azure-security.md)
