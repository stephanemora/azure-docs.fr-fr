---
title: Résolution de problèmes HDFS dans Azure HDInsight
description: Obtenez les réponses aux questions courantes sur l’utilisation de HDFS et d’Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.custom: seodec18
ms.openlocfilehash: a5dcd7d2204e7ec03bf6b11bce9be20870cb3054
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076462"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Résoudre les problèmes d’Apache Hadoop HDFS avec Azure HDInsight

Découvrez les principaux problèmes rencontrés lors de l’utilisation de charges utiles HDFS (Hadoop Distributed File System) dans Apache Ambari, et leur résolution.

## <a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Comment accéder au stockage HDFS local depuis un cluster ?

### <a name="issue"></a>Problème

Accédez au stockage HDFS local à l’aide de la ligne de commande et du code d’application plutôt qu’à partir du Stockage Blob Azure ou d’Azure Data Lake Storage à partir du cluster HDInsight.   

### <a name="resolution-steps"></a>Étapes de résolution

1. Dans la ligne de commande, utilisez `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` littéralement, comme dans la commande suivante :

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. À partir du code source, utilisez l’URI `hdfs://mycluster/` littéralement, comme dans l’exemple d’application suivant :

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Exécutez le fichier .jar compilé (par exemple un fichier nommé `java-unit-tests-1.0.jar`) sur le cluster HDInsight à l’aide de la commande suivante :

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).