---
title: 'BindException : adresse déjà utilisée dans Azure HDInsight'
description: 'BindException : adresse déjà utilisée dans Azure HDInsight'
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: 8851a4dfb7deafab7ad77ef80619dd49ca46ed71
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947928"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Scénario : BindException : adresse déjà utilisée dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Échec de l’opération de redémarrage du serveur de région Apache HBase. Dans le journal `region-server.log` dans le répertoire `/var/log/hbase` des nœuds worker où le démarrage des serveurs de région échoue, vous pouvez voir un message d’erreur semblable à ce qui suit :

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Cause :

Redémarrage des serveurs de région HBase pendant une activité impliquant de lourdes charges de travail. Voici ce qui se passe en arrière-plan quand un utilisateur lance l’opération de redémarrage sur le serveur de région HBase à partir de l’interface utilisateur Ambari :

1. Ambari Agent envoie une demande d’arrêt au serveur régional.

1. L’agent Ambari attend ensuite 30 secondes que le serveur de région s’arrête de manière appropriée.

1. Si l’application continue de se connecter au serveur de région, ce dernier ne s’arrête pas immédiatement et le délai d’expiration de 30 secondes est dépassé.

1. Après l’expiration des 30 secondes, Ambari Agent force l’arrêt du serveur régional (kill -9).

1. En raison de cet arrêt brutal et même si le processus du serveur de région est arrêté, le port associé au processus peut ne pas être libéré, ce qui aboutit en fin de compte à `AddressBindException`.

## <a name="resolution"></a>Résolution :

Réduisez la charge sur les serveurs de région HBase avant de lancer un redémarrage.

Vous pouvez également essayer de redémarrer manuellement les serveurs de région sur les nœuds Worker à l’aide des commandes suivantes :

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
