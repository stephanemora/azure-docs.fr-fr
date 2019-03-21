---
title: Python UDF avec Apache Hive et Apache Pig - Azure HDInsight
description: Découvrez comment utiliser des fonctions définies par l’utilisateur Python à partir de Apache Hive et Apache Pig dans HDInsight, la pile de technologies Apache Hadoop sur Azure.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: f6a9d688169f0f8fdd6f0be7b664dbe9ebd71941
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295230"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Utiliser des fonctions définies par l’utilisateur (UDF) Python avec Apache Hive et Apache Pig dans HDInsight

Découvrez comment utiliser des fonctions définies par l’utilisateur Python avec Apache Hive et Apache Pig dans Apache Hadoop sur Azure HDInsight.

## <a name="python"></a>Python sur HDInsight

Python 2.7 est installé par défaut sur HDInsight 3.0 et versions ultérieures. Apache Hive peut être utilisé avec cette version de Python pour traiter les flux de données. Le traitement de flux de données utilise STDOUT et STDIN pour transmettre des données entre Hive et les fonctions définies par l’utilisateur.

HDInsight inclut également Jython, une implémentation de Python écrite en Java. Jython s’exécute directement sur la Machine virtuelle Java et n’utilise pas le streaming. Jython est l’interpréteur Python recommandé lorsque vous utilisez Python avec Pig.

## <a name="prerequisites"></a>Conditions préalables

