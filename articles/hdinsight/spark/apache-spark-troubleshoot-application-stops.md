---
title: L’application de streaming Apache Spark s’arrête au bout de 24 jours dans Azure HDInsight
description: Une application de diffusion en continu Apache Spark s’arrête après 24 jours d'exécution sans erreurs dans les fichiers journaux.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894431"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scénario : L’application de diffusion en continu Apache Spark s’arrête après 24 jours d'exécution dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Apache Spark dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Une application de diffusion en continu Apache Spark s’arrête après 24 jours d'exécution sans erreurs dans les fichiers journaux.

## <a name="cause"></a>Cause

La valeur `livy.server.session.timeout` contrôle le laps de temps durant lequel Apache Livy doit attendre la fin d’une session. Une fois la valeur `session.timeout` atteinte, la session Livy et l'application sont automatiquement supprimées.

## <a name="resolution"></a>Résolution

Pour les travaux de longue durée, augmentez la valeur de `livy.server.session.timeout` à l’aide de l’interface utilisateur Ambari. Vous pouvez accéder à la configuration Livy à partir de l’interface utilisateur Ambari en utilisant l’URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`.

Remplacez `<yourclustername>` par le nom de votre cluster HDInsight tel qu'indiqué dans le portail.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
