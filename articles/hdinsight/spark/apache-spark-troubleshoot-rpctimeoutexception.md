---
title: RpcTimeoutException pour Apache Spark Thrift – Azure HDInsight
description: Vous constatez 502 erreurs lors du traitement de jeux de données volumineux à l’aide du serveur Thrift Apache Spark
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894280"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scénario : RpcTimeoutException pour le serveur Thrift Apache Spark dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Apache Spark dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

L’application Spark échoue avec `org.apache.spark.rpc.RpcTimeoutException` une exception et un message : `Futures timed out`, comme le montre l’exemple suivant :

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` et `overhead limit exceeded` des erreurs peuvent également survenir dans le `sparkthriftdriver.log`, comme le montre l’exemple suivant :

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Cause :

Ces erreurs sont dues à un manque de ressources mémoire lors du traitement des données. Si le processus de nettoyage de la mémoire de Java démarre, il peut causer le retrait de l’application Spark. Les requêtes commencent à expirer et à arrêter le traitement. L’erreur `Futures timed out` indique qu’un cluster est soumis à de fortes contraintes.

## <a name="resolution"></a>Résolution

Augmentez la taille du cluster en ajoutant plus de nœuds Worker ou en augmentant la capacité de mémoire des nœuds de cluster existants. Vous pouvez également ajuster le pipeline des données pour réduire la quantité de données traitées en même temps.

Le `spark.network.timeout` contrôle le délai d’expiration pour toutes les connexions réseau. L’augmentation du délai d’expiration du réseau donne le temps nécessaires pour que certaines opérations critiques puissent se terminer, mais ne permettra pas de résoudre complètement le problème.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
