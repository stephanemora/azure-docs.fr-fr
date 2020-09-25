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
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930182"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Obtenir les journaux des services de données activés pour Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prérequis

Pour récupérer les journaux des services de données activés pour Azure Arc, vous avez besoin de l’outil Azure Data CLI. [Instructions d’installation](./install-client-tools.md)

Vous devez être en mesure de vous connecter au service du contrôleur de services de données activé pour Azure Arc en tant qu’administrateur.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Obtenir les journaux des services de données activés pour Azure Arc

Vous pouvez obtenir les journaux des services de données activés pour Azure Arc pour l’ensemble des pods ou pour des pods spécifiques à des fins de dépannage.  Pour ce faire, vous pouvez utiliser les outils Kubernetes standard, comme la commande `kubectl logs`, mais dans cet article, vous allez utiliser l’outil Azure Data CLI, qui permet d’obtenir plus facilement tous les journaux en même temps.

Tout d’abord, vérifiez que vous êtes connecté au contrôleur de données.

```console
azdata login
```

Ensuite, exécutez la commande suivante pour vider les journaux :
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

Les fichiers journaux sont créés dans le répertoire de travail actuel par défaut, dans un sous-répertoire appelé « logs ».  Vous pouvez générer les fichiers journaux dans un autre répertoire en utilisant le paramètre `--target-folder`.

Vous pouvez choisir de compresser les fichiers en omettant le paramètre `--skip-compress`.

Vous pouvez déclencher et inclure les images mémoire en omettant `--exclude-dumps`, mais ce n’est pas recommandé, sauf si le support Microsoft a demandé les images mémoire.  Pour collecter l’image mémoire, le paramètre `allowDumps` du contrôleur de données doit être défini sur `true` lors de la création du contrôleur de données.

Vous pouvez aussi choisir de filtrer la collecte des journaux pour un pod (`--pod`) ou un conteneur (`--container`) spécifique en utilisant son nom.

Vous pouvez également choisir de filtrer pour collecter des journaux d’activité pour une ressource personnalisée spécifique en passant les paramètres `--resource-kind` et `--resource-name`.  La valeur du paramètre `resource-kind` doit être l’un des noms de définition de ressource personnalisée que la commande `kubectl get customresourcedefinition` peut récupérer.

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Exemple de hiérarchie de dossiers.  Notez que la hiérarchie de dossiers est organisée par nom de pod, puis par conteneur, et enfin par hiérarchie de répertoires au sein du conteneur.

```console
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