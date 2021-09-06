---
title: Obtenir les journaux pour résoudre les problèmes liés aux services de données activés par Azure Arc
description: Découvrez comment obtenir des fichiers journaux à partir d’un contrôleur de données pour résoudre les problèmes liés aux services de données activés par Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 82152756b5caf5bfbe0301a14185d8ffed1d1afe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531626"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>Obtenir les journaux pour résoudre les problèmes liés aux services de données activés par Azure Arc


## <a name="prerequisites"></a>Prérequis

Avant de continuer, vérifiez que vous disposez des éléments suivants :

* Azure CLI (`az`) avec l’extension `arcdata`. Pour plus d’informations, consultez [Installer les outils clients pour le déploiement et la gestion des services de données activés par Azure Arc](./install-client-tools.md).
* Un compte administrateur pour se connecter au contrôleur de données Azure Arc.

## <a name="get-log-files"></a>Obtenir les fichiers journaux

Vous pouvez obtenir les journaux de service pour l’ensemble des pods ou pour des pods spécifiques à des fins de dépannage. L’une des méthodes consiste à utiliser les outils Kubernetes standard, tels que la commande `kubectl logs`. Dans cet article, vous allez utiliser l’extension `arcdata` d’Azure CLI (`az`), qui facilite l’extraction de tous les journaux en même temps.

Exécutez la commande suivante pour sauvegarder les journaux :

   ```azurecli
   az arcdata dc debug copy-logs --exclude-dumps --skip-compress
   ```

   Par exemple :

   ```azurecli
   #az arcdata dc debug copy-logs --exclude-dumps --skip-compress
   ```

Le contrôleur de données crée les fichiers journaux dans le répertoire de travail actuel dans un sous-répertoire appelé `logs`. 

## <a name="options"></a>Options

La commande `az arcdata dc debug copy-logs` fournit les options suivantes pour gérer la sortie :

* Sortie des fichiers journaux dans un autre répertoire en utilisant le paramètre `--target-folder`.
* Compression des fichiers en omettant le paramètre `--skip-compress`.
* Déclenchement et inclusion des images mémoire en omettant le paramètre `--exclude-dumps`. Nous ne recommandons pas cette méthode, sauf si Support Microsoft a demandé les images mémoire. Pour collecter une image mémoire, le paramètre `allowDumps` du contrôleur de données doit être défini sur `true` lors de la création du contrôleur de données.
* Filtre pour collecter uniquement les journaux d’un pod (`--pod`) ou d’un conteneur (`--container`) spécifique par nom.
* Filtre pour collecter les journaux d’une ressource personnalisée spécifique en transmettant les paramètres `--resource-kind` et `--resource-name`. La valeur du paramètre `resource-kind` doit être l’un des noms de définition de ressource personnalisée. Vous pouvez récupérer ces noms à l’aide de la commande `kubectl get customresourcedefinition`.

Avec ces paramètres, vous pouvez remplacer les `<parameters>` dans l’exemple suivant : 

```azurecli
az arcdata dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name>
```

Par exemple :

```console
#az arcdata dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 
```

L’arborescence des dossiers suivante est un exemple. Elle est organisée par nom de pod, puis par conteneur, et enfin par arborescence de répertoires au sein du conteneur.

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

