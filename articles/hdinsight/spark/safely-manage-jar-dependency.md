---
title: Gérer les dépendances jar en toute sécurité – Azure HDInsight
description: Cet article décrit les bonnes pratiques de gestion des dépendances JAR (Java Archive) pour les applications HDInsight.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: 9868e32665c89bbe9aadc06f1c2834704e6534e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942386"
---
# <a name="safely-manage-jar-dependencies"></a>Gérer les dépendances jar de façon sécurisée

Les composants installés sur les clusters HDInsight ont des dépendances avec des bibliothèques tierces. En règle générale, une version spécifique de modules communs tels que Guava est référencée par ces composants intégrés. Quand vous soumettez une application avec ses dépendances, cela peut provoquer un conflit entre différentes versions du même module. Si vous référencez la version du composant dans le classpath en premier, les composants intégrés peuvent lever des exceptions en raison d’une incompatibilité de version. Toutefois, si les composants intégrés injectent d’abord leurs dépendances dans le classpath, votre application peut lever des erreurs telles que `NoSuchMethod`.

Pour éviter les conflits de versions, pensez à l’ombrage de vos dépendances d’application.

## <a name="what-does-package-shading-mean"></a>Que signifie l’ombrage de package ?
L’ombrage offre un moyen d’inclure et de renommer des dépendances. Il déplace les classes et réécrit le bytecode et les ressources affectés pour créer une copie privée de vos dépendances.

## <a name="how-to-shade-a-package"></a>Comment ombrer un package ?

### <a name="use-uber-jar"></a>Utiliser uber-jar
Uber-jar est un fichier jar unique qui contient à la fois le fichier jar de l’application et ses dépendances. Les dépendances dans uber-jar sont par défaut non ombrées. Dans certains cas, cela peut entraîner un conflit de versions si d’autres composants ou applications référencent une version différente de ces bibliothèques. Pour éviter cela, vous pouvez créer un fichier uber-jar avec une partie (ou la totalité) des dépendances ombrées.

### <a name="shade-package-using-maven"></a>Ombrer un package à l’aide de Maven
Maven peut générer des applications écrites à la fois en Java et Scala. Maven-shade-plugin peut vous aider à créer facilement un uber-jar ombré.

L’exemple ci-dessous montre un fichier `pom.xml` qui a été mis à jour pour ombrer un package à l’aide de maven-shade-plugin.  La section XML `<relocation>…</relocation>` déplace les classes du package `com.google.guava` vers le package `com.google.shaded.guava` en déplaçant les entrées de fichier JAR correspondantes et en réécrivant le bytecode affecté.

Après avoir modifié `pom.xml`, vous pouvez exécuter `mvn package` pour générer l’uber-jar ombré.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Ombrer un package à l’aide de SBT
SBT est également un outil de génération pour Scala et Java. SBT n’a pas de plug-in d’ombrage comme maven-shade-plugin. Vous pouvez modifier le fichier `build.sbt` pour ombrer des packages. 

Par exemple, pour ombrer `com.google.guava`, vous pouvez ajouter la commande ci-dessous au fichier `build.sbt` :

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Vous pouvez ensuite exécuter `sbt clean` et `sbt assembly` pour générer le fichier jar ombré. 

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser les outils IntelliJ HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

* [Créer une application Scala Maven pour Spark dans IntelliJ](./apache-spark-create-standalone-application.md)