---
title: L’interface utilisateur Apache Ambari affiche les hôtes et les services dans Azure HDInsight
description: Dépannage d’un problème d’interface utilisateur Apache Ambari qui affiche les hôtes et les services dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895637"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scénario : L’interface utilisateur Apache Ambari affiche les hôtes et les services dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

L’interface utilisateur Apache Ambari est accessible, mais l’interface utilisateur indique que presque tous les services sont en panne, tous les ordinateurs hôtes présentent une perte de pulsations.

## <a name="cause"></a>Cause :

Dans la plupart des scénarios, il s’agit d’un problème avec le serveur Ambari qui ne s’exécute pas sur le nœud principal actif. Vérifiez quel nœud principal est le nœud principal actif et assurez-vous que votre serveur Ambari s’exécute sur le serveur approprié. Ne démarrez pas manuellement ambari-server, laissez le service de contrôleur de basculement responsable du démarrage de ambari-Server sur le nœud principal de droite. Redémarrez le nœud principal actif pour forcer un basculement.

Des problèmes de mise en réseau peuvent également être à l’origine de ce problème. À partir de chaque nœud de cluster, vérifiez si vous pouvez exécuter une commande ping sur `headnodehost`. Il existe une situation rare dans laquelle aucun nœud de cluster ne peut se connecter à `headnodehost`:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Résolution

En général, le redémarrage du nœud principal actif atténue ce problème. Si ce n’est pas le cas, contactez l’équipe d’assistance technique HDInsight.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