* **Un cluster Hadoop sur HDInsight**. Consultez [prise en main HDInsight sous Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Un client SSH**. Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* Le [schéma d’URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pour votre stockage principal de clusters. Il s’agit de wasb : / / pour le stockage Azure, abfs : / / pour Azure Data Lake Storage Gen2 ou adl : / / pour Azure Data Lake Storage Gen1. Si un transfert sécurisé est activé pour le stockage Azure ou Data Lake Storage Gen2, l’URI serait wasbs : / / ou abfss : / /, respectivement, voir aussi [transfert sécurisé](../../storage/common/storage-require-secure-transfer.md).
* **Changement possible pour la configuration du stockage.**  Consultez [configuration du stockage](#storage-configuration) si vous utilisez le type de compte de stockage `BlobStorage`.
* facultatif.  Si vous envisagez d’utiliser PowerShell, vous devez le [module de AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) installé.

> [!NOTE]  
> Le compte de stockage utilisé dans cet article a été le stockage Azure avec [transfert sécurisé](/../storage/common/storage-require-secure-transfer.md) activé et par conséquent `wasbs` est utilisée tout au long de l’article.

## <a name="storage-configuration"></a>Configuration du stockage
Aucune action n’est requise si le compte de stockage utilisé est du genre `Storage (general purpose v1)` ou `StorageV2 (general purpose v2)`.  Le processus dans cet article produira la sortie vers au moins `/tezstaging`.  Une configuration de hadoop par défaut contiendra `/tezstaging` dans le `fs.azure.page.blob.dir` variable de configuration dans `core-site.xml` pour service `HDFS`.  Cette configuration entraîne la sortie vers le répertoire d’objets BLOB de pages, qui n’est pas pris en charge pour le type de compte de stockage `BlobStorage`.  Pour utiliser `BlobStorage` pour cet article, vous devez supprimer `/tezstaging` à partir de la `fs.azure.page.blob.dir` variable de configuration.  La configuration est accessible à partir de la [Ambari UI](../hdinsight-hadoop-manage-ambari.md).  Sinon, vous recevrez le message d’erreur : `Page blob is not supported for this account type.`

> [!WARNING]  
> Les étapes de ce document partent des hypothèses suivantes :  
>
> * Vous créez les scripts Python sur votre environnement de développement local.
> * Vous chargez les scripts sur HDInsight à l’aide du `scp` commande ou le script PowerShell fourni.
>
> Si vous souhaitez utiliser le [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) pour travailler avec HDInsight, vous devez :
>
> * Créez les scripts à l’intérieur de l’environnement de l’interpréteur de commandes cloud.
> * Utilisez `scp` pour charger les fichiers de l’interpréteur de commandes cloud vers HDInsight.
> * Utilisez `ssh` à partir de l’interpréteur de commandes pour vous connecter à HDInsight et exécuter les exemples.

## <a name="hivepython"></a>Fonction définie par l’utilisateur Apache Hive

Python peut être utilisé en tant que fonction définie par l'utilisateur à partir de Hive via l'instruction `TRANSFORM` HiveQL. Par exemple, le HiveQL suivant appelle le fichier `hiveudf.py` stocké dans le compte de stockage Azure par défaut du cluster.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Cet exemple effectue ce qui suit :

1. L’instruction `add file` au début du fichier ajoute le fichier `hiveudf.py` au cache distribué. Il devient alors accessible à tous les nœuds du cluster.
2. L’instruction `SELECT TRANSFORM ... USING` sélectionne les données de la table `hivesampletable`. Elle transmet également les valeurs clientid, devicemake et devicemodel au script `hiveudf.py`.
3. La clause `AS` décrit les champs renvoyés par `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-file"></a>Créer un fichier

Sur votre environnement de développement, créez un fichier texte nommé `hiveudf.py`. Utilisez le code suivant comme contenu du fichier :

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Le script effectue les opérations suivantes :

1. Lit une ligne de données à partir de STDIN.
2. Le caractère de saut de ligne de fin est supprimé à l’aide de `string.strip(line, "\n ")`.
3. Lors du traitement par flux, une seule ligne contient toutes les valeurs séparées par un caractère de tabulation. `string.split(line, "\t")` peut donc être utilisé pour fractionner l’entrée à chaque tabulation et retourner uniquement les champs.
4. Une fois le traitement terminé, la sortie doit être écrite dans STDOUT sur une seule ligne, chaque champ étant séparé par une tabulation. Par exemple : `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. La boucle `while` se répète jusqu’à ce que plus aucun élément `line` ne soit lu.

La sortie du script est une concaténation des valeurs d’entrée pour `devicemake` et `devicemodel`, ainsi qu’un code de hachage de la valeur concaténée.

### <a name="upload-file-shell"></a>Télécharger le fichier (interpréteur de commandes)
Dans les commandes ci-dessous, remplacez `sshuser` avec le nom d’utilisateur s’ils sont différents.  Remplacez `mycluster` avec le nom du cluster.  Vérifiez que votre répertoire de travail est l’endroit où se trouve le fichier.

1. Utilisez `scp` pour copier les fichiers sur votre cluster HDInsight. Modifiez et entrez la commande suivante :

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Utilisez SSH pour la connexion au cluster.  Modifiez et entrez la commande suivante :

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. À partir de la session SSH, ajoutez les fichiers python téléchargés précédemment sur le stockage pour le cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Utilisez la fonction UDF Hive (shell)

1. Pour vous connecter à Hive, utilisez la commande suivante à partir de votre session SSH ouverte :

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Cette commande démarre le client Beeline.

2. Entrez la requête suivante à l’invite de commandes `0: jdbc:hive2://headnodehost:10001/>` :

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Après avoir entré la dernière ligne, la tâche doit démarrer. Une fois le travail terminé, il renvoie une sortie comparable à l’exemple de sortie suivant :

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Pour quitter Beeline, entrez la commande suivante :

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Télécharger le fichier (PowerShell)

> [!IMPORTANT]  
> Ces scripts PowerShell ne fonctionnent pas si [transfert sécurisé](../../storage/common/storage-require-secure-transfer.md) est activé.  Utilisez les commandes d’environnement ou désactiver le transfert sécurisé.

PowerShell peut également être utilisé pour exécuter des requêtes Hive à distance. Vérifiez que votre répertoire de travail est where `hiveudf.py` se trouve.  Le script PowerShell suivant permet d’exécuter une requête Hive qui utilise le `hiveudf.py` script :

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Pour plus d’informations sur le chargement des fichiers, consultez le document [Chargement de données pour les tâches Apache Hadoop dans HDInsight](../hdinsight-upload-data.md).


#### <a name="use-hive-udf"></a>Use Hive UDF


```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

La sortie du travail **Hive** doit ressembler à l’exemple de sortie suivant :

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9


## <a name="pigpython"></a>Fonction définie par l’utilisateur Apache Pig

Un script Python peut être utilisé comme une fonction définie par l'utilisateur à partir de Pig via l'instruction `GENERATE`. Vous pouvez exécuter le script à l’aide de Jython ou de C Python.

* Jython s’exécute sur la Machine virtuelle Java (JVM) et peut être appelé en mode natif à partir de Pig.
* C Python est un processus externe. Par conséquent, les données de Pig sur JVM sont envoyées au script s’exécutant dans un processus Python. La sortie du script Python est ensuite renvoyée dans Pig.

Pour spécifier l’interpréteur Python, utilisez `register` lorsque vous référencez le script Python. Les exemples suivants permettent d’enregistrer des scripts avec Pig en tant que `myfuncs` :

* **Pour utiliser Jython** : `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Pour utiliser C Python** : `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Lorsque vous utilisez Jython, le chemin d’accès au fichier pig_jython peut être un chemin d’accès local ou un WASBS : / / chemin d’accès. Toutefois, lorsque vous utilisez C Python, vous devez référencer un fichier sur le système de fichiers local du nœud que vous utilisez pour envoyer le travail Pig.

Une fois l’inscription effectuée, le langage Pig Latin pour cet exemple est le même pour les deux :

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Cet exemple effectue ce qui suit :

1. La première ligne charge l’exemple de fichier de données `sample.log` dans `LOGS`. Elle définit également chaque enregistrement sous la forme d’un tableau de caractères `chararray`.
2. La ligne suivante élimine par filtrage les valeurs Null et stocke le résultat de l'opération dans `LOG`.
3. La ligne suivante itère sur les enregistrements dans `LOG` et utilise `GENERATE` pour appeler la méthode `create_structure` contenue dans le script Python/Jython chargé en tant que `myfuncs`. `LINE` est utilisé pour transmettre l'enregistrement actuel à la fonction.
4. Enfin, les sorties sont vidées dans STDOUT avec la commande `DUMP`. Cette commande affiche les résultats une fois l’opération terminée.

### <a name="create-file"></a>Créer un fichier

Sur votre environnement de développement, créez un fichier texte nommé `pigudf.py`. Utilisez le code suivant comme contenu du fichier :

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

Dans l’exemple Pig Latin, nous avons défini l’entrée `LINE` comme un tableau de caractères car il n’existait pas de schéma cohérent pour l’entrée. Le script Python transforme les données en un schéma cohérent pour la sortie.

1. L’instruction `@outputSchema` définit le format des données qui sont renvoyées à Pig. Dans le cas présent, il s'agit d'un **data bag**, qui est un type de données Pig. Ce conteneur contient les champs suivants, qui sont tous des tableaux de caractères (chaînes) :

   * date : date de création de l'entrée du journal
   * time : heure de création de l'entrée du journal
   * classname : nom de la classe pour laquelle l'entrée a été créée
   * level : niveau du journal
   * detail : détails pour l'entrée du journal

2. Ensuite, `def create_structure(input)` définit la fonction à laquelle Pig transmet les lignes.

3. L'exemple de données, `sample.log`, respecte pour l'essentiel le schéma basé sur les champs date, time, classname, level et detail. Toutefois, il contient quelques lignes qui commencent par `*java.lang.Exception*`. Ces lignes doivent être modifiées pour correspondre au schéma. L'instruction `if` recherche ces dernières, puis manipule les données d'entrée pour déplacer la chaîne `*java.lang.Exception*` à la fin, harmonisant les données avec le schéma de sortie prévu.

4. La commande `split` est ensuite utilisée pour fractionner les données aux quatre premiers caractères d'espacement. La sortie est assignée dans `date`, `time`, `classname`, `level`, et `detail`.

5. Enfin, les valeurs sont renvoyées à Pig.

Lorsque les données sont renvoyées à Pig, elles utilisent un schéma cohérent comme défini dans l’instruction `@outputSchema`.



### <a name="upload-file-shell"></a>Télécharger le fichier (interpréteur de commandes)

Dans les commandes ci-dessous, remplacez `sshuser` avec le nom d’utilisateur s’ils sont différents.  Remplacez `mycluster` avec le nom du cluster.  Vérifiez que votre répertoire de travail est l’endroit où se trouve le fichier.

1. Utilisez `scp` pour copier les fichiers sur votre cluster HDInsight. Modifiez et entrez la commande suivante :

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Utilisez SSH pour la connexion au cluster.  Modifiez et entrez la commande suivante :

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. À partir de la session SSH, ajoutez les fichiers python téléchargés précédemment sur le stockage pour le cluster.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```


### <a name="use-pig-udf-shell"></a>Utiliser Pig UDF (interpréteur de commandes)

1. Pour vous connecter à pig, utilisez la commande suivante à partir de votre session SSH ouverte :

    ```bash
    pig
    ```

2. Entrez les instructions ci-après au niveau de l’invite `grunt>` :

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Après l’entrée de la ligne suivante, le travail doit démarrer. Une fois le travail terminé, il renvoie une sortie comparable aux données suivantes :

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Utilisez `quit` pour quitter l’interpréteur de commandes Grunt, puis utilisez les éléments suivants pour modifier le fichier pigudf.py sur le système de fichiers local :

    ```bash
    nano pigudf.py
    ```

5. Une fois dans l’éditeur, annulez les marques de commentaire dans la ligne suivante en supprimant le caractère `#` initial :

    ```bash
    #from pig_util import outputSchema
    ```

    Cette ligne modifie le script Python pour le faire fonctionner avec C Python plutôt qu’avec Jython. Une fois la modification effectuée, utilisez **Ctrl+X** pour quitter l’éditeur. Sélectionnez **Y**, puis appuyez sur **Entrée** pour enregistrer les modifications.

6. Utilisez la commande `pig` pour relancer l’interpréteur de commandes. Une fois dans l’invite de commandes `grunt>` , utilisez les commandes suivantes pour exécuter le script Python à l’aide de l’interpréteur C Python.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Une fois ce travail terminé, le résultat devrait être le même que lorsque vous avez exécuté le script à l’aide de Jython.


### <a name="upload-file-powershell"></a>Télécharger le fichier (PowerShell)

> [!IMPORTANT]  
> Ces scripts PowerShell ne fonctionnent pas si [transfert sécurisé](../../storage/common/storage-require-secure-transfer.md) est activé.  Utilisez les commandes d’environnement ou désactiver le transfert sécurisé.

PowerShell peut également être utilisé pour exécuter des requêtes Hive à distance. Vérifiez que votre répertoire de travail est where `pigudf.py` se trouve.  Le script PowerShell suivant permet d’exécuter une requête Hive qui utilise le `pigudf.py` script :

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Utilisez la commande Pig (PowerShell)

> [!NOTE]  
> Lors de l’envoi à distance d’un travail à l’aide de PowerShell, il n’est pas possible d’utiliser C Python en tant qu’interpréteur .

PowerShell peut également être utilisé pour exécuter des tâches Pig Latin. Pour exécuter une tâche Pig Latin qui utilise le `pigudf.py` script, utilisez le script PowerShell suivant :

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Le résultat du travail **Pig** doit être comparable aux données suivantes :

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="errors-when-running-jobs"></a>Erreurs lors de l'exécution des travaux

Lorsque vous exécutez le travail hive, vous pouvez rencontrer une erreur similaire au texte suivant :

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ce problème peut être causé par les fins de ligne dans le fichier Python. De nombreux éditeurs Windows utilisent par défaut CRLF comme fin de ligne, mais les applications Linux utilisent généralement LF.

Pour supprimer les caractères de retour chariot (CR) avant de charger le fichier dans HDInsight, vous pouvez utiliser les instructions PowerShell suivantes :

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Scripts PowerShell

Les deux exemples de script PowerShell utilisés pour l’exécution des exemples contiennent une ligne mise en commentaire qui affiche la sortie d’erreur pour le travail. Si la tâche ne donne pas le résultat prévu, supprimez les marques de commentaire sur la ligne suivante pour voir si les informations sur l'erreur signalent un problème.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Les informations sur l’erreur (STDERR) et le résultat du travail (STDOUT) sont également enregistrés dans votre stockage HDInsight.

| Pour ce travail... | Examinez ces fichiers dans le conteneur d'objets blob |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Étapes suivantes

Si vous devez charger des modules Python qui ne sont pas fournis par défaut, consultez l’article [How to deploy a Python module to Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) (Déploiement d’un module Python vers Azure HDInsight).

Pour connaître d’autres façons d’utiliser Pig et Hive et pour en savoir plus sur l’utilisation de MapReduce, consultez les documents suivants :

* [Utilisation d’Apache Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation d’Apache Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
