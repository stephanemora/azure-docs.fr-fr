---
title: Mise à niveau HDP et aucune donnée dans les vues Apache Phoenix dans Azure HDInsight
description: La mise à niveau HDP ne génère aucune donnée dans les vues Apache Phoenix dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: f68e227e10a85b7e1d98dcabbfb870933059ec04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936938"
---
# <a name="scenario-hdp-upgrade-causes-no-data-in-apache-phoenix-views-in-azure-hdinsight"></a>Scénario : la mise à niveau HDP ne génère aucune donnée dans les affichages Apache Phoenix dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Une vue Apache Phoenix ne contient aucune date après la mise à niveau de HDP 2.4 vers HDP 2.5.

## <a name="cause"></a>Cause

La table d’index pour les vues (tous les index d’une vue sont stockés dans une seule table Apache HBase physique) est tronquée pendant la mise à niveau

## <a name="resolution"></a>Résolution

Supprimez et recréez tous les index d’une vue après la mise à niveau.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).