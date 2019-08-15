---
title: La vue Apache Ambari Tez se charge lentement dans Azure HDInsight
description: La vue Apache Ambari Tez peut se charger lentement ou ne pas se charger du tout
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 70e8ef1f36de30d659e09a15aa935f7a5b7693d6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841920"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scénario : La vue Apache Ambari Tez se charge lentement dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Interactive Query dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

La vue Apache Ambari Tez peut se charger lentement ou ne pas se charger du tout. Lors du chargement de la vue Ambari Tez, il se peut que les processus sur nœuds principaux cessent de répondre.

## <a name="cause"></a>Cause :

L’accès aux API Yarn ATS peut parfois avoir des performances médiocres sur les clusters créés avant octobre 2017 lorsque le cluster est exécuté sur un grand nombre de travaux Hive.

## <a name="resolution"></a>Résolution :

Il s’agit d’un problème qui a été corrigé en octobre 2017. Si vous recréez votre clustervous ne rencontrerez plus ce problème.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une demande de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus dans votre abonnement Microsoft Azure. En outre, un support technique est fourni via l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
