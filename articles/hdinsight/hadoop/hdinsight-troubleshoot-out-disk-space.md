---
title: L’espace disque est insuffisant sur le nœud de cluster dans Azure HDInsight
description: Dépannage des problèmes d'espace disque des nœuds d’un cluster Apache Hadoop dans Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894127"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scénario : L’espace disque est insuffisant sur le nœud de cluster dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Un travail peut échouer avec un message d’erreur semblable à celui-ci : `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Vous pouvez également recevoir une alerte Apache Ambari similaire à ce qui suit : `local-dirs usable space is below configured utilization percentage`.

## <a name="cause"></a>Cause :

Le cache d’application Apache Yarn peut avoir consommé tout l’espace disque disponible. Votre application Spark s’exécute probablement de manière inefficace.

## <a name="resolution"></a>Résolution

1. Utilisez l’interface utilisateur Ambari pour déterminer quel nœud manque d’espace disque.

1. Déterminez quel dossier du nœud en difficulté contribue à la plus grande partie de l'espace disque. Commencez par exécuter SSH sur le nœud, puis exécutez `df` pour répertorier l’utilisation du disque pour tous les montages. En général, `/mnt` est un disque temporaire utilisé par OSS. Vous pouvez entrer dans un dossier, puis taper `sudo du -hs` pour afficher les tailles de fichiers résumés sous un dossier. Si vous voyez un dossier similaire à `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`, cela signifie que l’application est toujours en cours d’exécution. Cela peut être dû à la persistance RDD ou aux fichiers de lecture aléatoire intermédiaires.

1. Pour atténuer le problème, arrêtez l’application, qui libérera l’espace disque utilisé par cette application.

1. Pour finalement résoudre le problème, optimisez votre application.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
