---
title: 'Erreur « watchdog: BUG: soft lockup - CPU » de cluster Azure HDInsight'
description: 'L’erreur « watchdog: BUG: soft lockup - CPU » apparaît dans les journaux syslogs de noyau du cluster Azure HDInsight'
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894101"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scénario : Erreur « watchdog: BUG: soft lockup - CPU » sur un cluster Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Les journaux syslogs du noyau contiennent le message d’erreur : `watchdog: BUG: soft lockup - CPU`.

## <a name="cause"></a>Cause

Un [bogue](https://bugzilla.kernel.org/show_bug.cgi?id=199437) dans le noyau Linux est à l’origine de verrouillages logiciels du processeur.

## <a name="resolution"></a>Résolution

Appliquer le correctif du noyau. Le script ci-dessous met à niveau le noyau Linux et redémarre les machines à différents moments au cours des 24 heures. Exécutez l’action de script dans deux lots. Le premier lot se trouve sur tous les nœuds, à l’exception du nœud principal. Le deuxième lot est sur le nœud principal. Ne s’exécute pas sur le nœud principal et d’autres nœuds en même temps.

1. Accédez à votre cluster HDInsight à partir du portail Azure.

1. Accédez aux actions de script.

1. Sélectionnez **Envoyer**, puis entrez ce qui suit

    | Propriété | Valeur |
    | --- | --- |
    | Type de script | -Personnalisé |
    | Nom |Correctif pour le problème de verrouillage logiciel du noyau |
    | URI de script bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Type(s) de nœud |Worker, Zookeeper |
    | Paramètres |N/A |

    Sélectionnez **Conservez cette action de script...**  si vous souhaitez exécuter le script lorsque des nœuds sont ajoutés.

1. Sélectionnez **Create** (Créer).

1. Attendez que l’exécution aboutisse.

1. Exécutez l’action de script sur le nœud principal en procédant comme à l’étape 3, mais cette fois avec des types de nœuds : Principal.

1. Attendez que l’exécution aboutisse.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
