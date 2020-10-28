---
title: Obtenir les journaux pour résoudre les problèmes liés au contrôleur de données activé pour Azure Arc
description: Obtenez les journaux d’activité de service pour résoudre des problèmes de contrôleur de données activé par Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 625092e0557d40051e1ffd538a496c20edc0222f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320198"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Obtenir les journaux des services de données activés pour Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prérequis

Avant de continuer, vous avez besoin des éléments suivants :

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. [Instructions d’installation](./install-client-tools.md).
* Un compte administrateur pour se connecter au contrôleur de services de données activés pour Azure Arc.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Obtenir les journaux des services de données activés pour Azure Arc

Vous pouvez obtenir les journaux des services de données activés pour Azure Arc pour l’ensemble des pods ou pour des pods spécifiques à des fins de dépannage. Pour ce faire, vous pouvez utiliser les outils Kubernetes standard, comme la commande `kubectl logs`, mais, dans cet article, vous allez utiliser l’outil [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)], qui permet d’obtenir plus facilement tous les journaux en même temps.

1. Connectez-vous au contrôleur de données avec un compte administrateur.

   ```console
   azdata login
   ```

2. Exécutez la commande suivante pour sauvegarder les journaux :

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Par exemple :

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

Le contrôleur de données crée les fichiers journaux dans le répertoire de travail actuel dans un sous-répertoire appelé `logs`. 

## <a name="options"></a>Options

`azdata arc dc debug copy-logs` fournit les options suivantes pour gérer la sortie.

* Sortie des fichiers journaux dans un autre répertoire en utilisant le paramètre `--target-folder`.
* Compression des fichiers en omettant le paramètre `--skip-compress`.
* Déclenchement et inclusion des images mémoire en omettant le paramètre `--exclude-dumps`. Cette méthode n’est pas recommandée, sauf si Support Microsoft a demandé les images mémoire. Pour collecter l’image mémoire, le paramètre `allowDumps` du contrôleur de données doit être défini sur `true` lors de la création du contrôleur de données.
* Filtre pour collecter uniquement les journaux d’un pod (`--pod`) ou d’un conteneur (`--container`) spécifique par nom.
* Filtre pour collecter les journaux d’une ressource personnalisée spécifique en transmettant les paramètres `--resource-kind` et `--resource-name`. La valeur du paramètre `resource-kind` doit être l’un des noms de définition de ressource personnalisée que la commande `kubectl get customresourcedefinition` peut récupérer.

Avec ces paramètres, vous pouvez remplacer les `<parameters>` dans l’exemple suivant. 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Par exemple

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Exemple de hiérarchie de dossiers. L’arborescence des dossiers est organisée par nom de pod, puis par conteneur, et enfin par arborescence de répertoires au sein du conteneur.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>Étapes suivantes

[azdata arc dc debug copy-logs](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)