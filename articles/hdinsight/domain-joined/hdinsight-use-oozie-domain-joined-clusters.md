---
title: Workflows Hadoop Oozie dans les clusters HDInsight joints à un domaine
description: Utilisez Hadoop Oozie dans le Pack Sécurité Entreprise HDInsight joint à un domaine et basé sur Linux. Découvrez comment définir un workflow Oozie et envoyer un travail Oozie.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 928f6adbb348683a110f7da9b20efaae998290ca
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972211"
---
#<a name="run-apache-oozie-in-domain-joined-hdinsight-hadoop-clusters"></a>Exécuter Apache Oozie dans des clusters Hadoop HDInsight joints à un domaine
Oozie est un workflow et un système de coordination qui gère les travaux Hadoop. Oozie est intégré dans la pile Hadoop et prend en charge les travaux suivants :
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Vous pouvez également utiliser Oozie pour planifier des travaux propres à un système, comme des programmes Java ou des scripts de l’interpréteur de commandes.

##<a name="prerequisites"></a>Configuration requise :
- Un cluster HDInsight Hadoop joint à un domaine. Consultez [Configuration de clusters HDInsight joints à un domaine](./apache-domain-joined-configure-using-azure-adds.md).

    > [!NOTE]
    > Pour voir des instructions détaillées sur l’utilisation d’Oozie avec des clusters joints à un domaine, [cliquez ici](../hdinsight-use-oozie-linux-mac.md)

##<a name="connecting-to-domain-joined-cluster"></a>Connexion à un cluster joint à un domaine
Pour plus d’informations sur SSH, consultez [Authentification : HDInsight joint à un domaine](../hdinsight-hadoop-linux-use-ssh-unix.md).
- Connectez-vous au cluster HDInsight à l’aide de SSH :
     ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```
Pour vérifier si l’authentification Kerberos a réussi, utilisez la commande `klist`. Si ce n’est pas le cas, utilisez `kinit` pour lancer l’authentification Kerberos.

- Se connecter à la passerelle HDInsight pour inscrire le jeton oAuth nécessaire à l’accès Azure Data Lake Store (ADLS)
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Le code de réponse d’état_200 OK_ indique la réussite de l’inscription. Si vous recevez une erreur 401 (inscription non autorisée), vérifiez le nom d’utilisateur et le mot de passe.

## <a name="define-the-workflow"></a>Définition du flux de travail
Les définitions de workflow Oozie sont écrites en langage de définition de processus Hadoop (hPDL), qui est un langage de définition de processus XML. Pour définir le flux de travail, procédez comme suit :

-   Configuration de l’espace de travail de l’utilisateur de domaine :
 ```bash
hdfs dfs -mkdir /user/<DomainUser>
cd /home/<DomainUserPath>
cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
tar -xvf oozie-examples.tar.gz
hdfs dfs -put examples /user/<DomainUser>/
 ```
Remplacez `DomainUser` par le nom de l’utilisateur de domaine. Remplacez `DomainUserPath` par le chemin du répertoire de base de l’utilisateur de domaine. Remplacez `ClusterVersion` par la version HDP de votre cluster.

-   Pour créer et modifier un fichier, utilisez l’instruction suivante :
 ```bash
nano workflow.xml
 ```

- Une fois que l’éditeur nano est ouvert, saisissez le code XML suivant comme contenu du fichier :
 ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
 ```
Remplacez `clustername` par le nom du cluster. 

Pour enregistrer le fichier, sélectionnez Ctrl + X, entrez `Y`, puis sélectionnez **Entrée**.

