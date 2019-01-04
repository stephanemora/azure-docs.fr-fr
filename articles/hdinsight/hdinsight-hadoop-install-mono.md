---
title: Installer ou mettre à niveau Mono sur HDInsight - Azure
description: Découvrez comment utiliser une version particulière de Mono avec le cluster HDInsight. Mono permet d’exécuter des applications .NET sur des clusters HDInsight sous Linux.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.custom: hdinsightactive
ms.openlocfilehash: 4f51de6ded29f93d29dbf80dd68715f621b5cb06
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384613"
---
# <a name="install-or-update-mono-on-hdinsight"></a>Installation ou mise à jour de Mono sur HDInsight

Apprenez à installer une version spécifique de [Mono](https://www.mono-project.com) sur HDInsight 3.4 ou version ultérieure.

Mono est installé sur HDInsight 3.4 et versions ultérieures et est utilisé pour exécuter des applications .NET. Pour plus d’informations sur la version de Mono fournie avec chaque version d’HDInsight, consultez [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md). Pour installer une autre version sur votre cluster, utilisez l’action de script de ce document. 

## <a name="how-it-works"></a>Fonctionnement

Le script accepte les paramètres suivants :

* __Numéro de version Mono__ : Version de Mono à installer. La version doit être disponible à l’adresse [https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

Le script installe les packages Mono suivants :

* __mono-complete__

* __ca-certificates-mono__

## <a name="the-script"></a>Le script

__Emplacement du script__ : [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Conditions requises__ :

* Le script doit être appliqué sur les __nœuds principaux__ et les __nœuds de travail__.

## <a name="to-use-the-script"></a>Pour utiliser le script

Pour plus d’informations sur l’utilisation de ce script avec HDInsight, consultez le document [Personnalisation de clusters HDInsight basés sur Linux à l'aide d'une action de script](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster). Vous pouvez utiliser le script dans le portail Azure, Azure PowerShell ou Azure Classic CLI.

Tout en respectant le document d’action de script, utilisez l’URI suivant :

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

Pour spécifier la version Mono qui est installée, utilisez le numéro de version dans le champ __Paramètres__. Par exemple, entrez `5.4` pour installer Mono 5.4.

> [!NOTE]  
> Lorsque vous configurez HDInsight avec ce script, définissez le script sur le statut __Persisted__ (Persistant). Ce paramètre permet à HDInsight d’appliquer le script aux nœuds de travail ajoutés par les opérations de mise à l’échelle.

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à mettre à niveau ou installer une version particulière de Mono sur HDInsight. Pour plus d’informations sur l’utilisation des applications .NET avec Mono sur HDInsight, consultez les documents suivants :

* [Utilisation de .NET pour la diffusion MapReduce sur HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Utilisation de .NET avec Apache Hive et Apache Pig sur HDInsight](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Développement de solutions C# avec Apache Storm sur HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
* [Migration de solutions .NET vers une version d’HDInsight pour Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md)

Pour plus d’informations sur l’utilisation des actions de script, consultez la page [Personnaliser des clusters HDInsight basés sur Linux à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster-linux.md).
