---
title: Apache Spark lent lorsque le stockage Azure HDInsight contient de nombreux fichiers
description: Un travail Apache Spark s’exécute lentement quand le conteneur de stockage Azure contient un grand nombre de fichiers dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894324"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Un travail Apache Spark s’exécute lentement quand le conteneur de stockage Azure contient de nombreux fichiers dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Apache Spark dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Pendant l’exécution d’un cluster HDInsight, le travail Apache Spark qui écrit dans le conteneur de stockage Azure ralentit en présence de nombreux fichiers/sous-dossiers. Par exemple, le travail dure 20 secondes lors de l’écriture dans un nouveau conteneur, mais environ 2 minutes lors de l’écriture dans un conteneur comprenant 200 000 fichiers.

## <a name="cause"></a>Cause :

Il s’agit d’un problème connu avec Spark. La lenteur est due aux opérations `ListBlob` et `GetBlobProperties` pendant l’exécution du travail Spark.

Pour suivre les partitions, Spark doit gérer un `FileStatusCache` qui contient des informations sur la structure de répertoires. À l’aide de ce cache, Spark peut analyser les chemins et connaître les partitions disponibles. Avec le suivi des partitions, Spark accède uniquement aux fichiers nécessaires quand vous lisez des données. Pour garder ces informations à jour, quand vous écrivez de nouvelles données, Spark doit lister tous les fichiers sous le répertoire et mettre à jour ce cache.

Dans Spark 2,1, même si nous n’avons pas besoin de mettre à jour le cache après chaque écriture, Spark vérifie si une colonne de partition existante correspond à celle proposée dans la demande d’écriture actuelle. Cette opération entraîne de nombreuses opérations de listing au début de chaque écriture.

Dans Spark 2,2, quand des données sont écrites avec le mode Append, ce problème de performances doit être résolu.

## <a name="resolution"></a>Résolution

Quand vous créez un jeu de données partitionné, il est important d’utiliser un schéma de partitionnement qui limite le nombre de fichiers que Spark doit lister pour mettre à jour le `FileStatusCache`.

Pour chaque énième micro-lot où N % 100 == 0 (100 n’est qu’un exemple), déplacez les données existantes vers un autre répertoire, qui peut être chargé par Spark.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