Le workflow comprend deux parties :
*   Section Informations d’identification : la section Informations d’identification comprend les informations d’identification qui seront utilisées pour l’authentification des actions Oozie :

    Dans cet exemple, l’authentification des actions Hive est utilisée. Pour plus d’informations, [cliquez ici]( https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

    Le service d’informations d’identification permet aux actions Oozie d’emprunter l’identité de l’utilisateur pour accéder aux services Hadoop.

*   Section Action : Nous avons ici trois actions : map-reduce, hive server 2 et hive server 1 :

    L’action map-reduce exécute un exemple de package Oozie pour map-reduce qui génère le nombre de mots agrégés.

    Les actions hive server 2 et hive server 1 exécutent une requête simple sur la table hivesample qui est fournie avec HDInsight.

    Les actions hive utilisent les informations d’identification définies dans la section Informations d’identification en vue d’une authentification à l’aide du mot clé `cred` dans l’élément action.

- Utilisez la commande suivante pour copier le fichier `workflow.xml` vers `/user/<domainuser>/examples/apps/map-reduce/workflow.xml` :
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

    Remplacez `domainuser` par le nom d’utilisateur de votre domaine.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Définir le fichier de propriétés de la tâche Oozie

1.  Pour créer et modifier un fichier de propriétés de tâche, utilisez l’instruction suivante :
     ```bash
    nano job.properties
     ```

2.   Une fois que l’éditeur nano est ouvert, utilisez le code XML suivant comme contenu du fichier :

    ```bash
        nameNode=adl://home
        jobTracker=headnodehost:8050
        queueName=default
        examplesRoot=examples
        oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
        hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
        hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
        oozie.use.system.libpath=true
        user.name=[domainuser]
        jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
        jdbcURL=[jdbcurlvalue]
        hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
        hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
    ```
    

   * Remplacez `domainuser` par le nom d’utilisateur de votre domaine.
   * Remplacez `ClusterShortName` par le nom court du cluster. Si le nom du cluster est https://sechadoopcontoso.azurehdisnight.net, `clustershortname` correspond aux six premières lettres du cluster : sechad
   * Remplacez `jdbcurlvalue` par l’URL JDBC du fichier config Hive. Par exemple, jdbc:hive2://headnodehost:10001/;transportMode=http .
    
   * Pour enregistrer le fichier, sélectionnez Ctrl + X, entrez `Y`, puis sélectionnez **Entrée**.

   * Ce fichier de propriétés doit être présent localement lors de l’exécution des tâches Oozie.

## <a name="creating-custom-hive-scripts-for-the-oozie-job"></a>Création de scripts Hive personnalisés pour la tâche Oozie
Vous pouvez créer les scripts hive pour hive server 1 et hive server 2 de la manière suivante :
-   Fichier Hive Server 1 :
1.  Pour créer et modifier un fichier pour l’action hive server 1, utilisez l’instruction suivante :
    ```bash
    nano countrowshive1.hql
    ```

2.  Créez le script.
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Enregistrez le fichier dans HDFS.
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

-   Fichier Hive Server 2 :
1.  Pour créer et modifier un fichier pour l’action hive server 2, utilisez l’instruction suivante :
    ```bash
    nano countrowshive2.hql
    ```

2.  Créez le script.
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Enregistrez le fichier dans HDFS.
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submission-of-oozie-jobs"></a>Envoi des tâches Oozie
L’envoi des tâches Oozie pour les clusters joints à un domaine est similaire à celui des tâches Oozie des clusters non joints à un domaine.

Pour plus d’informations, consultez [Soumission et gestion du travail](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-oozie-job-submission"></a>Résultats de l’envoi des tâches Oozie
Dans la mesure où les tâches Oozie sont exécutées à la place de l’utilisateur, les journaux d’audit Yarn et Ranger montrent les tâches exécutées par l’utilisateur impersonné. La sortie de la tâche Oozie dans l’interface CLI ressemble à ceci :


```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

*    Les journaux d’audit Ranger pour les actions hive server 2 montrent Oozie qui exécute l’action à la place de l’utilisateur. Seul l’administrateur du cluster peut voir les vues Ranger et Yarn.

## <a name="configuration-of-user-authorization-in-oozie"></a>Configuration des autorisations utilisateur dans Oozie
Oozie permet de configurer les autorisations des utilisateurs, de manière à les empêcher d’arrêter ou de supprimer les tâches des autres utilisateurs. Cette option peut être activée en définissant `oozie.service.AuthorizationService.security.enabled` sur `true`. 

Pour plus d’informations, consultez la section [User Authorization Configuration]( https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html) (Configuration des autorisations utilisateur) de la documentation Oozie :

Pour les composants tels que hive server 1, où le plug-in Ranger n’est pas disponible ou pris en charge, seule l’autorisation HDFS de granularité grossière est possible. L’autorisation de granularité fine est uniquement disponible via les plug-ins Ranger.

## <a name="oozie-web-ui"></a>Interface utilisateur Web Oozie
L’interface utilisateur web Oozie fournit une vue web de l’état des travaux Oozie sur le cluster. Dans les clusters joints à un domaine, vous devez :

1. Ajouter un [nœud de périphérie](../hdinsight-apps-use-edge-node.md) et activer [l’authentification Kerberos SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Suivre les étapes de [l’interface utilisateur web Oozie](../hdinsight-use-oozie-linux-mac.md) pour activer le tunneling SSH vers le nœud de périphérie et accéder à l’interface utilisateur web

## <a name="next-steps"></a>Étapes suivantes
* [Utiliser Oozie avec Hadoop pour définir et exécuter un workflow Azure HDInsight basé sur Linux](../hdinsight-use-oozie-linux-mac.md)
* [Utiliser un coordinateur Oozie basé sur le temps](../hdinsight-use-oozie-coordinator-time.md)
* [Exécuter des requêtes Hive en utilisant SSH sur des clusters HDInsight joints à un domaine](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)
