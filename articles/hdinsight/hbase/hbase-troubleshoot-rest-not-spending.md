---
title: Le REST Apache HBase ne répond pas aux requêtes dans Azure HDInsight
description: Résoudre le problème où l’API REST Apache HBase ne répond pas aux requêtes dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887170"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scénario : Le REST Apache HBase ne répond pas aux requêtes dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Le service REST Apache HBase ne répond pas aux requêtes dans Azure HDInsight.

## <a name="cause"></a>Cause

La cause possible ici peut être que le service REST Apache HBase présente des fuites de sockets, ce qui est particulièrement courant lorsque le service s’exécute depuis longtemps (par exemple, des mois). À partir du kit de développement logiciel (SDK) client, un message d’erreur semblable à celui-ci peut s’afficher :

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Résolution

Redémarrez le REST HBase à l’aide de la commande ci-dessous après par SSH sur l’hôte. Vous pouvez également utiliser des actions de script pour redémarrer ce service sur tous les nœuds Worker :

```bash
sudo service hdinsight-hbrest restart
```

Cette commande arrête HBase Region Server sur le même hôte. Vous pouvez soit démarrer manuellement HBase Region Server via Ambari, soit laisser la fonctionnalité de redémarrage automatique d’Ambari récupérer HBase Region Server automatiquement.

Si le problème persiste, vous pouvez installer le script d’atténuation suivant sous la forme d’une tâche CRON qui s’exécute toutes les 5 minutes sur chaque nœud Worker. Ce script d’atténuation effectue un test ping sur le service REST et le redémarre en cas de non-réponse du service REST.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
