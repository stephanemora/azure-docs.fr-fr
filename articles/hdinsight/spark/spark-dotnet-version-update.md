---
title: Mise à jour de .NET pour Apache Spark vers la version v1.0 dans HDI
description: En savoir plus sur la mise à jour de la version .NET pour Apache Spark vers 1.0 dans HDI et sur la façon dont cela affecte votre code et vos clusters existants.
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788127"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>Mise à jour de .NET pour Apache Spark vers la version v1.0 dans HDInsight

Ce document traite de la première version majeure de [.NET pour Apache Spark](https://github.com/dotnet/spark) et de l’impact qu’elle peut avoir sur vos pipelines de production actuels dans des clusters HDInsight.

## <a name="about-net-for-apache-spark-version-100"></a>À propos de .NET pour Apache Spark version 1.0.0

Il s’agit de la première [publication officielle majeure](https://github.com/dotnet/spark/releases/tag/v1.0.0) de .NET pour Apache Spark. Elle offre une exhaustivité de l’API DataFrame pour Spark 2.4.x et Spark 3.0.x, ainsi que d’autres fonctionnalités. Pour obtenir la liste complète des fonctionnalités, améliorations et correctifs de bogues, consultez les [notes de publication officielles de v1.0.0](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md).
Il est également important de noter que cette version n’est **pas** compatible avec les versions antérieures de `Microsoft.Spark` et `Microsoft.Spark.Worker`. Consultez le [guide de migration](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) si vous envisagez de mettre à niveau votre application .NET pour Apache Spark afin qu’elle soit compatible avec v1.0.0.

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>Utilisation de .NET pour Apache Spark v1.0 dans HDInsight

Alors que les clusters HDI actuels ne seront pas affectés (c’est-à-dire qu’ils auront toujours la même version qu’auparavant), les clusters HDI nouvellement créés comporteront cette dernière version v1.0.0 de .NET pour Apache Spark. Ce que cela signifie si :

- **Vous avez un cluster HDI plus ancien** : Si vous souhaitez mettre à niveau votre application .NET Spark vers v1.0.0 (recommandé), vous devez mettre à jour la version `Microsoft.Spark.Worker` sur votre cluster HDI. Pour plus d’informations, consultez la [section sur la modification des versions de .NET pour Apache Spark sur le cluster HDI](#changing-net-for-apache-spark-version-on-hdinsight).
Si vous ne souhaitez pas mettre à jour la version actuelle de .NET pour Apache Spark dans votre application, aucune autre étape n’est nécessaire.  

- **Vous disposez d’un nouveau cluster HDI** : Si vous souhaitez mettre à niveau votre application .NET Spark vers v1.0.0 (recommandé), aucune étape n’est nécessaire pour modifier le Worker sur HDI. Toutefois, vous devrez consulter le [guide de migration](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) pour comprendre les étapes nécessaires à la mise à jour de votre code et de vos pipelines.
Si vous ne souhaitez pas modifier la version actuelle de .NET pour Apache Spark dans votre application, vous devez remplacer la version v1.0 sur votre cluster HDI (par défaut sur les nouveaux clusters) par la version que vous utilisez. Pour plus d’informations, consultez la [section sur la modification des versions de .NET pour Apache Spark sur le cluster HDI](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight).  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>Modification de la version .NET pour Apache Spark sur HDInsight

### <a name="deploy-microsoftsparkworker"></a>Déployer Microsoft.Spark.Worker

`Microsoft.Spark.Worker` est un composant back-end qui réside sur les nœuds Worker individuels de votre cluster Spark. Lorsque vous souhaitez exécuter une fonction définie par l’utilisateur (UDF) C#, Spark doit comprendre comment lancer le CLR .NET pour exécuter cette fonction. `Microsoft.Spark.Worker` fournit une collection de classes à Spark qui active cette fonctionnalité. Sélectionnez la version de Worker en fonction de la version de .NET pour Apache Spark que vous souhaitez déployer sur le cluster HDI.

1. Téléchargez la version Linux de Microsoft.Spark.Worker pour votre version spécifique. Par exemple, si vous voulez `.NET for Apache Spark v1.0.0`, téléchargez [Microsoft.Spark.Worker.netcoreapp3.1.linux-x64-1.0.0.tar.gz](https://github.com/dotnet/spark/releases/tag/v1.0.0).  

2. Téléchargez le script [install-worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) pour installer les fichiers binaires du Worker téléchargés à l’étape 1 sur tous les nœuds Worker de votre cluster HDI.  

3. Chargez les fichiers mentionnés ci-dessus dans le compte de stockage Azure auquel votre cluster a accès. Pour plus d’informations, consultez [l’article sur le déploiement de .NET pour Apache Spark HDI](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure).

4. Exécutez le script `install-worker.sh` sur tous les nœuds Worker de votre cluster, à l’aide des actions Script. Pour plus d’informations, consultez [l’article sur le déploiement de .NET pour Apache Spark HDI](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action).

### <a name="update-your-application-to-use-specific-version"></a>Mettre à jour votre application pour utiliser une version spécifique

Vous pouvez mettre à jour votre application .NET pour Apache Spark afin d’utiliser une version spécifique en choisissant la version requise du [package NuGet Microsoft.Spark](https://www.nuget.org/packages/Microsoft.Spark/) dans votre projet. Veillez à consulter les notes de publication de la version et le [guide de migration](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) comme indiqué ci-dessus, si vous choisissez de mettre à jour votre application vers v1.0.0.

## <a name="faqs"></a>Foire aux questions

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>Mon cluster HDI existant avec une version < 1.0.0 va-t-il commencer à échouer avec la nouvelle version ?

Les clusters HDI existants continueront d’avoir la même version pour .NET pour Apache Spark et votre application existante (avec une version antérieure de .NET Spark) ne sera pas affectée.

## <a name="next-steps"></a>Étapes suivantes

[Déployer votre application .NET pour Apache Spark sur HDInsight](/dotnet/spark/tutorials/hdinsight-deployment)