---
title: Erreur IllegalArgumentException pour Apache Spark – Azure HDInsight
description: IllegalArgumentException pour l’activité Apache Spark dans Azure HDInsight pour Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894377"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scénario : IllegalArgumentException pour l’activité Apache Spark dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Apache Spark dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Vous recevez l’exception suivante lors de la tentative d’exécution d’une activité Spark dans un pipeline Azure Data Factory :

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Cause :

Un travail Spark échoue si le fichier jar de l’application ne se trouve pas dans le stockage par défaut/principal du cluster Spark.

Il s’agit d’un problème connu avec l’infrastructure open source Spark suivi dans ce bogue : [Échec du travail Spark si fs.defaultFS et le fichier jar d’application sont des URL différentes](https://issues.apache.org/jira/browse/SPARK-22587).

Ce problème a été résolu dans Spark 2.3.0.

## <a name="resolution"></a>Résolution

Assurez-vous que le fichier jar de l’application est stocké sur le stockage par défaut/principal pour le cluster HDInsight. Dans le cas d’Azure Data Factory, assurez-vous que le service lié ADF est pointé vers le conteneur HDInsight par défaut plutôt qu’un conteneur secondaire.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
