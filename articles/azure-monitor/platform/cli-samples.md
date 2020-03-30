---
title: Exemples pour le démarrage rapide de l’interface CLI Azure Monitor
description: Exemples de commandes de l’interface de ligne de commande pour les fonctionnalités d’Azure Monitor. Azure Monitor est un service Microsoft Azure qui vous permet d’envoyer des notifications d’alerte, ou d’appeler des URL web en fonction des valeurs des données de télémétrie configurées ainsi que de mettre à l’échelle automatiquement des services cloud, des machines virtuelles et des applications web.
ms.subservice: ''
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 69687cee8b8a907b82f2c848242ac64d54dedb87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79226377"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Exemples pour le démarrage rapide de l’interface CLI Azure Monitor
Cet article vous montre des exemples de commandes d’interface de ligne de commande (CLI) qui vous permettent d’accéder aux fonctionnalités de surveillance d’Azure Monitor. Azure Monitor permet une mise à l'échelle automatique des services cloud, des machines virtuelles et des applications web, et d’envoyer des notifications d'alerte ou d’appeler des URL web basées sur des valeurs de données de télémétrie configurées.

## <a name="prerequisites"></a>Conditions préalables requises

Si vous n’avez pas déjà installé Azure CLI, suivez les instructions d’[installation d’Azure CLI](/cli/azure/install-azure-cli). Vous pouvez également utiliser [Azure Cloud Shell](/azure/cloud-shell) pour exécuter l’interface CLI en tant qu’expérience interactive dans votre navigateur. Pour obtenir la documentation de référence complète sur l’ensemble des commandes disponibles, consultez la [documentation de référence sur l’interface de ligne de commande Azure Monitor](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Connexion à Azure
La première étape consiste à vous connecter à votre compte Azure.

```azurecli
az login
```

Après avoir exécuté cette commande, vous devez vous connecter via les instructions à l’écran. Toutes les commandes fonctionnent dans le cadre de votre abonnement par défaut.

Pour répertorier les détails de votre abonnement actuel, utilisez la commande suivante.

```azurecli
az account show
```

Pour remplacer le contexte de travail par un autre abonnement, utilisez la commande suivante.

```azurecli
az account set -s <Subscription ID or name>
```

Pour afficher une liste de toutes les commandes Azure Monitor prises en charge, effectuez les opérations suivantes.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Afficher le journal d’activité pour un abonnement

Pour afficher une liste d’événements du journal d’activité, effectuez les opérations suivantes.

```azurecli
az monitor activity-log list
```

Pour voir toutes les options disponibles, essayez les opérations suivantes.

```azurecli
az monitor activity-log list -h
```

Voici un exemple permettant de répertorier les journaux d’activité par groupe de ressources

```azurecli
az monitor activity-log list --resource-group <group name>
```

Exemple pour répertorier les journaux d’activité par appelant

```azurecli
az monitor activity-log list --caller myname@company.com
```

Exemple pour répertorier les journaux d’activité par appelant, sur un type de ressource, dans une plage de dates

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Utilisation des alertes 
> [!NOTE]
> Seules les alertes (classiques) sont prises en charge dans l’interface de ligne de commande pour le moment. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Obtenir des règles d’alerte (classique) dans un groupe de ressources

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Créer une règle d’alerte de métrique (classique)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Supprimer une règle d’alerte (classique)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Profils de journal

Utilisez les informations figurant dans cette section pour utiliser les profils de journal.

### <a name="get-a-log-profile"></a>Obtenir un profil de journal

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Ajouter un profil de journal avec rétention

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Ajouter un profil de journal avec rétention et EventHub

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Supprimer un profil de journal

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostics

Utilisez les informations figurant dans cette section pour utiliser les paramètres de diagnostic.

### <a name="get-a-diagnostic-setting"></a>Obtenir un paramètre de diagnostic

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Créer un paramètre de diagnostic 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Supprimer un paramètre de diagnostic

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Mise à l’échelle automatique

Utilisez les informations figurant dans cette section pour utiliser les paramètres de mise à l’échelle automatique. Vous devez modifier ces exemples.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obtenir les paramètres de mise à l’échelle automatique pour un groupe de ressources

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obtenir les paramètres de mise à l’échelle automatique par nom dans un groupe de ressources

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Régler les paramètres de mise à l'échelle automatique

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

