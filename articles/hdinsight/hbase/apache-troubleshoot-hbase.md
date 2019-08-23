---
title: Résolution de problèmes HBase à l’aide d’Azure HDInsight
description: Obtenez les réponses aux questions courantes sur l’utilisation de HBase et d’Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: cf44c27f51bf6312ef546b424646833f69ba0283
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638420"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Résolution de problèmes Apache HBase à l’aide d’Azure HDInsight

Découvrez les principaux problèmes rencontrés lors de l’utilisation de charges utiles Apache HBase dans Apache Ambari, et leur résolution.

## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Comment résoudre les problèmes de connectivité JDBC ou SQLLine avec Apache Phoenix ?

### <a name="resolution-steps"></a>Étapes de résolution

Pour vous connecter avec Apache Phoenix, vous devez fournir l’adresse IP d’un nœud Apache ZooKeeper actif. Assurez-vous que le service Zookeeper auquel l’utilitaire sqlline.py essaie de se connecter est en cours d’exécution.
1. Connectez-vous au cluster HDInsight à l’aide de SSH.
2. Entrez la commande suivante :
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Vous pouvez obtenir l’adresse IP du nœud ZooKeeper actif à partir de l’interface utilisateur d’Ambari. Accédez à **HBase** > **Quick Links** > **ZK\* (Active)**  > **Zookeeper Info** (HBase > Liens rapides > ZK (acitf) > Infos ZooKeeper).

3. Si l’utilitaire sqlline.py se connecte à Phoenix et n’expire pas, exécutez la commande suivante pour valider la disponibilité et l’intégrité de Phoenix :

   ```apache
           !tables
           !quit
   ```      
4. Si la commande fonctionne, il n’existe aucun problème. L’adresse IP fournie par l’utilisateur peut être incorrecte. Toutefois, si la commande s’interrompt pendant une durée prolongée, puis affiche l’erreur suivante, passez à l’étape 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Exécutez les commandes suivantes à partir du nœud principal (hn0) pour diagnostiquer l’état de la table Phoenix SYSTEM.CATALOG :

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   La commande doit renvoyer une erreur similaire à la suivante : 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. À partir de l’interface utilisateur Apache Ambari, suivez les étapes ci-dessous pour redémarrer le service HMaster sur tous les nœuds ZooKeeper :

    1. Dans la section **Summary** (Résumé) de HBase, accédez à **HBase** > **Active HBase Master** (HBase > HBase Master actif). 
    2. Dans la section **Components** (Composants), redémarrez le service HBase Master.
    3. Répétez ces étapes pour les services **Standby HBase Master** (Master HBase de secours) restants. 

La stabilisation et la récupération complète du service HBase Master peuvent prendre jusqu’à cinq minutes. Après quelques minutes, répétez les commandes sqlline.py pour confirmer que la table SYSTEM.CATALOG est activée et qu’elle peut être interrogée. 

Une fois la table SYSTEM.CATALOG revenue à son fonctionnement normal, le problème de connectivité à Phoenix devrait se résoudre automatiquement.

## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Pourquoi le redémarrage échoue-t-il sur un serveur régional ?

### <a name="issue"></a>Problème

L’échec du redémarrage d’un serveur régional peut être évité en respectant ces meilleures pratiques. Il est recommandé d’interrompre les activités impliquant de lourdes charges de travail lorsque vous prévoyez de redémarrer les serveurs régionaux HBase. Si une application continue de se connecter aux serveurs régionaux pendant l’arrêt, cela ralentit le redémarrage de ces derniers de plusieurs minutes. En outre, il est judicieux de commencer par vider toutes les tables. Pour des informations de référence sur le vidage des tables, consultez [HDInsight HBase : Comment améliorer le délai de redémarrage de cluster Apache HBase en vidant les tables](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Si vous lancez l’opération de redémarrage sur des serveurs régionaux HBase à partir de l’interface utilisateur d’Apache Ambari, vous voyez immédiatement que les serveurs régionaux s’arrêtent, mais qu’ils ne redémarrent pas immédiatement. 

Voici ce qui se passe en arrière-plan : 

1. Ambari Agent envoie une demande d’arrêt au serveur régional.
2. L’agent attend 30 secondes que le serveur s’arrête de manière appropriée. 
3. Si votre application continue à se connecter au serveur régional, ce dernier ne sera pas arrêté immédiatement. Le délai de 30 secondes expire avant l’arrêt. 
4. Après 30 secondes, Ambari Agent force l’arrêt du serveur régional en lui envoyant une commande force-kill (`kill -9`). Vous pouvez voir cet événement dans le journal ambari-agent (dans le répertoire /var/log/ du nœud de travail correspondant) :

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
   En raison de cet arrêt brutal, le port associé au processus ne peut pas être libéré même si le processus du serveur régional est arrêté. Cela peut entraîner l’exception AddressBindException au démarrage du serveur régional, comme indiqué dans les journaux d’activité suivants. Cet élément apparaît dans le journal region-server.log du répertoire /var/log/hbase des nœuds de travail où le démarrage des serveurs régionaux échoue. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>Étapes de résolution

1. Essayez de réduire la charge sur les serveurs régionaux HBase avant de lancer un redémarrage. 
2. Si l’étape 1 n’a aucun effet, vous pouvez également essayer de redémarrer manuellement les serveurs régionaux sur les nœuds de travail à l’aide des commandes suivantes :

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
