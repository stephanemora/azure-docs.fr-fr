---
title: az spring cloud
description: Gérer Azure Spring Cloud à l’aide d’Azure CLI
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 10f3856a7c0c0a98ab8b23e20342d745b1966b9b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89263193"
---
# <a name="az-spring-cloud"></a>az spring-cloud

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Gérer Azure Spring Cloud à l’aide d’Azure CLI

| az spring-cloud | Commandes |
|------|------:|
| [az spring-cloud create](#az-spring-cloud-create) | Créer une instance Azure Spring Cloud. |
| [az spring-cloud delete](#az-spring-cloud-delete) | Supprimer une instance Azure Spring Cloud. |
| [az spring-cloud list](#az-spring-cloud-list) | Lister toutes les instances Azure Spring Cloud dans le groupe de ressources donné ; sinon, lister les ID d’abonnements. |
| [az spring-cloud show](#az-spring-cloud-show) | Afficher les détails d’un Azure Spring Cloud. |

| az spring-cloud app | Commandes pour gérer les applications dans Azure Spring Cloud.  |
| ---- | ----: |
| [az spring-cloud app create](#az-spring-cloud-app-create) | Créer une application avec un déploiement par défaut dans Azure Spring Cloud. |
| [az spring-cloud app delete](#az-spring-cloud-app-delete) | Supprimer une application dans Azure Spring Cloud. |
| [az spring-cloud app deploy](#az-spring-cloud-app-deploy) | Déployer à partir du code source ou d’un binaire prédéfini vers une application, et mettre à jour les configurations associées. |
| [az spring-cloud app list](#az-spring-cloud-app-list) | Lister toutes les applications dans Azure Spring Cloud. |
| [az spring-cloud app restart](#az-spring-cloud-app-restart) | Redémarrer les instances de l’application à l’aide des paramètres par défaut de déploiement de production. |
| [az spring-cloud app scale](#az-spring-cloud-app-scale) | Mettre à l’échelle manuellement une application ou ses déploiements. |
| [az spring-cloud app set-deployment](#az-spring-cloud-app-set-deployment) | Définir le déploiement de production d’une application. |
| [az spring-cloud app show](#az-spring-cloud-app-show) | Afficher les détails d’une application dans Azure Spring Cloud. |
| [az spring-cloud app show-deploy-log](#az-spring-cloud-app-show-deploy-log) | Afficher les journaux de génération pour le déploiement le plus récent à partir de la source. Par défaut, il s’agit du déploiement de production. |
| [az spring-cloud app start](#az-spring-cloud-app-start) | Démarrer les instances de l’application à l’aide des paramètres par défaut de déploiement de production. |
| [az spring-cloud app stop](#az-spring-cloud-app-stop) | Arrêter les instances de l’application à l’aide des paramètres par défaut de déploiement de production. |
| [az spring-cloud app update](#az-spring-cloud-app-update) | Mettre à jour la configuration de l’application spécifiée. |

| az spring-cloud app binding | Commandes pour gérer les liaisons avec Azure Data Services.  L’application doit être redémarrée pour que ces paramètres prennent effet. |
| --- | ---: |
| [az spring-cloud app binding list](#az-spring-cloud-app-binding-list) | Lister toutes les liaisons de service dans une application. |
| [az spring-cloud app binding remove](#az-spring-cloud-app-binding-remove) | Supprimer une liaison de service de l’application. |
| [az spring-cloud app binding show](#az-spring-cloud-app-binding-show) | Afficher les détails d’une liaison de service. |
| [az spring-cloud app binding cosmos add](#az-spring-cloud-app-binding-cosmos-add) | Lier une base de données Azure Cosmos DB à l’application. |
| [az spring-cloud app binding cosmos update](#az-spring-cloud-app-binding-cosmos-update) | Mettre à jour une liaison de service Azure Cosmos DB. |
| [az spring-cloud app binding mysql add](#az-spring-cloud-app-binding-mysql-add) | Lier une base de données Azure Database pour MySQL à l’application. |
| [az spring-cloud app binding mysql update](#az-spring-cloud-app-binding-mysql-update) | Mettre à jour une liaison de service Azure Database pour MySQL. |
| [az spring-cloud app binding redis add](#az-spring-cloud-app-binding-redis-add) | Lier un Cache Azure pour Redis avec l’application. |
| [az spring-cloud app binding redis update](#az-spring-cloud-app-binding-redis-update) | Mettre à jour une liaison de service Cache Azure pour Redis. |

| az spring-cloud app deployment | Commandes pour gérer le cycle de vie de déploiement d’une application dans Azure Spring Cloud. |
| --- | ---: |
| [az spring-cloud app deployment create](#az-spring-cloud-app-deployment-create) | Créer un déploiement intermédiaire pour l’application. |
| [az spring-cloud app deployment delete](#az-spring-cloud-app-deployment-delete) | Supprimer un déploiement de l’application. |
| [az spring-cloud app deployment list](#az-spring-cloud-app-deployment-list) | Lister tous les déploiements dans une application. |
| [az spring-cloud app deployment show](#az-spring-cloud-app-deployment-show) | Afficher les détails du déploiement. |

| az spring-cloud config-server | Commandes pour gérer le serveur de configuration Azure Spring Cloud. |
| --- | ---: |
| [az spring-cloud config-server clear](#az-spring-cloud-config-server-clear) | Effacer tous les paramètres du serveur de configuration. |
| [az spring-cloud config-server set](#az-spring-cloud-config-server-set) | Définir le serveur de configuration à partir d’un fichier YAML. |
| [az spring-cloud config-server show](#az-spring-cloud-config-server-show) | Afficher la configuration du serveur de configuration. |
| [az spring-cloud config server git set](#az-spring-cloud-config-server-git-set) | Définir les propriétés git du serveur de configuration.  Les valeurs précédentes seront remplacées. |
| [az spring-cloud config server git repo add](#az-spring-cloud-config-server-git-repo-add) | Ajouter une nouvelle configuration de dépôt git au serveur de configuration. |
| [az spring-cloud config server git repo list](#az-spring-cloud-config-server-git-repo-list) | Lister toutes les configurations de dépôt git pour le serveur de configuration. |
| [az spring-cloud config server git repo remove](#az-spring-cloud-config-server-git-repo-remove) | Supprimer le dépôt git spécifié du serveur de configuration. |

| az spring-cloud test-endpoint | Commandes pour gérer le test des points de terminaison dans Azure Spring Cloud |
| --- | ---: |
| [az spring-cloud test-endpoint disable](#az-spring-cloud-test-endpoint-disable) | Désactiver le point de terminaison de test. |
| [az spring-cloud test-endpoint enable](#az-spring-cloud-test-endpoint-enable) | Activer le point de terminaison de test. |
| [az spring-cloud test-endpoint list](#az-spring-cloud-test-endpoint-list) | Lister les clés de points de terminaison de test. |
| [az spring-cloud test-endpoint renew-key](#az-spring-cloud-test-endpoint-renew-key) | Regénérer une clé de point de terminaison de test. |

## <a name="az-spring-cloud-create"></a>az spring-cloud create

Créer une application avec un déploiement par défaut dans Azure Spring Cloud.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de cette instance Azure Spring Cloud. |
| --resource-group -g | Spécifie le groupe de ressources pour cette application.  Configurez le groupe par défaut à l’aide de `az configure --defaults group=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --location -l | Spécifie l’emplacement du serveur pour cette application.  Recherchez les emplacements valides à l’aide de `az account list-locations`. |
| --no-wait | Ne pas attendre la fin des opérations de longue durée.

### <a name="examples"></a>Exemples

Créer un Azure Spring Cloud dans westus

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az spring-cloud delete

Supprimer une instance Azure Spring Cloud.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de l’instance Azure Spring Cloud à supprimer. |
| --resource-group -g | Nom du groupe de ressources auquel appartient l’instance Azure Spring Cloud. |

| Paramètres facultatifs | Description |
| --- | :--- |
| -no-wait | Ne pas attendre la fin des opérations de longue durée. |

### <a name="example"></a>Exemple

Supprimer une instance Azure Spring Cloud nommée « MyService » de « MyResourceGroup ».

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az spring-cloud list

Lister toutes les instances Azure Spring Cloud associées au groupe de ressources donné. Si aucun groupe de ressources n’est spécifié, lister les ID d’abonnements.

```azurecli
az spring-cloud list --resource-group -g
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --resource-group -g | Nom du groupe de ressources. |

## <a name="az-spring-cloud-show"></a>az spring-cloud show

Afficher les détails de l’instance Azure Spring Cloud spécifiée.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de l’instance Azure Spring Cloud. |
| --resource-group -g | Nom du groupe de ressources auquel appartient l’instance Azure Spring Cloud.

## <a name="az-spring-cloud-app-create"></a>az spring-cloud app create

Créer une application dans un Azure Spring Cloud.

```azurecli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --cpu | Nombre de cœurs virtuels par instance.  Valeur par défaut : 1. |
| --enable-persistent-storage | Valeur booléenne.  Si la valeur est true, monte un disque 50 Go avec le chemin par défaut. |
| --instance-count | Nombre d’instances.  Valeur par défaut : 1. |
| --is-public | Valeur booléenne.  Si la valeur est true, assigne un domaine public. |
| --memory | Nombre de Go de mémoire par instance.  Valeur par défaut : 1. |

### <a name="examples"></a>Exemples

Créer une application avec la configuration par défaut.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Créer une application accessible publiquement avec trois instances.  Chaque instance a 3 Go de mémoire et deux cœurs d’UC.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az spring-cloud app delete

Supprime une application dans Azure Spring Cloud.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deploy"></a>az spring-cloud app deploy

Déployer une application sur Azure Spring Cloud à partir du code source ou d’un binaire prédéfini, et mettre à jour les configurations associées.

```azurecli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --cpu | Nombre de cœurs d’UC virtuels par instance. |
| --deployment -d | Nom d’un déploiement d’application existant.  Si rien n’est spécifié, la valeur par défaut est le déploiement de production. |
| --env | Variables d’environnement séparées par des espaces au format « clé[= valeur] ». |
| --instance-count | Nombre d’instances. |
| --jar-path | Si ce paramètre est fourni, déployer jar à partir du chemin donné. Sinon, déployer le dossier actuel en tant que tar. |
| --jvm-options | Chaîne contenant des options JVM.  Utilisez « = » au lieu de «  » pour éviter les erreurs d’analyse du shell. Par exemple, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memory | Nombre de Go de mémoire par instance. |
| --no-wait | Ne pas attendre la fin des opérations de longue durée. |
| --runtime-version | Version du runtime du langage utilisé dans l’application.  Valeurs autorisées : `Java_11`, `Java_8`. |
| --target-module | Module enfant à déployer.  Obligatoire quand plusieurs packages jar sont générés à partir du code source. |
| --version | Version du déploiement.  Inchangé s’il n’est pas défini. |

### <a name="examples"></a>Exemples

Déployer le code source dans une application. Cela compresse le répertoire actif, génère un fichier binaire à l’aide du service de build Pivotal, puis déploie dans l’application.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Déployer un fichier jar prédéfini dans une application à l’aide d’options JVM et de variables d’environnement.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Déployer le code source vers un déploiement spécifique d’une application.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az spring-cloud app list

Lister toutes les applications dans l’instance Azure Spring Cloud.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Paramètres obligatoires | Description |
| --- | :--- |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-restart"></a>az spring-cloud app restart

Redémarrer les instances de l’application.  Par défaut, il s’agit du déploiement de production.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --deployment -d | Nom du déploiement existant de l’application.  Si rien n’est spécifié, la valeur par défaut est le déploiement de production. |
| --no-wait | Ne pas attendre la fin des opérations de longue durée. |

## <a name="az-spring-cloud-app-scale"></a>az spring-cloud app scale

Mettre à l’échelle manuellement une application ou ses déploiements.

```azurecli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --cpu | Nombre de cœurs d’UC virtuels par instance d’application. |
| --deployment -d | Nom du déploiement existant de l’application.  Si rien n’est spécifié, la valeur par défaut est le déploiement de production. |
| --instance-count | Nombre d’instances de cette application. |
| --memory | Nombre de Go de mémoire par instance de l’application. |
| --no-wait | Ne pas attendre la fin des opérations de longue durée. |

### <a name="examples"></a>Exemples

Effectuer le scale-up d’une application avec quatre cœurs d’UC et 8 Go de mémoire par instance.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Effectuer le scale-up d’un déploiement de l’application sur cinq instances.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az spring-cloud app set-deployment

Définir les options de configuration pour le déploiement de production de l’application.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --deployment -d | Nom d’un déploiement existant de l’application. |
| --name -n | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --no-wait | Ne pas attendre la fin des opérations de longue durée. |

### <a name="examples"></a>Exemples

Faire basculer un déploiement intermédiaire de l’application en production.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az spring-cloud app show

Afficher les détails d’une application dans Azure Spring Cloud.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az spring-cloud app show-deploy-log

Afficher le journal de génération du dernier déploiement à partir du code source.  Par défaut, il s’agit de l’environnement de production.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --deployment -d | Nom d’un déploiement existant de l’application.  Par défaut, il s’agit de l’environnement de production. |

## <a name="az-spring-cloud-app-start"></a>az spring-cloud app start

Démarre des instances de l’application.  Par défaut, il s’agit de l’environnement de production.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --deployment -d | Nom d’un déploiement existant de l’application.  Par défaut, il s’agit de l’environnement de production. |
| --no-wait | Ne pas attendre la fin des opérations de longue durée. |

## <a name="az-spring-cloud-app-stop"></a>az spring-cloud app stop

Arrêter des instances de l’application.  Par défaut, il s’agit de l’environnement de production.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --deployment -d | Nom d’un déploiement existant de l’application.  Par défaut, il s’agit de l’environnement de production. |
| --no-wait | Ne pas attendre la fin des opérations de longue durée. |

## <a name="az-spring-cloud-app-update"></a>az spring-cloud app update

Mettre à jour la configuration stockée d’une application.

```azurecli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name -n | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --deployment -d | Nom d’un déploiement existant de l’application.  Par défaut, il s’agit de l’environnement de production. |
| --enable-persistent-storage | Propriété booléenne.  Si la valeur est true, monter un disque de 50 Go avec le chemin par défaut. |
| --env | Variables d’environnement séparées par des espaces au format « clé[= valeur] ». |
| --is-public | Propriété booléenne.  Si la valeur est true, assigner un domaine public à l’application. |
| --jvm-options | Chaîne contenant des options JVM.  Utilisez « = » au lieu de «  » pour éviter les erreurs d’analyse du shell. Par exemple, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --no-wait | Ne pas attendre la fin des opérations de longue durée. |
| --runtime-version | Version du runtime du langage utilisé dans l’application.  Valeurs autorisées : `Java_11`, `Java_8`. |

### <a name="example"></a>Exemple

Ajouter une variable d’environnement pour l’application.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az spring-cloud app binding list

Lister toutes les liaisons de service dans une application.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-remove"></a>az spring-cloud app binding remove

Supprimer une liaison de service de l’application.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --name | Nom de la liaison de service à supprimer. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-show"></a>az spring-cloud app binding show

Afficher les détails d’une liaison de service.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --name | Nom de la liaison de service. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az spring-cloud app binding cosmos add

Lier une base de données Azure Cosmos DB à l’application.

```azurecli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --api-type | Spécifiez le type d’API à l’aide de l’une des valeurs suivantes : cassandra, gremlin, mongo, sql, table. |
| --app | Nom de l’application. |
| --name | Nom de la liaison de service. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

|Paramètres facultatifs | Description |
| --- | :--- |
| --collection-name | Nom de la collection.  Obligatoire lors de l’utilisation de Gremlin. |
| --database-name | Nom de la base de données.  Obligatoire lors de l’utilisation de Mongo, SQL et Gremlin. |
| --key-space | Espace de clés Cassandra.  Obligatoire lors de l’utilisation de Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az spring-cloud app binding cosmos update

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --name | Nom de la liaison de service. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

|Paramètres facultatifs | Description |
| --- | :--- |
| --collection-name | Nom de la collection.  Obligatoire lors de l’utilisation de Gremlin. |
| --database-name | Nom de la base de données.  Obligatoire lors de l’utilisation de Mongo, SQL et Gremlin. |
| --key-space | Espace de clés Cassandra.  Obligatoire lors de l’utilisation de Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az spring-cloud app binding mysql add

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --database-name | Nom de la base de données. |
| --key | Clé API du service. |
| --name | Nom de la liaison de service. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --resource-id | ID de ressource Azure du service avec lequel effectuer la liaison. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |
| --username | Nom d’utilisateur pour l’accès à la base de données. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az spring-cloud app binding mysql update

Mettre à jour la connexion de liaison de service de l’application à une base de données Azure Database pour MySQL.

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --name | Nom de la liaison de service. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --database-name | Nom de la base de données. |
| --key | Clé API du service. |
| --username | Nom d’utilisateur pour l’accès à la base de données. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az spring-cloud app binding redis add

Lier un Cache Azure pour Redis avec l’application.

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --name | Nom de la liaison de service. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --resource-id | ID de ressource Azure du service avec lequel vous souhaitez effectuer la liaison. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --disable-ssl | Désactiver le protocole TLS. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az spring-cloud app binding redis update

Mettre à jour une liaison de service pour le Cache Azure pour Redis.

| Paramètres obligatoires | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --name | Nom de la liaison de service. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --disable-ssl | Désactiver le protocole TLS. |

## <a name="az-spring-cloud-app-deployment-create"></a>az spring-cloud app deployment create

Créer un déploiement intermédiaire pour l’application.

Pour déployer du code ou mettre à jour les paramètres d’un déploiement existant, utilisez `az spring-cloud app deploy --deployment <staging-deployment>` ou `az spring-cloud app update --deployment <staging deployment>.

| Paramètres obligatoires | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --name | Nom de la liaison de service. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --cpu | Nombre de cœurs d’UC virtuels par instance.  Valeur par défaut : 1 |
| --env | Variables d’environnement séparées par des espaces au format « clé[= valeur] ». |
| --instance-count | Nombre d’instances. Valeur par défaut : 1. |
| --jar-path | S’il est fourni, déployer jar.  Sinon, déployer le dossier actuel en tant que tar. |
| --jvm-options | Chaîne contenant des options JVM.  Utilisez « = » au lieu de «  » pour éviter les erreurs d’analyse du shell. Par exemple, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memory | Nombre de Go de mémoire par instance. |
| --no-wait | Ne pas attendre la fin des opérations de longue durée. |
| --runtime-version | Version du runtime du langage utilisé dans l’application.  Valeurs autorisées : `Java_11`, `Java_8`. |
| --skip-clone-settings | Créer un déploiement intermédiaire en clonant les paramètres de déploiement de production actuels. |
| --target-module | Module enfant à déployer.  Obligatoire quand plusieurs packages jar sont générés à partir du code source. |
| --version | Version du déploiement.  Inchangé s’il n’est pas défini. |

### <a name="examples"></a>Exemples

Déployer le code source vers un nouveau déploiement de l’application.  Cela compresse le répertoire actif, génère un fichier binaire à l’aide du service de build Pivotal, puis procède au déploiement.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Déployer un fichier jar prédéfini dans une application avec des options JVM et des variables d’environnement.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az spring-cloud app deployment delete

Supprimer un déploiement de l’application.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --name | Nom du déploiement. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-list"></a>az spring-cloud app deployment list

Lister tous les déploiements dans une application.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-app-deployment-show"></a>az spring-cloud app deployment show

Afficher les détails d’un déploiement.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --name | Nom du déploiement. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --service -s | Nom de l’instance Azure Spring Cloud.  Vous pouvez configurer le service par défaut à l’aide de `az configure --defaults spring-cloud=<name>`. |

## <a name="az-spring-cloud-config-server-clear"></a>az spring-cloud config-server clear

Effacer tous les paramètres de configuration du serveur de configuration.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name | Nom de l’instance Azure Spring Cloud. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-set"></a>az spring-cloud config-server set

Définir les paramètres de configuration sur le serveur de configuration à l’aide d’un fichier YAML.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --config-file | Chemin de fichier d’un manifeste YAML pour la configuration du serveur de configuration. |
| --name | Nom de l’instance Azure Spring Cloud. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --no-wait | Ne pas attendre la fin des opérations de longue durée.

## <a name="az-spring-cloud-config-server-show"></a>az spring-cloud config-server show

Afficher les paramètres du serveur de configuration.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name | Nom de l’instance Azure Spring Cloud. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-git-set"></a>az spring-cloud config-server git set

Définir les propriétés git du serveur de configuration.  Cela remplacera toutes les propriétés git existantes.

```azurecli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name | Nom de l’instance Azure Spring Cloud. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --uri | URI de la configuration ajoutée. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --defer | Stocker temporairement l’objet dans le cache local au lieu de l’envoyer à Azure.  Utilisez `az cache` pour voir/effacer. |
| --host-key | Clé d’hôte pour la configuration ajoutée. |
| --host-key-algorithm | Algorithme de clé d’hôte pour la configuration ajoutée. |
| --label | Étiquette de la configuration ajoutée. |
| --password | Mot de passe de la configuration ajoutée. |
| --private-key | Clé privée de la configuration ajoutée. |
| --search-paths | Chemins de recherche de la configuration ajoutée.  Utilisez des virgules pour séparer plusieurs chemins. |
| --strict-host-key-checking | Activer la vérification stricte des clés d’hôtes de la configuration ajoutée. |
| --username | Nom d’utilisateur de la configuration ajoutée. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az spring-cloud config-server git repo add

```azurecli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name | Nom de l’instance Azure Spring Cloud. |
| --repo-name | URI du dépôt. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --uri | URI de la configuration ajoutée. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --defer | Stocker temporairement l’objet dans le cache local au lieu de l’envoyer à Azure.  Utilisez `az cache` pour voir/effacer. |
| --host-key | Clé d’hôte pour la configuration ajoutée. |
| --host-key-algorithm | Algorithme de clé d’hôte pour la configuration ajoutée. |
| --label | Étiquette de la configuration ajoutée. |
| --password | Mot de passe de la configuration ajoutée. |
| --pattern | Modèle pour le dépôt.  Utilisez des virgules pour séparer plusieurs chemins.|
| --private-key | Clé privée de la configuration ajoutée. |
| --search-paths | Chemins de recherche de la configuration ajoutée.  Utilisez des virgules pour séparer plusieurs chemins. |
| --strict-host-key-checking | Activer la vérification stricte des clés d’hôtes de la configuration ajoutée. |
| --username | Nom d’utilisateur de la configuration ajoutée. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az spring-cloud config-server git repo list

Lister tous les dépôts git définis dans le serveur de configuration.

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name | Nom de l’instance Azure Spring Cloud. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --defer | Stocker temporairement l’objet dans le cache local au lieu de l’envoyer à Azure.  Utilisez `az cache` pour voir/effacer. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az spring-cloud config-server git repo remove

Supprimer une configuration de dépôt git existante du serveur de configuration.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name | Nom de l’instance Azure Spring Cloud. |
| --repo-name | URI du dépôt. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --defer | Stocker temporairement l’objet dans le cache local au lieu de l’envoyer à Azure.  Utilisez `az cache` pour voir/effacer. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az spring-cloud test-endpoint disable

Désactiver le point de terminaison de test de l’instance Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name | Nom de l’instance Azure Spring Cloud. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az spring-cloud test-endpoint enable

Activer le point de terminaison de test pour l’instance Azure Spring Cloud. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name | Nom de l’instance Azure Spring Cloud. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-list"></a>az spring-cloud test-endpoint list 

Lister les clés de points de terminaison de test disponibles pour l’instance Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name | Nom de l’instance Azure Spring Cloud. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |

| Paramètres facultatifs | Description |
| --- | :--- |
| --app | Nom de l’application. |
| --deployment -d | Nom d’un déploiement existant de l’application.  Si rien n’est spécifié, par défaut il s’agit du déploiement de production. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az spring-cloud test-endpoint renew-key

Regénérer une clé de point de terminaison de test pour l’instance Azure Spring Cloud.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Paramètres obligatoires | Description |
| --- | :--- |
| --name | Nom de l’instance Azure Spring Cloud. |
| --resource-group -g | Nom du groupe de ressources.  Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --type | Type de clé de point de terminaison de test.  Valeurs autorisées :  Primary, Secondary. |
