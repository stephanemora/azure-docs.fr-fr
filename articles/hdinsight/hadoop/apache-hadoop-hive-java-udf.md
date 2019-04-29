---
title: Fonction définie par l’utilisateur (UDF) de Java avec Apache Hive dans HDInsight - Azure
description: Découvrez comment créer une fonction définie par l’utilisateur basée sur Java qui fonctionne avec Apache Hive. Cet exemple UDF convertit un tableau de chaînes de texte en minuscules.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 03/21/2019
ms.date: 04/29/2019
ms.author: v-yiso
ms.openlocfilehash: 24c2e8b9600b3d622d3d6b42b3bc3615a87ff853
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122028"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Utiliser une fonction UDF Java avec Apache Hive dans HDInsight

Découvrez comment créer une fonction définie par l’utilisateur basée sur Java qui fonctionne avec Apache Hive. L’UDF Java dans cet exemple convertit un tableau de chaînes de texte en caractères tous minuscules.

## <a name="prerequisites"></a>Conditions préalables

* Un cluster Hadoop sur HDInsight. Consultez [prise en main HDInsight sous Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Kit de développeur Java (JDK) version 8](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) correctement [installé](https://maven.apache.org/install.html) en fonction d’Apache.  Maven est un système de génération de projet pour les projets Java.
* Le [schéma d’URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pour votre stockage principal de clusters. Il s’agit de wasb : / / pour le stockage Azure, abfs : / / pour Azure Data Lake Storage Gen2 ou adl : / / pour Azure Data Lake Storage Gen1. Si un transfert sécurisé est activé pour le stockage Azure ou Data Lake Storage Gen2, l’URI serait wasbs : / / ou abfss : / /, respectivement, voir aussi [transfert sécurisé](../../storage/common/storage-require-secure-transfer.md).

* Un éditeur de texte ou un IDE Java

    > [!IMPORTANT]  
    > Si vous créez les fichiers Python sur un client Windows, vous avez besoin d’un éditeur qui utilise LF comme fin de ligne. Si vous ne savez pas si votre éditeur utilise LF ou CRLF, consultez la section [Dépannage](#troubleshooting) pour savoir comment supprimer le caractère CR.

## <a name="test-environment"></a>Environnement de test
L’environnement utilisé pour cet article était un ordinateur exécutant Windows 10.  Les commandes ont été exécutées dans une invite de commandes, et les différents fichiers ont été modifiés avec le bloc-notes. Modifier en conséquence pour votre environnement.

À partir d’une invite de commandes, entrez les commandes ci-dessous pour créer un environnement de travail :

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Créer un exemple Java UDF

1. Créez un nouveau projet Maven en entrant la commande suivante :

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Cette commande crée un répertoire nommé `exampleudf`, qui contient le projet Maven.

2. Une fois le projet a été créé, supprimez le `exampleudf/src/test` répertoire qui a été créée dans le cadre du projet en entrant la commande suivante :

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Ouvrez `pom.xml` en entrant la commande ci-dessous :

    ```cmd
    notepad pom.xml
    ```

    Puis remplacez le `<dependencies>` entrée avec le code XML suivant :

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Ces entrées spécifient la version de Hadoop et de Hive fournie avec HDInsight 3.6. Vous trouverez des informations sur les versions de Hadoop et Hive incluses dans HDInsight dans le document [Contrôle des versions du composant HDInsight](../hdinsight-component-versioning.md) .

    Ajoutez une section `<build>` avant la ligne `</project>` à la fin du fichier. Cette section doit comporter le fichier XML suivant :

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    Ces entrées définissent la génération du projet. Plus précisément, la version de java utilisée par le projet et la méthode de génération d’un uberjar pour le déploiement sur le cluster.

    Enregistrez le fichier une fois les modifications apportées.

4. Entrez la commande ci-dessous pour créer et ouvrir un nouveau fichier `ExampleUDF.java`:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Puis copiez et collez le code java ci-dessous dans le nouveau fichier. Puis fermez le fichier.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Ce code permet d’implémenter une fonction définie par l’utilisateur qui accepte une valeur de chaîne et renvoie une version de la chaîne en minuscules.

## <a name="build-and-install-the-udf"></a>Générer et installer la fonction UDF

Dans les commandes ci-dessous, remplacez `sshuser` avec le nom d’utilisateur s’ils sont différents. Remplacez `mycluster` avec le nom du cluster.

1. Compiler et empaqueter la fonction UDF en entrant la commande suivante :

    ```cmd
    mvn compile package
    ```

    Cette commande génère et conditionne l’UDF dans le fichier `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar`.

2. Utilisez le `scp` commande pour copier le fichier vers le cluster HDInsight en entrant la commande suivante :

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight
    ```

3. Connectez-vous au cluster à l’aide de SSH en entrant la commande suivante :

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.cn
    ```

4. À partir de la session SSH ouverte, copiez le fichier jar vers le stockage HDInsight.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Utiliser la fonction UDF à partir de Hive

1. Démarrez le client Beeline à partir de la session SSH en entrant la commande suivante :

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Cette commande suppose que vous avez utilisé la valeur par défaut d’ **admin** pour le compte de connexion de votre cluster.

2. Une fois sur l’invite `jdbc:hive2://localhost:10001/>` , entrez la commande suivante pour ajouter la fonction UDF dans Hive et l’exposer en tant que fonction.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Utilisez la fonction UDF pour convertir les valeurs extraites d’une table en chaînes de caractères minuscules.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Cette requête sélectionne l’état de la table, convertit la chaîne en bas de cas et ensuite les afficher, ainsi que le nom non modifié. Le résultat ressemble au texte suivant :

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>Résolution de problèmes

Lorsque vous exécutez le travail hive, vous pouvez rencontrer une erreur similaire au texte suivant :

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ce problème peut être causé par les fins de ligne dans le fichier Python. De nombreux éditeurs Windows utilisent par défaut CRLF comme fin de ligne, mais les applications Linux utilisent généralement LF.

Pour supprimer les caractères de retour chariot (CR) avant de charger le fichier dans HDInsight, vous pouvez utiliser les instructions PowerShell suivantes :

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir d’autres utilisations de Hive, consultez [Utiliser Apache Hive avec HDInsight](hdinsight-use-hive.md).

Pour plus d’informations sur les fonctions définies par l’utilisateur Hive, consultez [Apache Hive Operators and User-Defined Functions ](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) (Opérateurs et fonctions définies par l’utilisateur Apache Hive) du wiki Hive sur le site apache.org.
