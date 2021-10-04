---
title: Fonctions d’expression dans le flux de données de mappage
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez les fonctions d’expression du flux de données de mappage.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: dc0bdf8f8d40b23f4f6e8338ab6b608278428149
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060268"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Expressions de transformation de données dans le flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Cet article fournit des détails sur les expressions et fonctions prises en charge par Azure Data Factory et Azure Synapse Analytics dans les flux de données de mappage.


## <a name="expression-functions"></a>Fonctions d’expression

Dans les pipelines Data Factory et Synapse, utilisez le langage d’expression de la fonctionnalité de flux de données de mappage pour configurer les transformations de données.

| Fonction d’expression | Tâche |
|-----|-----|
| [abs](data-flow-expression-functions.md#abs) | Valeur absolue d’un nombre.  |
| [acos](data-flow-expression-functions.md#acos) | Calcule une valeur inverse de cosinus.  |
| [add](data-flow-expression-functions.md#add) | Ajoute une paire de chaînes ou de nombres. Ajoute une date à un nombre de jours. Ajoute une durée à un timestamp. Ajoute un tableau à un autre de type similaire. Identique à l’opérateur +.  |
| [addDays](data-flow-expression-functions.md#addDays) | Ajoute des jours à une date ou à un horodatage. Identique à l’opérateur + pour la date.  |
| [addMonths](data-flow-expression-functions.md#addMonths) | Ajoute des mois à une date ou à un timestamp. Vous pouvez éventuellement transmettre un fuseau horaire.  |
| [and](data-flow-expression-functions.md#and) | Opérateur AND logique. Identique à &&.  |
| [asin](data-flow-expression-functions.md#asin) | Calcule une valeur de sinus inverse.  |
| [atan](data-flow-expression-functions.md#atan) | Calcule une valeur de tangente inverse.  |
| [atan2](data-flow-expression-functions.md#atan2) | Retourne l’angle (en radians) qui se trouve entre l’axe positif des abscisses d’un plan et le point donné par les coordonnées.  |
| [between](data-flow-expression-functions.md#between) | Vérifie si la première valeur est comprise entre deux autres valeurs incluses. Les valeurs numériques, de chaîne et DateHeure peuvent être comparées   |
| [bitwiseAnd](data-flow-expression-functions.md#bitwiseAnd) | Opérateur And au niveau du bit sur des types de données intégraux. Identique à l’opérateur &   |
| [bitwiseOr](data-flow-expression-functions.md#bitwiseOr) | Opérateur Or au niveau du bit sur des types de données intégraux. Identique à l’opérateur \|  |
| [bitwiseXor](data-flow-expression-functions.md#bitwiseXor) | Opérateur Or au niveau du bit sur des types de données intégraux. Identique à l’opérateur \|  |
| [blake2b](data-flow-expression-functions.md#blake2b) | Calcule la synthèse Blake2 de l’ensemble de colonnes comprenant différents types de données primitifs à partir d’une longueur en bits, qui peut uniquement être égale aux multiples de 8 compris entre 8 et 512. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne  |
| [blake2bBinary](data-flow-expression-functions.md#blake2bBinary) | Calcule la synthèse Blake2 de l’ensemble de colonnes comprenant différents types de données primitifs à partir d’une longueur en bits, qui peut uniquement être égale aux multiples de 8 compris entre 8 et 512. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne  |
| [case](data-flow-expression-functions.md#case) | Selon des conditions alternantes, applique une valeur ou une autre. Si les entrées sont en nombre égal, l’autre valeur est Null par défaut pour la dernière condition.  |
| [cbrt](data-flow-expression-functions.md#cbrt) | Calcule la racine cubique d’un nombre.  |
| [ceil](data-flow-expression-functions.md#ceil) | Retourne le plus petit entier qui n’est pas inférieur au nombre.  |
| [coalesce](data-flow-expression-functions.md#coalesce) | Renvoie la première valeur non nulle d'un jeu d'entrées. Toutes les entrées doivent être du même type.  |
| [columnNames](data-flow-expression-functions.md#columnNames) | Obtient les noms de toutes les colonnes de sortie pour un flux. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument.  |
| [columns](data-flow-expression-functions.md#columns) | Obtient les valeurs de toutes les colonnes de sortie pour un flux. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument.   |
| [compare](data-flow-expression-functions.md#compare) | Compare deux valeurs du même type. Retourne un entier négatif si valeur1 < valeur2, 0 si valeur1 == valeur2, une valeur positive si valeur1 > valeur2.  |
| [concat](data-flow-expression-functions.md#concat) | Concatène un nombre variable de chaînes. Identique à l’opérateur + avec des chaînes.  |
| [concatWS](data-flow-expression-functions.md#concatWS) | Concatène un nombre variable de chaînes avec un séparateur. Le premier paramètre est le séparateur.  |
| [cos](data-flow-expression-functions.md#cos) | Calcule une valeur de cosinus.  |
| [cosh](data-flow-expression-functions.md#cosh) | Calcule le cosinus hyperbolique d’une valeur.  |
| [crc32](data-flow-expression-functions.md#crc32) | Calcule le code de hachage CRC32 de l’ensemble de colonnes comprenant différents types de données primitives à partir d’une longueur en bits, qui peut uniquement être égale aux valeurs 0(256), 224, 256, 384, 512. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne.  |
| [currentDate](data-flow-expression-functions.md#currentDate) | Obtient la date à laquelle l’exécution du travail commence. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). |
| [currentTimestamp](data-flow-expression-functions.md#currentTimestamp) | Obtient l’horodatage du début de l’exécution du travail avec le fuseau horaire local.  |
| [currentUTC](data-flow-expression-functions.md#currentUTC) | Obtient l’horodatage actuel au format UTC. Si vous souhaitez que votre heure actuelle soit interprétée dans un fuseau horaire différent de celui de votre cluster, vous pouvez passer un fuseau horaire facultatif au format « GMT », « PST », « UTC », « Amérique/Caïmans ». La valeur par défaut est le fuseau horaire actuel. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). Pour convertir l’heure UTC en un fuseau horaire différent, utilisez `fromUTC()`.  |
| [dayOfMonth](data-flow-expression-functions.md#dayOfMonth) | Obtient le jour du mois dans une date.  |
| [dayOfWeek](data-flow-expression-functions.md#dayOfWeek) | Obtient le jour de la semaine dans une date. 1 - dimanche, 2 - lundi..., 7 - samedi.  |
| [dayOfYear](data-flow-expression-functions.md#dayOfYear) | Obtient le jour de l’année dans une date.  |
| [days](data-flow-expression-functions.md#days) | Durée en millisecondes du nombre de jours.  |
| [degrees](data-flow-expression-functions.md#degrees) | Convertit les radians en degrés.  |
| [divide](data-flow-expression-functions.md#divide) | Divise une paire de nombres. Identique à l’opérateur `/`.  |
| [dropLeft](data-flow-expression-functions.md#dropLeft) | Supprime le nombre de caractères nécessaire à partir de la gauche de la chaîne. Si le nombre à supprimer est supérieur à la longueur de la chaîne, une chaîne vide est retournée.|
| [dropRight](data-flow-expression-functions.md#dropRight) | Supprime le nombre de caractères nécessaire à partir de la droite de la chaîne. Si le nombre à supprimer est supérieur à la longueur de la chaîne, une chaîne vide est retournée.|
| [endsWith](data-flow-expression-functions.md#endsWith) | Vérifie si la chaîne se termine par la chaîne fournie.  |
| [equals](data-flow-expression-functions.md#equals) | Opérateur de comparaison « Est égal à » Identique à l’opérateur ==.  |
| [equalsIgnoreCase](data-flow-expression-functions.md#equalsIgnoreCase) | Opérateur de comparaison « Est égal à » qui ignore la casse. Identique à l’opérateur <=>.  |
| [escape](data-flow-expression-functions.md#escape) | Échappe une chaîne conformément à un format. Les valeurs littérales pour le format acceptable sont « json », « xml », « ecmascript », « html », « java ».|
| [expr](data-flow-expression-functions.md#expr) | Génère une expression à partir d’une chaîne. Cela revient à écrire cette expression sous une forme non littérale. Ceci peut être utilisé pour passer des paramètres sous forme de représentations de chaîne.|
| [factorial](data-flow-expression-functions.md#factorial) | Calcule la factorielle d’un nombre.  |
| [false](data-flow-expression-functions.md#false) | Retourne toujours une valeur false. Utilisez la fonction `syntax(false())` s’il existe une colonne nommée 'false'.  |
| [floor](data-flow-expression-functions.md#floor) | Retourne le plus grand entier qui n’est pas supérieur au nombre.  |
| [fromBase64](data-flow-expression-functions.md#fromBase64) | Décode la chaîne encodée en base64.|
| [fromUTC](data-flow-expression-functions.md#fromUTC) | Convertit vers l’horodatage à partir du temps universel coordonné. Vous pouvez, si vous le souhaitez, passer un fuseau horaire au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. La valeur par défaut est le fuseau horaire actuel. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [greater](data-flow-expression-functions.md#greater) | Opérateur de comparaison « Supérieur à » Identique à l’opérateur >.  |
| [greaterOrEqual](data-flow-expression-functions.md#greaterOrEqual) | Opérateur de comparaison « Supérieur ou égal à ». Identique à l’opérateur >=.  |
| [greatest](data-flow-expression-functions.md#greatest) | Retourne la valeur la plus élevée parmi celles de la liste des valeurs d’entrée, en ignorant les valeurs Null. Retourne la valeur Null si toutes les entrées ont une valeur Null.  |
| [hasColumn](data-flow-expression-functions.md#hasColumn) | Vérifie une valeur de colonne par son nom dans le flux de données. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument. Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.  |
| [hour](data-flow-expression-functions.md#hour) | Obtient la valeur d’heure d’un horodatage. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [hours](data-flow-expression-functions.md#hours) | Durée en millisecondes du nombre d’heures.  |
| [iif](data-flow-expression-functions.md#iif) | Selon la condition, applique une valeur ou une autre. Si l’autre valeur n’est pas spécifiée, elle est considérée comme Null. Les deux valeurs doivent être compatibles (numérique, chaîne...).   |
| [iifNull](data-flow-expression-functions.md#iifNull) | Vérifie si le premier paramètre a la valeur Null. Si la valeur n’est pas Null, le premier paramètre est retourné. Si la valeur est Null, le deuxième paramètre est retourné. Si trois paramètres sont spécifiés, le comportement est le même que iff(isNull(valeur1), valeur2, valeur3), et le troisième paramètre est retourné si la première valeur n’a pas la valeur Null.  |
| [initCap](data-flow-expression-functions.md#initCap) | Convertit la première lettre de chaque mot en majuscules. Les mots sont identifiés comme étant séparés par des espaces blancs.  |
| [instr](data-flow-expression-functions.md#instr) | Recherche la position (de base 1) de la sous-chaîne au sein d’une chaîne. Si elle est introuvable, 0 est retourné.  |
| [isDelete](data-flow-expression-functions.md#isDelete) | Vérifie si la ligne est marquée pour la suppression. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  |
| [isError](data-flow-expression-functions.md#isError) | Vérifie si la ligne est marquée comme comprenant une erreur. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  |
| [isIgnore](data-flow-expression-functions.md#isIgnore) | Vérifie si la ligne est marquée comme devant être ignorée. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  |
| [isInsert](data-flow-expression-functions.md#isInsert) | Vérifie si la ligne est marquée pour l’insertion. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  |
| [isMatch](data-flow-expression-functions.md#isMatch) | Vérifie si la ligne correspond à la recherche. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  |
| [isNull](data-flow-expression-functions.md#isNull) | Vérifie si la valeur est NULL.  |
| [isUpdate](data-flow-expression-functions.md#isUpdate) | Vérifie si la ligne est marquée pour la mise à jour. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  |
| [IsUpsert](data-flow-expression-functions.md#isUpsert) | Vérifie si la ligne est marquée pour l’insertion. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  |
| [jaroWinkler](data-flow-expression-functions.md#jaroWinkler) | Obtient la distance JaroWinkler entre deux chaînes. |
| [lastDayOfMonth](data-flow-expression-functions.md#lastDayOfMonth) | Obtient le dernier jour du mois dans une date.  |
| [least](data-flow-expression-functions.md#least) | Opérateur de comparaison « Inférieur ou égal à ». Identique à l’opérateur <=.  |
| [left](data-flow-expression-functions.md#left) | Extrait une sous-chaîne comportant un certain nombre de caractères en partant de la gauche d’une chaîne au niveau de l’index 1. Identique à SUBSTRING(str, 1, n).  |
| [length](data-flow-expression-functions.md#length) | Retourne la longueur de la chaîne.  |
| [lesser](data-flow-expression-functions.md#lesser) | Opérateur de comparaison « Inférieur à » Identique à l’opérateur <.  |
| [lesserOrEqual](data-flow-expression-functions.md#lesserOrEqual) | Opérateur de comparaison « Inférieur ou égal à ». Identique à l’opérateur <=.  |
| [levenshtein](data-flow-expression-functions.md#levenshtein) | Obtient la distance levenshtein entre deux chaînes.  |
| [like](data-flow-expression-functions.md#like) | Le modèle est une chaîne qui est mise en correspondance littéralement. Les symboles spéciaux suivants représentent des exceptions : _ correspond à tout caractère de l’entrée (semblable à . dans les expressions régulières ```posix```)|
| [locate](data-flow-expression-functions.md#locate) | Recherche la position (de base 1) de la sous-chaîne au sein d’une chaîne, en partant d’une certaine position. Si la position est omise, on considère qu’elle est située au début de la chaîne. Si elle est introuvable, 0 est retourné.  |
| [log](data-flow-expression-functions.md#log) | Calcule la valeur de journal. Une base facultative peut être fournie, voire un nombre Euler, le cas échéant.  |
| [log10](data-flow-expression-functions.md#log10) | Calcule la valeur de journal sur une base 10.  |
| [lower](data-flow-expression-functions.md#lower) | Convertit une chaîne en minuscules.  |
| [lpad](data-flow-expression-functions.md#lpad) | Décale la chaîne sur la gauche selon la marge intérieure fournie, jusqu’à ce qu’elle atteigne une certaine longueur. Si la longueur de la chaîne est supérieure ou égale à cette longueur, elle est tronquée à la longueur.  |
| [ltrim](data-flow-expression-functions.md#ltrim) | Supprime une chaîne de caractères de début situés sur la gauche. Si le deuxième paramètre n’est pas spécifié, cela supprime l’espace blanc. Sinon, cela supprime tout caractère spécifié dans le deuxième paramètre.  |
| [md5](data-flow-expression-functions.md#md5) | Calcule le code de hachage MD5 de l’ensemble de colonnes comprenant différents types de données primitives, et retourne une chaîne hexadécimale de 32 caractères. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne.  |
| [millisecond](data-flow-expression-functions.md#millisecond) | Obtient la valeur en millisecondes d'une date. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [milliseconds](data-flow-expression-functions.md#milliseconds) | Durée en millisecondes du nombre de millisecondes.  |
| [minus](data-flow-expression-functions.md#minus) | Soustrait des nombres. Soustrait le nombre de jours à une date. Soustrait une durée d’un timestamp. Soustrait deux timestamps pour obtenir la différence en millisecondes. Identique à l’opérateur -.  |
| [minute](data-flow-expression-functions.md#minute) | Obtient la valeur de minute d’un horodatage. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [minutes](data-flow-expression-functions.md#minutes) | Durée en millisecondes du nombre de minutes.  |
| [mod](data-flow-expression-functions.md#mod) | Module une paire de nombres. Identique à l’opérateur %.  |
| [month](data-flow-expression-functions.md#month) | Obtient la valeur de mois d’une date ou d’un horodatage.  |
| [monthsBetween](data-flow-expression-functions.md#monthsBetween) | Obtient le nombre de mois entre deux dates. Vous pouvez arrondir le résultat du calcul. Vous pouvez transmettre un fuseau horaire facultatif au format « GMT », « PST », « UTC », « America/Caïman ». Le fuseau horaire local est utilisé par défaut. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [multiply](data-flow-expression-functions.md#multiply) | Multiplie une paire de nombres. Identique à l’opérateur *.  |
| [negate](data-flow-expression-functions.md#negate) | Inverse un nombre. Transforme les nombres positifs en nombres négatifs, et vice versa.  |
| [nextSequence](data-flow-expression-functions.md#nextSequence) | Retourne la séquence unique suivante. Le nombre est consécutif uniquement au sein d’une partition et il est préfixé de partitionId.  |
| [normalize](data-flow-expression-functions.md#normalize) | Normalise la valeur de chaîne pour séparer les caractères Unicode accentués.  |
| [not](data-flow-expression-functions.md#not) | Opérateur de négation logique.  |
| [notEquals](data-flow-expression-functions.md#notEquals) | Opérateur de comparaison « N'est pas égal à » Identique à l’opérateur !=.  |
| [notNull](data-flow-expression-functions.md#notNull) | Vérifie si la valeur n’est pas Null.  |
| [null](data-flow-expression-functions.md#null) | Retourne une valeur NULL. Utilisez la fonction `syntax(null())` s’il existe une colonne nommée 'null'. Toute opération qui l’utilise entraînera une valeur NULL.  |
| [or](data-flow-expression-functions.md#or) | Opérateur OR logique. Identique à \|\|.  |
| [pMod](data-flow-expression-functions.md#pMod) | Module positivement une paire de nombres.  |
| [partitionId](data-flow-expression-functions.md#partitionId) | Retourne l’ID de partition actuel où se trouve la ligne d’entrée.  |
| [puissance](data-flow-expression-functions.md#power) | Élève un nombre à la puissance d’un autre nombre.  |
| [radians](data-flow-expression-functions.md#radians) | Convertit les degrés en radians.|
| [random](data-flow-expression-functions.md#random) | Retourne un nombre aléatoire selon une valeur initiale facultative dans une partition. La valeur initiale doit être une valeur fixe et elle est utilisée conjointement avec la valeur partitionId pour produire des valeurs aléatoires   |
| [regexExtract](data-flow-expression-functions.md#regexExtract) | Extrait une sous-chaîne correspondant à un modèle d’expression régulière donné. Le dernier paramètre identifie le groupe de correspondance. S’il est omis, la valeur par défaut est de 1. Utilisez `<regex>` (apostrophe ouvrante) pour rechercher une chaîne sans séquence d’échappement.  |
| [regexMatch](data-flow-expression-functions.md#regexMatch) | Vérifie si la chaîne correspond au modèle d’expression régulière donné. Utilisez `<regex>` (apostrophe ouvrante) pour rechercher une chaîne sans séquence d’échappement.  |
| [regexReplace](data-flow-expression-functions.md#regexReplace) | Remplace toutes les occurrences d’un modèle d’expression régulière par une autre sous-chaîne d’une chaîne donnée. Utilisez `<regex>`(apostrophe ouvrante) pour rechercher une chaîne sans séquence d’échappement.  |
| [regexSplit](data-flow-expression-functions.md#regexSplit) | Fractionne une chaîne selon un délimiteur basé sur une expression régulière et retourne un tableau de chaînes.  |
| [replace](data-flow-expression-functions.md#replace) | Remplace toutes les occurrences d’une sous-chaîne par une autre sous-chaîne dans une chaîne donnée. Si le dernier paramètre est omis, sa valeur par défaut est une chaîne vide.  |
| [reverse](data-flow-expression-functions.md#reverse) | Inverse une chaîne.  |
| [right](data-flow-expression-functions.md#right) | Extrait une sous-chaîne comportant un certain nombre de caractères en partant de la droite. Identique à SUBSTRING(str, LENGTH(str) - n, n).  |
| [rlike](data-flow-expression-functions.md#rlike) | Vérifie si la chaîne correspond au modèle d’expression régulière donné.  |
| [round](data-flow-expression-functions.md#round) | Arrondit un nombre donné selon une mise à l’échelle et un mode d’arrondi facultatifs. Si la mise à l’échelle est omise, la valeur par défaut 0 est utilisée. Si le mode est omis, la valeur par défaut ROUND_HALF_UP(5) est utilisée. Les valeurs de l’arrondi incluent|
| [rpad](data-flow-expression-functions.md#rpad) | Décale la chaîne sur la droite selon la marge intérieure fournie, jusqu’à ce qu’elle atteigne une certaine longueur. Si la longueur de la chaîne est supérieure ou égale à cette longueur, elle est tronquée à la longueur.  |
| [rtrim](data-flow-expression-functions.md#rtrim) | Supprime une chaîne de caractères de début situés sur la droite. Si le deuxième paramètre n’est pas spécifié, cela supprime l’espace blanc. Sinon, cela supprime tout caractère spécifié dans le deuxième paramètre.  |
| [second](data-flow-expression-functions.md#second) | Obtient la deuxième valeur d’une date. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [secondes](data-flow-expression-functions.md#seconds) | Durée en millisecondes du nombre de secondes.  |
| [sha1](data-flow-expression-functions.md#sha1) | Calcule le code de hachage SHA-1 de l’ensemble de colonnes comprenant différents types de données primitives, et retourne une chaîne hexadécimale de 40 caractères. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne.  |
| [sha2](data-flow-expression-functions.md#sha2) | Calcule le code de hachage SHA-2 de l’ensemble de colonnes comprenant différents types de données primitives à partir d’une longueur en bits, qui peut uniquement être égale aux valeurs 0(256), 224, 256, 384, 512. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne.  |
| [sin](data-flow-expression-functions.md#sin) | Calcule une valeur de sinus.  |
| [sinh](data-flow-expression-functions.md#sinh) | Calcule une valeur de sinus hyperbolique.  |
| [soundex](data-flow-expression-functions.md#soundex) | Obtient le code ```soundex``` de la chaîne.  |
| [split](data-flow-expression-functions.md#split) | Fractionne une chaîne selon un délimiteur et retourne un tableau de chaînes.  |
| [sqrt](data-flow-expression-functions.md#sqrt) | Calcule la racine carrée d’un nombre.  |
| [startsWith](data-flow-expression-functions.md#startsWith) | Vérifie si la chaîne commence par la chaîne fournie.  |
| [subDays](data-flow-expression-functions.md#subDays) | Soustrait des jours d’une date ou d’un horodatage. Identique à l’opérateur - pour la date.  |
| [subMonths](data-flow-expression-functions.md#subMonths) | Soustrait des mois d’une date ou d’un timestamp.  |
| [substring](data-flow-expression-functions.md#substring) | Extrait une sous-chaîne d’une certaine longueur en partant d’une position. La position est de base 1. Si la longueur est omise, elle est définie par défaut sur la fin de la chaîne.  |
| [tan](data-flow-expression-functions.md#tan) | Calcule une valeur de tangente.  |
| [tanh](data-flow-expression-functions.md#tanh) | Calcule une valeur de tangente hyperbolique.  |
| [translate](data-flow-expression-functions.md#translate) | Remplace un jeu de caractères par un autre jeu de caractères dans la chaîne. Chaque caractère est remplacé par un seul caractère.  |
| [trim](data-flow-expression-functions.md#trim) | Supprime une chaîne de caractères de début et de fin. Si le deuxième paramètre n’est pas spécifié, cela supprime l’espace blanc. Sinon, cela supprime tout caractère spécifié dans le deuxième paramètre.  |
| [true](data-flow-expression-functions.md#true) | Retourne toujours une valeur true. Utilisez la fonction `syntax(true())` s’il existe une colonne nommée 'true'.  |
| [typeMatch](data-flow-expression-functions.md#typeMatch) | Correspond au type de la colonne. Peut uniquement être utilisé dans les expressions de modèle. « number » peut être de type short, integer, long, double, float ou decimal ; « integral » peut être de type short, integer ou long ; « fractional » peut être de type double, float ou decimal et « datetime » peut être de type date ou timestamp.  |
| [unescape](data-flow-expression-functions.md#unescape) | Annule l’échappement d’une chaîne conformément à un format. Les valeurs littérales pour le format acceptable sont « json », « xml », « ecmascript », « html », « java ».|
| [upper](data-flow-expression-functions.md#upper) | Convertit une chaîne en majuscules.  |
| [uuid](data-flow-expression-functions.md#uuid) | Retourne l’UUID généré.  |
| [weekOfYear](data-flow-expression-functions.md#weekOfYear) | Obtient la semaine de l’année dans une date.  |
| [weeks](data-flow-expression-functions.md#weeks) | Durée en millisecondes du nombre de semaines.  |
| [xor](data-flow-expression-functions.md#xor) | Opérateur logique XOR. Identique à l’opérateur ^.  |
| [year](data-flow-expression-functions.md#year) | Obtient la valeur d’année d’une date.  |
|||

## <a name="aggregate-functions"></a>Fonctions d'agrégation
Les fonctions suivantes sont uniquement disponibles dans les transformations d’agrégation, de tableau croisé dynamique, de suppression de tableau croisé dynamique et de fenêtre.

| Fonction d'agrégation | Tâche |
|----|----|
| [approxDistinctCount](data-flow-expression-functions.md#approxDistinctCount) | Obtient le nombre agrégé approximatif de valeurs distinctes pour une colonne. Le deuxième paramètre facultatif sert à contrôler l’erreur d’estimation.|
| [avg](data-flow-expression-functions.md#avg) | Obtient la moyenne des valeurs d’une colonne.  |
| [avgIf](data-flow-expression-functions.md#avgIf) | Selon certains critères, obtient la moyenne des valeurs d’une colonne.  |
| [collect](data-flow-expression-functions.md#collect) | Collecte toutes les valeurs de l’expression dans le groupe agrégé dans un tableau. Les structures peuvent être collectées et transformées en d’autres structures au cours de ce processus. Le nombre d’éléments est égal au nombre de lignes dans ce groupe, et peut contenir des valeurs Null. Le nombre d’éléments collectés doit être petit.  |
| [count](data-flow-expression-functions.md#count) | Obtient le nombre agrégé de valeurs. Si la ou les colonnes facultatives sont spécifiées, les valeurs Null ne sont pas comptabilisées.  |
| [countDistinct](data-flow-expression-functions.md#countDistinct) | Obtient la somme agrégée de valeurs distinctes appartenant à un ensemble de colonnes.  |
| [countIf](data-flow-expression-functions.md#countIf) | Selon certains critères, obtient le nombre agrégé de valeurs. Si la colonne facultative est spécifiée, les valeurs Null ne sont pas comptabilisées.  |
| [covariancePopulation](data-flow-expression-functions.md#covariancePopulation) | Obtient la covariance de remplissage entre deux colonnes.  |
| [covariancePopulationIf](data-flow-expression-functions.md#covariancePopulationIf) | Selon certains critères, obtient la covariance de remplissage de deux colonnes.  |
| [covarianceSample](data-flow-expression-functions.md#covarianceSample) | Obtient l’exemple de covariance de deux colonnes.  |
| [covarianceSampleIf](data-flow-expression-functions.md#covarianceSampleIf) | Selon certains critères, obtient l’exemple de covariance de deux colonnes.  |
| [first](data-flow-expression-functions.md#first) | Obtient la première valeur d’un groupe de colonnes. Si le deuxième paramètre ignoreNulls est omis, il est supposé que sa valeur est false.  |
| [isDistinct](data-flow-expression-functions.md#isDistinct) | Détermine si une colonne ou un ensemble de colonnes est distinct. Elle ne compte pas Null comme une valeur distincte |
| [kurtosis](data-flow-expression-functions.md#kurtosis) | Obtient l’aplatissement d’une colonne.  |
| [kurtosisIf](data-flow-expression-functions.md#kurtosisIf) | Selon certains critères, obtient l’aplatissement d’une colonne.  |
| [last](data-flow-expression-functions.md#last) | Obtient la dernière valeur d’un groupe de colonnes. Si le deuxième paramètre ignoreNulls est omis, il est supposé que sa valeur est false.  |
| [max](data-flow-expression-functions.md#max) | Obtient la valeur maximale d’une colonne.  |
| [maxIf](data-flow-expression-functions.md#maxIf) | Selon certains critères, obtient la valeur maximale d’une colonne.  |
| [mean](data-flow-expression-functions.md#mean) | Obtient la moyenne des valeurs d’une colonne. Identique à AVG.  |
| [meanIf](data-flow-expression-functions.md#meanIf) | Selon certains critères, obtient la moyenne des valeurs d’une colonne. Identique à avgIf.  |
| [min](data-flow-expression-functions.md#min) | Obtient la valeur minimale d’une colonne.  |
| [minIf](data-flow-expression-functions.md#minIf) | Selon certains critères, obtient la valeur minimale d’une colonne.  |
| [skewness](data-flow-expression-functions.md#skewness) | Obtient l’asymétrie d’une colonne.  |
| [skewnessIf](data-flow-expression-functions.md#skewnessIf) | Selon certains critères, obtient l’asymétrie d’une colonne.  |
| [stddev](data-flow-expression-functions.md#stddev) | Obtient l’écart type d’une colonne.  |
| [stddevIf](data-flow-expression-functions.md#stddevIf) | Selon certains critères, obtient l’écart type d’une colonne.  |
| [stddevPopulation](data-flow-expression-functions.md#stddevPopulation) | Obtient l’écart type de remplissage d’une colonne.  |
| [stddevPopulationIf](data-flow-expression-functions.md#stddevPopulationIf) | Selon certains critères, obtient l’écart type de remplissage d’une colonne.  |
| [stddevSample](data-flow-expression-functions.md#stddevSample) | Obtient l’exemple d’écart type d’une colonne.  |
| [stddevSampleIf](data-flow-expression-functions.md#stddevSampleIf) | Selon certains critères, obtient l’exemple d’écart type d’une colonne.  |
| [sum](data-flow-expression-functions.md#sum) | Obtient la somme agrégée d’une colonne numérique.  |
| [sumDistinct](data-flow-expression-functions.md#sumDistinct) | Obtient la somme agrégée de valeurs distinctes appartenant à une colonne numérique.  |
| [sumDistinctIf](data-flow-expression-functions.md#sumDistinctIf) | Selon certains critères, obtient la somme agrégée d’une colonne numérique. La condition peut être basée sur n’importe quelle colonne.  |
| [sumIf](data-flow-expression-functions.md#sumIf) | Selon certains critères, obtient la somme agrégée d’une colonne numérique. La condition peut être basée sur n’importe quelle colonne.  |
| [variance](data-flow-expression-functions.md#variance) | Obtient la variance d’une colonne.  |
| [varianceIf](data-flow-expression-functions.md#varianceIf) | Selon certains critères, obtient la variance d’une colonne.  |
| [variancePopulation](data-flow-expression-functions.md#variancePopulation) | Obtient la variance de remplissage d’une colonne.  |
| [variancePopulationIf](data-flow-expression-functions.md#variancePopulationIf) | Selon certains critères, obtient la variance de remplissage d’une colonne.  |
| [varianceSample](data-flow-expression-functions.md#varianceSample) | Obtient la variance non biaisée d’une colonne.  |
| [varianceSampleIf](data-flow-expression-functions.md#varianceSampleIf) | Selon certains critères, obtient la variance non biaisée d’une colonne.  |
|||

## <a name="array-functions"></a>Fonctions Tableau
Les fonctions Tableau effectuent des transformations sur les structures de données correspondant à des tableaux. Celles-ci incluent des mots clés spéciaux pour adresser des éléments de tableau et des index :

* ```#acc``` représente une valeur que vous souhaitez inclure dans votre sortie unique lors de la réduction d’un tableau
* ```#index``` représente l’index de tableau actuel, ainsi que les numéros d’index de tableau ```#index2, #index3 ...```
* ```#item``` représente la valeur de l’élément actuel dans le tableau

| Fonction de tableau | Tâche |
|----|----|
| [array](data-flow-expression-functions.md#array) | Crée un tableau d’éléments. Tous les éléments doivent être du même type. Si aucun élément n’est spécifié, par défaut, le tableau contient des chaînes vides. Identique à un opérateur de création [].  |
| [at](data-flow-expression-functions.md#at) | Recherche l’élément au niveau d’un index de tableau. L'index est de base 1. L’index hors limites génère une valeur Null. Recherche une valeur dans une carte d’après une clé. Si la clé est introuvable, retourne Null.|
| [contains](data-flow-expression-functions.md#contains) | Retourne true si un élément quelconque du tableau fourni correspond à true dans le prédicat fourni. Contains attend une référence à un élément dans la fonction de prédicat comme #item.  |
| [distinct](data-flow-expression-functions.md#distinct) | Retourne un ensemble distinct d’éléments d’un tableau.|
| [except](data-flow-expression-functions.md#except) | Retourne un jeu de différences d’un tableau à partir d’un autre en supprimant les doublons.|
| [filter](data-flow-expression-functions.md#filter) | Filtre hors du tableau les éléments qui ne respectent pas le prédicat fourni. Filter attend une référence à un élément dans la fonction de prédicat comme #item.  |
| [find](data-flow-expression-functions.md#find) | Recherche le premier élément d’un tableau qui correspond à la condition. Elle prend une fonction de filtre dans laquelle vous pouvez traiter l’élément dans le tableau comme #item. Pour les mappages profondément imbriqués, vous pouvez faire référence aux mappages parents à l’aide de la notation #item_n(#item_1, #item_2...).  |
| [flatten](data-flow-expression-functions.md#flatten) | Aplatit le ou les tableaux en un seul tableau. Les tableaux d’éléments atomiques sont retournés sans modification. Le dernier argument est facultatif et sa valeur par défaut est false pour aplatir de manière récursive plus d’un niveau de profondeur.|
| [in](data-flow-expression-functions.md#in) | Vérifie si un élément se trouve dans le tableau.  |
| [intersect](data-flow-expression-functions.md#intersect) | Retourne un ensemble d’intersection d’un élément distinct depuis 2 tableaux.|
| [map](data-flow-expression-functions.md#map) | Mappe chaque élément du tableau à un nouvel élément à l’aide de l’expression fournie. Map attend une référence à un élément dans la fonction d’expression comme #item.  |
| [mapIf](data-flow-expression-functions.md#mapIf) | Mappe conditionnellement un tableau à un autre tableau de même longueur ou plus petit. Les valeurs peuvent être de n’importe quel type de données, y compris structTypes. Elle prend une fonction de filtre dans laquelle vous pouvez traiter l’élément dans le tableau comme #item et l’index actuel comme #index. Pour les mappages profondément imbriqués, vous pouvez référencer les mappages parents à l’aide de la notation ``#item_[n](#item_1, #index_1...)``.|
| [mapIndex](data-flow-expression-functions.md#mapIndex) | Mappe chaque élément du tableau à un nouvel élément à l’aide de l’expression fournie. Map attend une référence à un élément dans la fonction d’expression sous la forme #item et une référence à l’index des éléments sous la forme #index.  |
| [mapLoop](data-flow-expression-functions.md#mapLoop) | Boucle de 1 à la longueur définie pour créer un tableau de cette longueur. Elle prend une fonction de mappage dans laquelle vous pouvez traiter l’index dans le tableau comme #index. Pour les mappages profondément imbriqués, vous pouvez référencer les mappages parents à l’aide de la notation #index_n(#index_1, #index_2...).|
| [reduce](data-flow-expression-functions.md#reduce) | Accumule des éléments dans un tableau. Reduce attend une référence à un accumulateur et un élément dans la première fonction d’expression comme #acc et #item et il attend que la valeur résultante comme #result soit utilisée dans la deuxième fonction d’expression.  |
| [size](data-flow-expression-functions.md#size) | Recherche la taille d’un tableau ou d’un type de mappage  |
| [slice](data-flow-expression-functions.md#slice) | Extrait un sous-ensemble d’un tableau en partant d’une position. La position est de base 1. Si la longueur est omise, elle est définie par défaut sur la fin de la chaîne.  |
| [sort](data-flow-expression-functions.md#sort) | Trie le tableau à l’aide de la fonction de prédicat fournie. Sort attend une référence à deux éléments consécutifs dans la fonction d’expression comme #item1 et #item2.  |
| [unfold](data-flow-expression-functions.md#unfold) | Déplie un tableau en un ensemble de lignes et répète les valeurs pour la colonne restante dans chaque ligne.|
| [union](data-flow-expression-functions.md#union) | Retourne un ensemble d’unions d’éléments distincts depuis 2 tableaux.|
|||

## <a name="cached-lookup-functions"></a>Fonctions de recherche mise en cache
Les fonctions suivantes sont disponibles uniquement lors de l’utilisation d’une recherche mise en cache quand vous avez inclus un récepteur mis en cache.

| Fonction de recherche mise en cache | Tâche |
|----|----|
| [lookup](data-flow-expression-functions.md#lookup) | Recherche la première ligne du récepteur mis en cache en utilisant les clés spécifiées qui correspondent aux clés du récepteur mis en cache.|
| [mlookup](data-flow-expression-functions.md#mlookup) | Recherche toutes les lignes correspondantes du récepteur mis en cache en utilisant les clés spécifiées qui correspondent aux clés du récepteur mis en cache.|
| [output](data-flow-expression-functions.md#output) | Retourne la première ligne des résultats du récepteur de cache |
| [outputs](data-flow-expression-functions.md#outputs) | Retourne la totalité des lignes de sortie des résultats du récepteur de cache |
|||

## <a name="conversion-functions"></a>Fonctions de conversion

Les fonctions de conversion permettent de convertir des données et de tester des types de données.

| Fonction de conversion | Tâche |
|----|----|
| [isBitSet](data-flow-expression-functions.md#isBitSet) | Vérifie si une position de bit est définie dans ce bitset |
| [setBitSet](data-flow-expression-functions.md#setBitSet) | Fixe la position des bits dans ce bitset |
| [isBoolean](data-flow-expression-functions.md#isBoolean) | Détermine si la valeur de chaîne est une valeur booléenne conformément aux règles de ``toBoolean()``|
| [isByte](data-flow-expression-functions.md#isByte) | Détermine si la valeur de chaîne est une valeur d'octet au format facultatif conformément aux règles de ``toByte()``|
| [isDate](data-flow-expression-functions.md#isDate) | Détermine si la chaîne de date d'entrée est une date en utilisant un format de date d'entrée facultatif. Reportez-vous au SimpleDateFormat de Java pour les formats disponibles. Si le format de date d’entrée est omis, le format par défaut est ``yyyy-[M]M-[d]d``. Les formats acceptés sont les suivants : ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]``|
| [isShort](data-flow-expression-functions.md#isShort) | Détermine si la valeur de chaîne est une valeur courte au format facultatif conformément aux règles de ``toShort()``|
| [isInteger](data-flow-expression-functions.md#isInteger) | Détermine si la valeur de chaîne est une valeur entière au format facultatif conformément aux règles de ``toInteger()``|
| [isLong](data-flow-expression-functions.md#isLong) | Détermine si la valeur de chaîne est une valeur longue au format facultatif conformément aux règles de ``toLong()``|
| [isNan](data-flow-expression-functions.md#isNan) | Vérifiez s’il ne s’agit pas d’un nombre.|
| [isFloat](data-flow-expression-functions.md#isFloat) | Détermine si la valeur de chaîne est une valeur flottante au format facultatif conformément aux règles de ``toFloat()``|
| [isDouble](data-flow-expression-functions.md#isDouble) | Détermine si la valeur de chaîne est une valeur double au format facultatif conformément aux règles de ``toDouble()``|
| [isDecimal](data-flow-expression-functions.md#isDecimal) | Détermine si la valeur de chaîne est une valeur décimale au format facultatif conformément aux règles de ``toDecimal()``|
| [isTimestamp](data-flow-expression-functions.md#isTimestamp) | Détermine si la chaîne de date d'entrée est un horodateur à l'aide d'un format d'horodatage d'entrée facultatif. Reportez-vous à la classe SimpleDateFormat de Java pour connaître les formats disponibles. Si l'horodatage est omis, le modèle par défaut ``yyyy-[M]M-[d]d hh:mm:ss[.f...]`` est utilisé. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. L'horodatage prend en charge une précision allant jusqu'à la milliseconde avec une valeur de 999. Reportez-vous à la classe SimpleDateFormat de Java pour connaître les formats disponibles.|
| [toBase64](data-flow-expression-functions.md#toBase64) | Encode la chaîne donnée en base64.  |
| [toBinary](data-flow-expression-functions.md#toBinary) | Convertit toutes les valeurs numériques/date/timestamp/chaîne en représentation binaire.  |
| [toBoolean](data-flow-expression-functions.md#toBoolean) | Convertit ('t', 'true', 'y', 'yes', '1') en valeur true, ('f', 'false', 'n', 'no', '0') en valeur false et toute autre valeur en valeur Null.  |
| [toByte](data-flow-expression-functions.md#toByte) | Convertit une valeur numérique ou une chaîne en une valeur de type octet. Un format décimal Java facultatif peut être utilisé pour la conversion.  |
| [toDate](data-flow-expression-functions.md#toDate) | Convertit la chaîne de date d’entrée en date à l’aide d’un format de date d’entrée facultatif. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. Si le format de date d’entrée est omis, le format par défaut est aaaa-[M]M-[j]j. Les formats acceptés sont les suivants :[ aaaa, aaaa-[M]M, aaaa-[M]M-[j]h, aaaa-[M]M-[j]jT* ].  |
| [toDecimal](data-flow-expression-functions.md#toDecimal) | Convertit une valeur numérique ou une chaîne en une valeur de type decimal. Si la précision et l’échelle ne sont pas spécifiées, la valeur utilisée par défaut est (10,2). Un format décimal Java facultatif peut être utilisé pour la conversion. Un format de paramètres régionaux facultatif sous forme de langue BCP47, par exemple en-US, de, zh-CN.  |
| [toDouble](data-flow-expression-functions.md#toDouble) | Convertit une valeur numérique ou une chaîne en une valeur de type double. Un format décimal Java facultatif peut être utilisé pour la conversion. Un format de paramètres régionaux facultatif sous forme de langue BCP47, par exemple en-US, de, zh-CN.  |
| [toFloat](data-flow-expression-functions.md#toFloat) | Convertit une valeur numérique ou une chaîne en une valeur de type float. Un format décimal Java facultatif peut être utilisé pour la conversion. Tronque toutes les valeurs de type double.  |
| [toInteger](data-flow-expression-functions.md#toInteger) | Convertit une valeur numérique ou une chaîne en une valeur entière. Un format décimal Java facultatif peut être utilisé pour la conversion. Tronque toutes les valeurs de type long, float ou double.  |
| [toLong](data-flow-expression-functions.md#toLong) | Convertit une valeur numérique ou une chaîne en une valeur de type long. Un format décimal Java facultatif peut être utilisé pour la conversion. Tronque toutes les valeurs de type float ou double.  |
| [toShort](data-flow-expression-functions.md#toShort) | Convertit une valeur numérique ou une chaîne en une valeur de type short. Un format décimal Java facultatif peut être utilisé pour la conversion. Tronque toutes les valeurs de type integer, long, float ou double.  |
| [toString](data-flow-expression-functions.md#toString) | Convertit un type de données primitif en une chaîne. Vous pouvez spécifier un format pour les nombres et les dates. Si aucun format n’est spécifié, la valeur système par défaut est utilisée. Le format décimal Java est utilisé pour les nombres. Pour connaître tous les formats de date possibles, reportez-vous à Java SimpleDateFormat. Le format par défaut est aaaa-MM-jj.  |
| [toTimestamp](data-flow-expression-functions.md#toTimestamp) | Convertit une chaîne en timestamp en fonction d’un format de timestamp facultatif. Si l’horodatage est omis, le modèle par défaut est utilisé : aaaa-[M]M-[j]j hh:mm:ss[.f...]. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. L’horodateur prend en charge une précision à la milliseconde avec la valeur 999. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [toUTC](data-flow-expression-functions.md#toUTC) | Convertit l’horodatage au format UTC. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. La valeur par défaut est le fuseau horaire actuel. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
|||

## <a name="map-functions"></a>Fonctions cartographiques

  Les fonctions cartographiques effectuent des opérations sur les types de données cartographiques

| Fonction cartographique | Tâche |
|----|----|
| [associate](data-flow-expression-functions.md#associate) | Crée un mappage de clés/valeurs. Toutes les clés et valeurs doivent être du même type. Si aucun élément n’est spécifié, la valeur par défaut est un mappage de type chaîne à chaîne. Similaire à l’opérateur de création ```[ -> ]```. Les clés et les valeurs doivent être spécifiées en alternance.|
| [KeyValues](data-flow-expression-functions.md#keyValues) | Crée un mappage de clés/valeurs. Le premier paramètre est un tableau de clés et le second est le tableau de valeurs. Les deux tableaux doivent avoir la même longueur.|
| [mapAssociation](data-flow-expression-functions.md#mapAssociation) | Transforme une carte en associant les clés aux nouvelles valeurs. Retourne un tableau. Prend une fonction de mappage dans laquelle vous pouvez traiter l’élément comme #key et la valeur actuelle comme #value. |
| [reassociate](data-flow-expression-functions.md#reassociate) | Transforme une carte en associant les clés aux nouvelles valeurs. Prend une fonction de mappage dans laquelle vous pouvez traiter l’élément comme #key et la valeur actuelle comme #value.  |
|||

## <a name="metafunctions"></a>Métafonctions

Les métafonctions fonctionnent principalement sur les métadonnées d’un flux de données.

| Métafonction  | Tâche |
|----|----|
| [byItem](data-flow-expression-functions.md#byItem) | Recherche un sous-élément dans une structure ou un tableau de structure. Si plusieurs correspondances sont trouvées, retourne la première. S’il n’existe aucune correspondance, une valeur Null est retournée. Le type de la valeur retournée doit être converti par l’une des actions de conversion de type (? date, ? chaîne, etc.).  Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre  |
| [byOrigin](data-flow-expression-functions.md#byOrigin) | Sélectionne une valeur de colonne selon le nom dans le flux de données d’origine. Le deuxième argument est le nom du flux d’origine. S’il existe plusieurs correspondances, la première correspondance est retournée. S’il n’existe aucune correspondance, une valeur Null est retournée. Le type de la valeur retournée doit être converti par l’une des fonctions de conversion de type (TO_DATE, TO_STRING, etc.). Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.  |
| [byOrigins](data-flow-expression-functions.md#byOrigins) | Sélectionne un tableau de colonnes par son nom dans le flux. Le deuxième argument est le flux à partir duquel il provient. S’il existe plusieurs correspondances, la première correspondance est retournée. S’il n’existe aucune correspondance, une valeur Null est retournée. Le type de la valeur retournée doit être converti par l’une des fonctions de conversion de type (TO_DATE, TO_STRING, etc.). Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.|
| [byName](data-flow-expression-functions.md#byName) | Sélectionne une valeur de colonne selon le nom dans le flux de données. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument. S’il existe plusieurs correspondances, la première correspondance est retournée. S’il n’existe aucune correspondance, une valeur Null est retournée. Le type de la valeur retournée doit être converti par l’une des fonctions de conversion de type (TO_DATE, TO_STRING, etc.).  Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.  |
| [byNames](data-flow-expression-functions.md#byNames) | Sélectionnez un tableau de colonnes par son nom dans le flux. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument. S’il existe plusieurs correspondances, la première correspondance est retournée. S’il n’existe aucune correspondance pour une colonne, la sortie entière est une valeur NULL. La valeur retournée requiert des fonctions de conversion de type (ToDate, toString, ...).  Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.|
| [byPath](data-flow-expression-functions.md#byPath) | Recherche un chemin d’accès hiérarchique par nom dans le flux. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument. Si aucun chemin d’accès n’est trouvé, la valeur Null est renvoyée. Les noms/chemins de colonnes connus au moment de la conception doivent être adressés uniquement par leur nom ou leur chemin en notation pointée. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.  |
| [byPosition](data-flow-expression-functions.md#byPosition) | Sélectionne une valeur de colonne selon sa position relative (base 1) dans le flux. Si la position est hors limites, une valeur Null est retournée. Le type de la valeur retournée doit être converti par l’une des fonctions de conversion de type (TO_DATE, TO_STRING, etc.). Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.  |
| [hasPath](data-flow-expression-functions.md#hasPath) | Vérifie s’il existe un certain chemin d’accès hiérarchique par nom dans le flux. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument. Les noms/chemins de colonnes connus au moment de la conception doivent être adressés uniquement par leur nom ou leur chemin en notation pointée. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.  |
| [originColumns](data-flow-expression-functions.md#originColumns) | Obtient toutes les colonnes de sortie pour un flux d’origine où des colonnes ont été créées. Se place au sein d’une autre fonction.|
| [hex](data-flow-expression-functions.md#hex) | Renvoie une représentation sous forme de chaîne hexadécimale d'une valeur binaire |
| [unhex](data-flow-expression-functions.md#unhex) | Annule la représentation sous forme de chaîne hexadécimale d'une valeur binaire. Peut être utilisé en conjonction avec sha2, md5 pour convertir une chaîne en représentation binaire |
|||

## <a name="window-functions"></a>Fonctions Windows

Les fonctions suivantes ne sont disponibles que dans des transformations de fenêtres.

| Fonction Windows | Tâche |
|----|----|
| [cumeDist](data-flow-expression-functions.md#cumeDist) | La fonction CumeDist calcule la position d’une valeur par rapport à toutes les valeurs de la partition. Le résultat correspond au nombre de lignes précédant ou correspondant à la ligne actuelle dans l’ordre de la partition, divisé par le nombre total de lignes de la partition de fenêtre. Toutes les valeurs égales auront la même position.  |
| [denseRank](data-flow-expression-functions.md#denseRank) | Calcule le rang d’une valeur dans un groupe de valeurs spécifiées dans la clause ORDER BY d’une fenêtre. Le résultat est égal à 1 plus le nombre de lignes précédant ou correspondant à la ligne actuelle dans l’ordre de la partition. Les valeurs ne vont pas produire d’écarts dans la séquence. Le rang Dense fonctionne même quand les données ne sont pas triées, et il recherche les valeurs qui ont été modifiées.  |
| [lag](data-flow-expression-functions.md#lag) | Obtient la valeur des n lignes évaluées dans le premier paramètre avant la ligne actuelle. Le deuxième paramètre correspond au nombre de lignes précédentes à examiner et la valeur par défaut est 1. S’il n’y a pas autant de lignes, la valeur Null est retournée, sauf si une valeur par défaut est spécifiée.  |
| [lead](data-flow-expression-functions.md#lead) | Obtient la valeur des n lignes évaluées dans le premier paramètre après la ligne actuelle. Le deuxième paramètre correspond au nombre de lignes suivantes à examiner et la valeur par défaut est 1. S’il n’y a pas autant de lignes, la valeur Null est retournée, sauf si une valeur par défaut est spécifiée.  |
| [nTile](data-flow-expression-functions.md#nTile) | La fonction ```NTile``` divise les lignes de chaque partition de fenêtre en `n` compartiments, compris entre 1 et `n` au maximum. Les valeurs de compartiment varient de 1 au maximum. Si les lignes de la partition ne sont pas réparties de façon uniforme dans les compartiments, les valeurs restantes sont distribuées une par une dans chaque compartiment, en commençant par le premier compartiment. La fonction ```NTile``` est utile pour le calcul des tertiles, quartiles, déciles et autres statistiques de synthèse courantes. La fonction calcule deux variables pendant l’initialisation : Aucune ligne supplémentaire ne sera ajoutée à la taille d’un compartiment habituel. Les deux variables sont basées sur la taille de la partition actuelle. Pendant le processus de calcul, la fonction effectue le suivi du nombre actuel de lignes, du nombre actuel de compartiments et du nombre de lignes où le compartiment va changer (bucketThreshold). Lorsque le nombre actuel de lignes atteint le seuil du compartiment, la valeur du compartiment est augmentée de 1 et le seuil est augmenté par la taille du compartiment (plus 1, si le compartiment actuel est rempli).  |
| [rank](data-flow-expression-functions.md#rank) | Calcule le rang d’une valeur dans un groupe de valeurs spécifiées dans la clause ORDER BY d’une fenêtre. Le résultat est égal à 1 plus le nombre de lignes précédant ou correspondant à la ligne actuelle dans l’ordre de la partition. Les valeurs vont produire des écarts dans la séquence. Le rang fonctionne même quand les données ne sont pas triées, et il recherche les valeurs qui ont été modifiées.  |
| [rowNumber](data-flow-expression-functions.md#rowNumber) | Attribue un numérotage séquentiel aux lignes d’une fenêtre en commençant par 1.  |
|||

## <a name="alphabetical-listing-of-all-functions"></a>Liste alphabétique de toutes les fonctions

Voici une liste alphabétique de toutes les fonctions disponibles dans le flux de données de mappage.

<a name="abs" ></a>

### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Valeur absolue d’un nombre.  
* ``abs(-20) -> 20``  
* ``abs(10) -> 10``  
___   


<a name="acos" ></a>

### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur inverse de cosinus.  
* ``acos(1) -> 0.0``  
___


<a name="add" ></a>

### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Ajoute une paire de chaînes ou de nombres. Ajoute une date à un nombre de jours. Ajoute une durée à un timestamp. Ajoute un tableau à un autre de type similaire. Identique à l’opérateur +.  
* ``add(10, 20) -> 30``  
* ``10 + 20 -> 30``  
* ``add('ice', 'cream') -> 'icecream'``  
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``  
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``  
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``  
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___


<a name="addDays" ></a>

### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Ajoute des jours à une date ou à un horodatage. Identique à l’opérateur + pour la date.  
* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``  
___


<a name="addMonths" ></a>

### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Ajoute des mois à une date ou à un timestamp. Vous pouvez éventuellement transmettre un fuseau horaire.  
* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``  
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``  
___


<a name="and" ></a>

### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Opérateur AND logique. Identique à &&.  
* ``and(true, false) -> false``  
* ``true && false -> false``  
___


<a name="approxDistinctCount" ></a>

### <code>approxDistinctCount</code>
<code><b>approxDistinctCount(<i>&lt;value1&gt;</i> : any, [ <i>&lt;value2&gt;</i> : double ]) => long</b></code><br/><br/>
Obtient le nombre agrégé approximatif de valeurs distinctes pour une colonne. Le deuxième paramètre facultatif sert à contrôler l’erreur d’estimation.
* ``approxDistinctCount(ProductID, .05) => long``  
___


<a name="array" ></a>

### <code>array</code>
<code><b>array([<i>&lt;value1&gt;</i> : any], ...) => array</b></code><br/><br/>
Crée un tableau d’éléments. Tous les éléments doivent être du même type. Si aucun élément n’est spécifié, par défaut, le tableau contient des chaînes vides. Identique à un opérateur de création [].  
* ``array('Seattle', 'Washington')``
* ``['Seattle', 'Washington']``
* ``['Seattle', 'Washington'][1]``
* ``'Washington'``
___


<a name="asin" ></a>

### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de sinus inverse.  
* ``asin(0) -> 0.0``  
___


<a name="associate" ></a>

### <code>associate</code>
<code><b>reassociate(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => map</b></code><br/><br/>
Crée un mappage de clés/valeurs. Toutes les clés et valeurs doivent être du même type. Si aucun élément n’est spécifié, la valeur par défaut est un mappage de type chaîne à chaîne. Similaire à l’opérateur de création ```[ -> ]```. Les clés et les valeurs doivent être spécifiées en alternance.
*   ``associate('fruit', 'apple', 'vegetable', 'carrot' )=> ['fruit' -> 'apple', 'vegetable' -> 'carrot']``
___


<a name="at" ></a>

### <code>at</code>
<code><b>at(<i>&lt;value1&gt;</i> : array/map, <i>&lt;value2&gt;</i> : integer/key type) => array</b></code><br/><br/>
Recherche l’élément au niveau d’un index de tableau. L'index est de base 1. L’index hors limites génère une valeur Null. Recherche une valeur dans une carte d’après une clé. Si la clé est introuvable, retourne Null.
*   ``at(['apples', 'pears'], 1) => 'apples'``
*   ``at(['fruit' -> 'apples', 'vegetable' -> 'carrot'], 'fruit') => 'apples'``
___


<a name="atan" ></a>

### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de tangente inverse.  
* ``atan(0) -> 0.0``  
___


<a name="atan2" ></a>

### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Retourne l’angle (en radians) qui se trouve entre l’axe positif des abscisses d’un plan et le point donné par les coordonnées.  
* ``atan2(0, 0) -> 0.0``  
___


<a name="avg" ></a>

### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtient la moyenne des valeurs d’une colonne.  
* ``avg(sales)``  
___


<a name="avgIf" ></a>

### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Selon certains critères, obtient la moyenne des valeurs d’une colonne.  
* ``avgIf(region == 'West', sales)``  
___


<a name="between" ></a>

### <code>between</code>
<code><b>between(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : any) => boolean</b></code><br/><br/>
Vérifie si la première valeur est comprise entre deux autres valeurs incluses. Les valeurs numériques, de chaîne et DateHeure peuvent être comparées * ``between(10, 5, 24)``
* ``true``
* ``between(currentDate(), currentDate() + 10, currentDate() + 20)``
* ``false``
___


<a name="bitwiseAnd" ></a>

### <code>bitwiseAnd</code>
<code><b>bitwiseAnd(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
Opérateur And au niveau du bit sur des types de données intégraux. Identique à l’opérateur & * ``bitwiseAnd(0xf4, 0xef)``
* ``0xe4``
* ``(0xf4 & 0xef)``
* ``0xe4``
___


<a name="bitwiseOr" ></a>

### <code>bitwiseOr</code>
<code><b>bitwiseOr(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
Opérateur Or au niveau du bit sur des types de données intégraux. Identique à l’opérateur | * ``bitwiseOr(0xf4, 0xef)``
* ``0xff``
* ``(0xf4 | 0xef)``
* ``0xff``
___


<a name="bitwiseXor" ></a>

### <code>bitwiseXor</code>
<code><b>bitwiseXor(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Opérateur Or au niveau du bit sur des types de données intégraux. Identique à l’opérateur | * ``bitwiseXor(0xf4, 0xef)``
* ``0x1b``
* ``(0xf4 ^ 0xef)``
* ``0x1b``
* ``(true ^ false)``
* ``true``
* ``(true ^ true)``
* ``false``
___


<a name="blake2b" ></a>

### <code>blake2b</code>
<code><b>blake2b(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcule la synthèse Blake2 de l’ensemble de colonnes comprenant différents types de données primitifs à partir d’une longueur en bits, qui peut uniquement être égale aux multiples de 8 compris entre 8 et 512. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne * ``blake2b(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``'c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d'``
___


<a name="blake2bBinary" ></a>

### <code>blake2bBinary</code>
<code><b>blake2bBinary(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => binary</b></code><br/><br/>
Calcule la synthèse Blake2 de l’ensemble de colonnes comprenant différents types de données primitifs à partir d’une longueur en bits, qui peut uniquement être égale aux multiples de 8 compris entre 8 et 512. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne * ``blake2bBinary(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``unHex('c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d')``
___


<a name="byItem" ></a>

### <code>byItem</code>
<code><b>byItem(<i>&lt;parent column&gt;</i> : any, <i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Recherche un sous-élément dans une structure ou un tableau de structure. Si plusieurs correspondances sont trouvées, retourne la première. S’il n’existe aucune correspondance, une valeur Null est retournée. Le type de la valeur retournée doit être converti par l’une des actions de conversion de type (? date, ? chaîne, etc.).  Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre * ``byItem( byName('customer'), 'orderItems') ? (itemName as string, itemQty as integer)``
* ``byItem( byItem( byName('customer'), 'orderItems'), 'itemName') ? string``
___


<a name="byName" ></a>

### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Sélectionne une valeur de colonne selon le nom dans le flux de données. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument. S’il existe plusieurs correspondances, la première correspondance est retournée. S’il n’existe aucune correspondance, une valeur Null est retournée. Le type de la valeur retournée doit être converti par l’une des fonctions de conversion de type (TO_DATE, TO_STRING, etc.).  Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.  
* ``toString(byName('parent'))``  
* ``toLong(byName('income'))``  
* ``toBoolean(byName('foster'))``  
* ``toLong(byName($debtCol))``  
* ``toString(byName('Bogus Column'))``  
* ``toString(byName('Bogus Column', 'DeriveStream'))``  
___


<a name="byNames" ></a>

### <code>byNames</code>
<code><b>byNames(<i>&lt;column names&gt;</i> : array, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Sélectionnez un tableau de colonnes par son nom dans le flux. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument. S’il existe plusieurs correspondances, la première correspondance est retournée. S’il n’existe aucune correspondance pour une colonne, la sortie entière est une valeur NULL. La valeur retournée requiert des fonctions de conversion de type (ToDate, toString, ...).  Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.
* ``toString(byNames(['parent', 'child']))``
* ``byNames(['parent']) ? string``
* ``toLong(byNames(['income']))``
* ``byNames(['income']) ? long``
* ``toBoolean(byNames(['foster']))``
* ``toLong(byNames($debtCols))``
* ``toString(byNames(['a Column']))``
* ``toString(byNames(['a Column'], 'DeriveStream'))``
* ``byNames(['orderItem']) ? (itemName as string, itemQty as integer)``
___


<a name="byOrigin" ></a>

### <code>byOrigin</code>
<code><b>byOrigin(<i>&lt;column name&gt;</i> : string, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
Sélectionne une valeur de colonne selon le nom dans le flux de données d’origine. Le deuxième argument est le nom du flux d’origine. S’il existe plusieurs correspondances, la première correspondance est retournée. S’il n’existe aucune correspondance, une valeur Null est retournée. Le type de la valeur retournée doit être converti par l’une des fonctions de conversion de type (TO_DATE, TO_STRING, etc.). Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.  
* ``toString(byOrigin('ancestor', 'ancestorStream'))``
___


<a name="byOrigins" ></a>

### <code>byOrigins</code>
<code><b>byOrigins(<i>&lt;column names&gt;</i> : array, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
Sélectionne un tableau de colonnes par son nom dans le flux. Le deuxième argument est le flux à partir duquel il provient. S’il existe plusieurs correspondances, la première correspondance est retournée. S’il n’existe aucune correspondance, une valeur Null est retournée. Le type de la valeur retournée doit être converti par l’une des fonctions de conversion de type (TO_DATE, TO_STRING, etc.). Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.
* ``toString(byOrigins(['ancestor1', 'ancestor2'], 'ancestorStream'))``
___


<a name="byPath" ></a>

### <code>byPath</code>
<code><b>byPath(<i>&lt;value1&gt;</i> : string, [<i>&lt;streamName&gt;</i> : string]) => any</b></code><br/><br/>
Recherche un chemin d’accès hiérarchique par nom dans le flux. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument. Si aucun chemin d’accès n’est trouvé, la valeur Null est renvoyée. Les noms/chemins de colonnes connus au moment de la conception doivent être adressés uniquement par leur nom ou leur chemin en notation pointée. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.  
* ``byPath('grandpa.parent.child') => column`` 
___


<a name="byPosition" ></a>

### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Sélectionne une valeur de colonne selon sa position relative (base 1) dans le flux. Si la position est hors limites, une valeur Null est retournée. Le type de la valeur retournée doit être converti par l’une des fonctions de conversion de type (TO_DATE, TO_STRING, etc.). Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.  
* ``toString(byPosition(1))``  
* ``toDecimal(byPosition(2), 10, 2)``  
* ``toBoolean(byName(4))``  
* ``toString(byName($colName))``  
* ``toString(byPosition(1234))``  
___


<a name="case" ></a>

### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Selon des conditions alternantes, applique une valeur ou une autre. Si les entrées sont en nombre égal, l’autre valeur est Null par défaut pour la dernière condition.  
* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``  
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``  
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``  
___


<a name="cbrt" ></a>

### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule la racine cubique d’un nombre.  
* ``cbrt(8) -> 2.0``  
___


<a name="ceil" ></a>

### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Retourne le plus petit entier qui n’est pas inférieur au nombre.  
* ``ceil(-0.1) -> 0``  
___


<a name="coalesce" ></a>

### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Renvoie la première valeur non nulle d'un jeu d'entrées. Toutes les entrées doivent être du même type.  
* ``coalesce(10, 20) -> 10``  
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``  
___


<a name="collect" ></a>

### <code>collect</code>
<code><b>collect(<i>&lt;value1&gt;</i> : any) => array</b></code><br/><br/>
Collecte toutes les valeurs de l’expression dans le groupe agrégé dans un tableau. Les structures peuvent être collectées et transformées en d’autres structures au cours de ce processus. Le nombre d’éléments est égal au nombre de lignes dans ce groupe, et peut contenir des valeurs Null. Le nombre d’éléments collectés doit être petit.  
* ``collect(salesPerson)``
* ``collect(firstName + lastName))``
* ``collect(@(name = salesPerson, sales = salesAmount) )``
___


<a name="columnNames" ></a>

### <code>columnNames</code>
<code><b>columnNames(<i>&lt;value1&gt;</i> : string) => array</b></code><br/><br/>
Obtient les noms de toutes les colonnes de sortie pour un flux. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument.  
* ``columnNames()``
* ``columnNames('DeriveStream')``
___


<a name="columns" ></a>

### <code>columns</code>
<code><b>columns([<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Obtient les valeurs de toutes les colonnes de sortie pour un flux. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument.   
* ``columns()``
* ``columns('DeriveStream')``
___


<a name="compare" ></a>

### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Compare deux valeurs du même type. Retourne un entier négatif si valeur1 < valeur2, 0 si valeur1 == valeur2, une valeur positive si valeur1 > valeur2.  
* ``(compare(12, 24) < 1) -> true``  
* ``(compare('dumbo', 'dum') > 0) -> true``  
___


<a name="concat" ></a>

### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatène un nombre variable de chaînes. Identique à l’opérateur + avec des chaînes.  
* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``  
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``  
* ``isNull('sql' + null) -> true``  
___


<a name="concatWS" ></a>

### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatène un nombre variable de chaînes avec un séparateur. Le premier paramètre est le séparateur.  
* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``  
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``  
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``  
___


<a name="contains" ></a>

### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
Retourne true si un élément quelconque du tableau fourni correspond à true dans le prédicat fourni. Contains attend une référence à un élément dans la fonction de prédicat comme #item.  
* ``contains([1, 2, 3, 4], #item == 3) -> true``  
* ``contains([1, 2, 3, 4], #item > 5) -> false``  
___


<a name="cos" ></a>

### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de cosinus.  
* ``cos(10) -> -0.8390715290764524``  
___


<a name="cosh" ></a>

### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule le cosinus hyperbolique d’une valeur.  
* ``cosh(0) -> 1.0``  
___


<a name="count" ></a>

### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Obtient le nombre agrégé de valeurs. Si la ou les colonnes facultatives sont spécifiées, les valeurs Null ne sont pas comptabilisées.  
* ``count(custId)``  
* ``count(custId, custName)``  
* ``count()``  
* ``count(iif(isNull(custId), 1, NULL))``  
___


<a name="countDistinct" ></a>

### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Obtient la somme agrégée de valeurs distinctes appartenant à un ensemble de colonnes.  
* ``countDistinct(custId, custName)``  
___


<a name="countIf" ></a>

### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Selon certains critères, obtient le nombre agrégé de valeurs. Si la colonne facultative est spécifiée, les valeurs Null ne sont pas comptabilisées.  
* ``countIf(state == 'CA' && commission < 10000, name)``  
___


<a name="covariancePopulation" ></a>

### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Obtient la covariance de remplissage entre deux colonnes.  
* ``covariancePopulation(sales, profit)``  
___


<a name="covariancePopulationIf" ></a>

### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient la covariance de remplissage de deux colonnes.  
* ``covariancePopulationIf(region == 'West', sales)``  
___


<a name="covarianceSample" ></a>

### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Obtient l’exemple de covariance de deux colonnes.  
* ``covarianceSample(sales, profit)``  
___


<a name="covarianceSampleIf" ></a>

### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient l’exemple de covariance de deux colonnes.  
* ``covarianceSampleIf(region == 'West', sales, profit)``  
___



<a name="crc32" ></a>

### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Calcule le code de hachage CRC32 de l’ensemble de colonnes comprenant différents types de données primitives à partir d’une longueur en bits, qui peut uniquement être égale aux valeurs 0(256), 224, 256, 384, 512. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne.  
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``  
___


<a name="cumeDist" ></a>

### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
La fonction CumeDist calcule la position d’une valeur par rapport à toutes les valeurs de la partition. Le résultat correspond au nombre de lignes précédant ou correspondant à la ligne actuelle dans l’ordre de la partition, divisé par le nombre total de lignes de la partition de fenêtre. Toutes les valeurs égales auront la même position.  
* ``cumeDist()``  
___


<a name="currentDate" ></a>

### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Obtient la date à laquelle l’exécution du travail commence. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). 
* ``currentDate() == toDate('2250-12-31') -> false``  
* ``currentDate('PST')  == toDate('2250-12-31') -> false``  
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``  
___


<a name="currentTimestamp" ></a>

### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Obtient l’horodatage du début de l’exécution du travail avec le fuseau horaire local.  
* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``  
___


<a name="currentUTC" ></a>

### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Obtient l’horodatage actuel au format UTC. Si vous souhaitez que votre heure actuelle soit interprétée dans un fuseau horaire différent de celui de votre cluster, vous pouvez passer un fuseau horaire facultatif au format « GMT », « PST », « UTC », « Amérique/Caïmans ». La valeur par défaut est le fuseau horaire actuel. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). Pour convertir l’heure UTC en un fuseau horaire différent, utilisez `fromUTC()`.  
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``  
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___


<a name="dayOfMonth" ></a>

### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtient le jour du mois dans une date.  
* ``dayOfMonth(toDate('2018-06-08')) -> 8``  
___


<a name="dayOfWeek" ></a>

### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtient le jour de la semaine dans une date. 1 - dimanche, 2 - lundi..., 7 - samedi.  
* ``dayOfWeek(toDate('2018-06-08')) -> 6``  
___


<a name="dayOfYear" ></a>

### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtient le jour de l’année dans une date.  
* ``dayOfYear(toDate('2016-04-09')) -> 100``  
___


<a name="days" ></a>

### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durée en millisecondes du nombre de jours.  
* ``days(2) -> 172800000L``  
___


<a name="degrees" ></a>

### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Convertit les radians en degrés.  
* ``degrees(3.141592653589793) -> 180``  
___


<a name="denseRank" ></a>

### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
Calcule le rang d’une valeur dans un groupe de valeurs spécifiées dans la clause ORDER BY d’une fenêtre. Le résultat est égal à 1 plus le nombre de lignes précédant ou correspondant à la ligne actuelle dans l’ordre de la partition. Les valeurs ne vont pas produire d’écarts dans la séquence. Le rang Dense fonctionne même quand les données ne sont pas triées, et il recherche les valeurs qui ont été modifiées.  
* ``denseRank()``  
___


<a name="distinct" ></a>

### <code>distinct</code>
<code><b>distinct(<i>&lt;value1&gt;</i> : array) => array</b></code><br/><br/>
Retourne un ensemble distinct d’éléments d’un tableau.
* ``distinct([10, 20, 30, 10]) => [10, 20, 30]``
___


<a name="divide" ></a>

### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Divise une paire de nombres. Identique à l’opérateur `/`.  
* ``divide(20, 10) -> 2``  
* ``20 / 10 -> 2``
___


<a name="dropLeft" ></a>

### <code>dropLeft</code>
<code><b>dropLeft(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : integer) => string</b></code><br/><br/>
Supprime le nombre de caractères nécessaire à partir de la gauche de la chaîne. Si le nombre à supprimer est supérieur à la longueur de la chaîne, une chaîne vide est retournée.
*   dropLeft('bojjus', 2) => 'jjus' *   dropLeft('cake', 10) => '' ___


<a name="dropRight" ></a>

### <code>dropRight</code>
<code><b>dropRight(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : integer) => string</b></code><br/><br/>
Supprime le nombre de caractères nécessaire à partir de la droite de la chaîne. Si le nombre à supprimer est supérieur à la longueur de la chaîne, une chaîne vide est retournée.
*   dropRight('bojjus', 2) => 'bojj' *   dropRight('cake', 10) => '' ___


<a name="endsWith" ></a>

### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Vérifie si la chaîne se termine par la chaîne fournie.  
* ``endsWith('dumbo', 'mbo') -> true``  
___


<a name="equals" ></a>

### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Opérateur de comparaison « Est égal à » Identique à l’opérateur ==.  
* ``equals(12, 24) -> false``  
* ``12 == 24 -> false``  
* ``'bad' == 'bad' -> true``  
* ``isNull('good' == toString(null)) -> true``  
* ``isNull(null == null) -> true``  
___


<a name="equalsIgnoreCase" ></a>

### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Opérateur de comparaison « Est égal à » qui ignore la casse. Identique à l’opérateur <=>.  
* ``'abc'<=>'Abc' -> true``  
* ``equalsIgnoreCase('abc', 'Abc') -> true``  
___


<a name="escape" ></a>

### <code>escape</code>
<code><b>escape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
Échappe une chaîne conformément à un format. Les valeurs littérales pour le format acceptable sont « json », « xml », « ecmascript », « html », « java ».
___


<a name="except" ></a>

### <code>except</code>
<code><b>except(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
Retourne un jeu de différences d’un tableau à partir d’un autre en supprimant les doublons.
* ``except([10, 20, 30], [20, 40]) => [10, 30]``  
___


<a name="expr" ></a>

### <code>expr</code>
<code><b>expr(<i>&lt;expr&gt;</i> : string) => any</b></code><br/><br/>
Génère une expression à partir d’une chaîne. Cela revient à écrire cette expression sous une forme non littérale. Ceci peut être utilisé pour passer des paramètres sous forme de représentations de chaîne.
*    expr(’price * discount’) => any ___


<a name="factorial" ></a>

### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Calcule la factorielle d’un nombre.  
* ``factorial(5) -> 120``  
___


<a name="false" ></a>

### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Retourne toujours une valeur false. Utilisez la fonction `syntax(false())` s’il existe une colonne nommée 'false'.  
* ``(10 + 20 > 30) -> false``  
* ``(10 + 20 > 30) -> false()``
___


<a name="filter" ></a>

### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
Filtre hors du tableau les éléments qui ne respectent pas le prédicat fourni. Filter attend une référence à un élément dans la fonction de prédicat comme #item.  
* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``  
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``  
___


<a name="find" ></a>

### <code>find</code>
<code><b>find(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Recherche le premier élément d’un tableau qui correspond à la condition. Elle prend une fonction de filtre dans laquelle vous pouvez traiter l’élément dans le tableau comme #item. Pour les mappages profondément imbriqués, vous pouvez faire référence aux mappages parents à l’aide de la notation #item_n(#item_1, #item_2...).  
* ``find([10, 20, 30], #item > 10) -> 20``
* ``find(['azure', 'data', 'factory'], length(#item) > 4) -> 'azure'``
* ``find([
      @(
         name = 'Daniel',
         types = [
                   @(mood = 'jovial', behavior = 'terrific'),
                   @(mood = 'grumpy', behavior = 'bad')
                 ]
        ),
      @(
         name = 'Mark',
         types = [
                   @(mood = 'happy', behavior = 'awesome'),
                   @(mood = 'calm', behavior = 'reclusive')
                 ]
        )
      ],
      contains(#item.types, #item.mood=='happy')  /*Filter out the happy kid*/
    )``
* ``
     @(
           name = 'Mark',
           types = [
                     @(mood = 'happy', behavior = 'awesome'),
                     @(mood = 'calm', behavior = 'reclusive')
                   ]
      )
    ``  
___


<a name="first" ></a>* ``
 @(
       name = 'Mark',
       types = [
                 @(mood = 'happy', behavior = 'awesome'),
                 @(mood = 'calm', behavior = 'reclusive')
               ]
  )
``  
___


<a name="first" ></a>

### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Obtient la première valeur d’un groupe de colonnes. Si le deuxième paramètre ignoreNulls est omis, il est supposé que sa valeur est false.  
* ``first(sales)``  
* ``first(sales, false)``  
___



<a name="flatten" ></a>

### <code>flatten</code>
<code><b>flatten(<i>&lt;array&gt;</i> : array, <i>&lt;value2&gt;</i> : array ..., <i>&lt;value2&gt;</i> : boolean) => array</b></code><br/><br/>
Aplatit le ou les tableaux en un seul tableau. Les tableaux d’éléments atomiques sont retournés sans modification. Le dernier argument est facultatif et sa valeur par défaut est false pour aplatir de manière récursive plus d’un niveau de profondeur.
*   ``flatten([['bojjus', 'girl'], ['gunchus', 'boy']]) => ['bojjus', 'girl', 'gunchus', 'boy']``
*   ``flatten([[['bojjus', 'gunchus']]] , true) => ['bojjus', 'gunchus']``
___


<a name="floor" ></a>

### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Retourne le plus grand entier qui n’est pas supérieur au nombre.  
* ``floor(-0.1) -> -1``  
___


<a name="fromBase64" ></a>

### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Décode la chaîne encodée en base64.
* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``  
___


<a name="fromUTC" ></a>

### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Convertit vers l’horodatage à partir du temps universel coordonné. Vous pouvez, si vous le souhaitez, passer un fuseau horaire au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. La valeur par défaut est le fuseau horaire actuel. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``fromUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``fromUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___


<a name="greater" ></a>

### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Opérateur de comparaison « Supérieur à » Identique à l’opérateur >.  
* ``greater(12, 24) -> false``  
* ``('dumbo' > 'dum') -> true``  
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___


<a name="greaterOrEqual" ></a>

### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Opérateur de comparaison « Supérieur ou égal à ». Identique à l’opérateur >=.  
* ``greaterOrEqual(12, 12) -> true``  
* ``('dumbo' >= 'dum') -> true``  
___


<a name="greatest" ></a>

### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Retourne la valeur la plus élevée parmi celles de la liste des valeurs d’entrée, en ignorant les valeurs Null. Retourne la valeur Null si toutes les entrées ont une valeur Null.  
* ``greatest(10, 30, 15, 20) -> 30``  
* ``greatest(10, toInteger(null), 20) -> 20``  
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``  
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___


<a name="hasColumn" ></a>

### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Vérifie une valeur de colonne par son nom dans le flux de données. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument. Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.  
* ``hasColumn('parent')``  
___


<a name="hasPath" ></a>

### <code>hasPath</code>
<code><b>hasPath(<i>&lt;value1&gt;</i> : string, [<i>&lt;streamName&gt;</i> : string]) => boolean</b></code><br/><br/>
Vérifie s’il existe un certain chemin d’accès hiérarchique par nom dans le flux. Vous pouvez transmettre un nom de flux facultatif en tant que deuxième argument. Les noms/chemins de colonnes connus au moment de la conception doivent être adressés uniquement par leur nom ou leur chemin en notation pointée. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser des substitutions de paramètre.  
* ``hasPath('grandpa.parent.child') => boolean``
___  


<a name="hex" ></a>

### <code>hex</code>
<code><b>hex(<i>\<value1\></i>: binary) => string</b></code><br/><br/>
Renvoie une représentation sous forme de chaîne hexadécimale d'une valeur binaire * ``hex(toBinary([toByte(0x1f), toByte(0xad), toByte(0xbe)])) -> '1fadbe'``
___


<a name="hour" ></a>

### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtient la valeur d’heure d’un horodatage. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``  
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``  
___


<a name="hours" ></a>

### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durée en millisecondes du nombre d’heures.  
* ``hours(2) -> 7200000L``  
___


<a name="iif" ></a>

### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Selon la condition, applique une valeur ou une autre. Si l’autre valeur n’est pas spécifiée, elle est considérée comme Null. Les deux valeurs doivent être compatibles (numérique, chaîne...). * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``  
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``  
___


<a name="iifNull" ></a>

### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
Vérifie si le premier paramètre a la valeur Null. Si la valeur n’est pas Null, le premier paramètre est retourné. Si la valeur est Null, le deuxième paramètre est retourné. Si trois paramètres sont spécifiés, le comportement est le même que iff(isNull(valeur1), valeur2, valeur3), et le troisième paramètre est retourné si la première valeur n’a pas la valeur Null.  
* ``iifNull(10, 20) -> 10``  
* ``iifNull(null, 20, 40) -> 20``  
* ``iifNull('azure', 'data', 'factory') -> 'factory'``  
* ``iifNull(null, 'data', 'factory') -> 'data'``  
___


<a name="in" ></a>

### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Vérifie si un élément se trouve dans le tableau.  
* ``in([10, 20, 30], 10) -> true``  
* ``in(['good', 'kid'], 'bad') -> false``  
___


<a name="initCap" ></a>

### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Convertit la première lettre de chaque mot en majuscules. Les mots sont identifiés comme étant séparés par des espaces blancs.  
* ``initCap('cool iceCREAM') -> 'Cool Icecream'``  
___


<a name="instr" ></a>

### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Recherche la position (de base 1) de la sous-chaîne au sein d’une chaîne. Si elle est introuvable, 0 est retourné.  
* ``instr('dumbo', 'mbo') -> 3``  
* ``instr('microsoft', 'o') -> 5``  
* ``instr('good', 'bad') -> 0``  
___


<a name="intersect" ></a>

### <code>intersect</code>
<code><b>intersect(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
Retourne un ensemble d’intersection d’un élément distinct depuis 2 tableaux.
* ``intersect([10, 20, 30], [20, 40]) => [20]``  
___


<a name="isBitSet" ></a>

### <code>isBitSet</code>
<code><b>isBitSet (<i><i>\<value1\></i></i> : array, <i>\<value2\></i>:integer ) => boolean</b></code><br/><br/>
Vérifie si une position de bit est définie dans ce bitset * ``isBitSet(toBitSet([10, 32, 98]), 10) => true``
___


<a name="isBoolean" ></a>

### <code>isBoolean</code>
<code><b>isBoolean(<i>\<value1\></i>: string) => boolean</b></code><br/><br/>
Détermine si la valeur de chaîne est une valeur booléenne conformément aux règles de ``toBoolean()``
* ``isBoolean('true') -> true``
* ``isBoolean('no') -> true``
* ``isBoolean('microsoft') -> false``
___


<a name="isByte" ></a>

### <code>isByte</code>
<code><b>isByte(<i>\<value1\></i> : string) => boolean</b></code><br/><br/>
Détermine si la valeur de chaîne est une valeur d'octet au format facultatif conformément aux règles de ``toByte()``
* ``isByte('123') -> true``
* ``isByte('chocolate') -> false``
___


<a name="isDate" ></a>

### <code>isDate</code>
<code><b>isDate (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Détermine si la chaîne de date d'entrée est une date en utilisant un format de date d'entrée facultatif. Reportez-vous au SimpleDateFormat de Java pour les formats disponibles. Si le format de date d’entrée est omis, le format par défaut est ``yyyy-[M]M-[d]d``. Les formats acceptés sont les suivants : ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]``
* ``isDate('2012-8-18') -> true``
* ``isDate('12/18--234234' -> 'MM/dd/yyyy') -> false``
___


<a name="isDecimal" ></a>

### <code>isDecimal</code>
<code><b>isDecimal (<i>\<value1\></i> : string) => boolean</b></code><br/><br/>
Détermine si la valeur de chaîne est une valeur décimale au format facultatif conformément aux règles de ``toDecimal()``
* ``isDecimal('123.45') -> true``
* ``isDecimal('12/12/2000') -> false``
___


<a name="isDelete" ></a>

### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne est marquée pour la suppression. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  
* ``isDelete()``  
* ``isDelete(1)``  
___


<a name="isDistinct" ></a>

### <code>isDistinct</code>
<code><b>isDistinct(<i>&lt;value1&gt;</i> : any , <i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Détermine si une colonne ou un ensemble de colonnes est distinct. Elle ne compte pas Null comme une valeur distincte *    ``isDistinct(custId, custName) => boolean``
___



<a name="isDouble" ></a>

### <code>isDouble</code>
<code><b>isDouble (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Détermine si la valeur de chaîne est une valeur double au format facultatif conformément aux règles de ``toDouble()``
* ``isDouble('123') -> true``
* ``isDouble('$123.45' -> '$###.00') -> true``
* ``isDouble('icecream') -> false``
___


<a name="isError" ></a>

### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne est marquée comme comprenant une erreur. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  
* ``isError()``  
* ``isError(1)``  
___


<a name="isFloat" ></a>

### <code>isFloat</code>
<code><b>isFloat (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Détermine si la valeur de chaîne est une valeur flottante au format facultatif conformément aux règles de ``toFloat()``
* ``isFloat('123') -> true``
* ``isFloat('$123.45' -> '$###.00') -> true``
* ``isFloat('icecream') -> false``
___


<a name="isIgnore" ></a>

### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne est marquée comme devant être ignorée. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  
* ``isIgnore()``  
* ``isIgnore(1)``  
___


<a name="isInsert" ></a>

### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne est marquée pour l’insertion. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  
* ``isInsert()``  
* ``isInsert(1)``  
___


<a name="isInteger" ></a>

### <code>isInteger</code>
<code><b>isInteger (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Détermine si la valeur de chaîne est une valeur entière au format facultatif conformément aux règles de ``toInteger()``
* ``isInteger('123') -> true``
* ``isInteger('$123' -> '$###') -> true``
* ``isInteger('microsoft') -> false``
___


<a name="isLong" ></a>

### <code>isLong</code>
<code><b>isLong (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Détermine si la valeur de chaîne est une valeur longue au format facultatif conformément aux règles de ``toLong()``
* ``isLong('123') -> true``
* ``isLong('$123' -> '$###') -> true``
* ``isLong('gunchus') -> false``
___


<a name="isMatch" ></a>

### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne correspond à la recherche. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  
* ``isMatch()``  
* ``isMatch(1)``  
___


<a name="isNan" ></a>

### <code>isNan</code>
<code><b>isNan (<i>\<value1\></i> : integral) => boolean</b></code><br/><br/>
Vérifiez s’il ne s’agit pas d’un nombre.
* ``isNan(10.2) => false``
___


<a name="isNull" ></a>

### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Vérifie si la valeur est NULL.  
* ``isNull(NULL()) -> true``  
* ``isNull('') -> false``  
___


<a name="isShort" ></a>

### <code>isShort</code>
<code><b>isShort (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Détermine si la valeur de chaîne est une valeur courte au format facultatif conformément aux règles de ``toShort()``
* ``isShort('123') -> true``
* ``isShort('$123' -> '$###') -> true``
* ``isShort('microsoft') -> false``
___


<a name="isTimestamp" ></a>

### <code>isTimestamp</code>
<code><b>isTimestamp (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Détermine si la chaîne de date d'entrée est un horodateur à l'aide d'un format d'horodatage d'entrée facultatif. Reportez-vous à la classe SimpleDateFormat de Java pour connaître les formats disponibles. Si l'horodatage est omis, le modèle par défaut ``yyyy-[M]M-[d]d hh:mm:ss[.f...]`` est utilisé. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. L'horodatage prend en charge une précision allant jusqu'à la milliseconde avec une valeur de 999. Reportez-vous à la classe SimpleDateFormat de Java pour connaître les formats disponibles.
* ``isTimestamp('2016-12-31 00:12:00') -> true``
* ``isTimestamp('2016-12-31T00:12:00' -> 'yyyy-MM-dd\\'T\\'HH:mm:ss' -> 'PST') -> true``
* ``isTimestamp('2012-8222.18') -> false``
___


<a name="isUpdate" ></a>

### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne est marquée pour la mise à jour. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  
* ``isUpdate()``  
* ``isUpdate(1)``  
___


<a name="isUpsert" ></a>

### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne est marquée pour l’insertion. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. L’index de flux doit être 1 ou 2 et la valeur par défaut est 1.  
* ``isUpsert()``  
* ``isUpsert(1)``  
___


<a name="jaroWinkler" ></a>

### <code>jaroWinkler</code>
<code><b>jaroWinkler(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => double</b></code><br/><br/>
Obtient la distance JaroWinkler entre deux chaînes. 
* ``jaroWinkler('frog', 'frog') => 1.0``  
___


<a name="keyValues" ></a>

### <code>keyValues</code>
<code><b>keyValues(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => map</b></code><br/><br/>
Crée un mappage de clés/valeurs. Le premier paramètre est un tableau de clés et le second est le tableau de valeurs. Les deux tableaux doivent avoir la même longueur.
*   ``keyValues(['bojjus', 'appa'], ['gunchus', 'ammi']) => ['bojjus' -> 'gunchus', 'appa' -> 'ammi']``
___ 


<a name="kurtosis" ></a>

### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient l’aplatissement d’une colonne.  
* ``kurtosis(sales)``  
___


<a name="kurtosisIf" ></a>

### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient l’aplatissement d’une colonne.  
* ``kurtosisIf(region == 'West', sales)``  
___


<a name="lag" ></a>

### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Obtient la valeur des n lignes évaluées dans le premier paramètre avant la ligne actuelle. Le deuxième paramètre correspond au nombre de lignes précédentes à examiner et la valeur par défaut est 1. S’il n’y a pas autant de lignes, la valeur Null est retournée, sauf si une valeur par défaut est spécifiée.  
* ``lag(amount, 2)``  
* ``lag(amount, 2000, 100)``  
___


<a name="last" ></a>

### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Obtient la dernière valeur d’un groupe de colonnes. Si le deuxième paramètre ignoreNulls est omis, il est supposé que sa valeur est false.  
* ``last(sales)``  
* ``last(sales, false)``  
___


<a name="lastDayOfMonth" ></a>

### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Obtient le dernier jour du mois dans une date.  
* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``  
___


<a name="lead" ></a>

### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Obtient la valeur des n lignes évaluées dans le premier paramètre après la ligne actuelle. Le deuxième paramètre correspond au nombre de lignes suivantes à examiner et la valeur par défaut est 1. S’il n’y a pas autant de lignes, la valeur Null est retournée, sauf si une valeur par défaut est spécifiée.  
* ``lead(amount, 2)``  
* ``lead(amount, 2000, 100)``  
___


<a name="least" ></a>

### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Opérateur de comparaison « Inférieur ou égal à ». Identique à l’opérateur <=.  
* ``least(10, 30, 15, 20) -> 10``  
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``  
___


<a name="left" ></a>

### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrait une sous-chaîne comportant un certain nombre de caractères en partant de la gauche d’une chaîne au niveau de l’index 1. Identique à SUBSTRING(str, 1, n).  
* ``left('bojjus', 2) -> 'bo'``  
* ``left('bojjus', 20) -> 'bojjus'``  
___


<a name="length" ></a>

### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Retourne la longueur de la chaîne.  
* ``length('dumbo') -> 5``  
___


<a name="lesser" ></a>

### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Opérateur de comparaison « Inférieur à » Identique à l’opérateur <.  
* ``lesser(12, 24) -> true``  
* ``('abcd' < 'abc') -> false``  
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___


<a name="lesserOrEqual" ></a>

### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Opérateur de comparaison « Inférieur ou égal à ». Identique à l’opérateur <=.  
* ``lesserOrEqual(12, 12) -> true``  
* ``('dumbo' <= 'dum') -> false``  
___


<a name="levenshtein" ></a>

### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Obtient la distance levenshtein entre deux chaînes.  
* ``levenshtein('boys', 'girls') -> 4``  
___


<a name="like" ></a>

### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Le modèle est une chaîne qui est mise en correspondance littéralement. Les symboles spéciaux suivants représentent des exceptions : _ correspond à tout caractère de l’entrée (semblable à . dans les expressions régulières ```posix``` « % » correspond à zéro, un ou plusieurs caractères de l’entrée (comme les caractères « .* » dans les expressions régulières ```posix```).
Le caractère d’échappement est « '' ». Si un caractère d’échappement précède un caractère spécial ou un autre caractère d’échappement, le caractère qui suit a une correspondance littérale. Aucun autre caractère ne peut être échappé.  
* ``like('icecream', 'ice%') -> true``  
___


<a name="locate" ></a>

### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Recherche la position (de base 1) de la sous-chaîne au sein d’une chaîne, en partant d’une certaine position. Si la position est omise, on considère qu’elle est située au début de la chaîne. Si elle est introuvable, 0 est retourné.  
* ``locate('mbo', 'dumbo') -> 3``  
* ``locate('o', 'microsoft', 6) -> 7``  
* ``locate('bad', 'good') -> 0``  
___


<a name="log" ></a>

### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Calcule la valeur de journal. Une base facultative peut être fournie, voire un nombre Euler, le cas échéant.  
* ``log(100, 10) -> 2``  
___


<a name="log10" ></a>

### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule la valeur de journal sur une base 10.  
* ``log10(100) -> 2``  
___


<a name="lookup" ></a>

### <code>lookup</code>
<code><b>lookup(key, key2, ...) => complex[]</b></code><br/><br/>
Recherche la première ligne du récepteur mis en cache en utilisant les clés spécifiées qui correspondent aux clés du récepteur mis en cache.
* ``cacheSink#lookup(movieId)``  
___


<a name="lower" ></a>

### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Convertit une chaîne en minuscules.  
* ``lower('GunChus') -> 'gunchus'``  
___


<a name="lpad" ></a>

### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Décale la chaîne sur la gauche selon la marge intérieure fournie, jusqu’à ce qu’elle atteigne une certaine longueur. Si la longueur de la chaîne est supérieure ou égale à cette longueur, elle est tronquée à la longueur.  
* ``lpad('dumbo', 10, '-') -> '-----dumbo'``  
* ``lpad('dumbo', 4, '-') -> 'dumb'``  
* ``lpad('dumbo', 8, '<>') -> '<><dumbo'``  
___


<a name="ltrim" ></a>

### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Supprime une chaîne de caractères de début situés sur la gauche. Si le deuxième paramètre n’est pas spécifié, cela supprime l’espace blanc. Sinon, cela supprime tout caractère spécifié dans le deuxième paramètre.  
* ``ltrim('  dumbo  ') -> 'dumbo  '``  
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``  
___


<a name="map" ></a>

### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Mappe chaque élément du tableau à un nouvel élément à l’aide de l’expression fournie. Map attend une référence à un élément dans la fonction d’expression comme #item.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___


<a name="mapAssociation" ></a>

### <code>mapAssociation</code>
<code><b>mapAssociation(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => array</b></code><br/><br/>
Transforme une carte en associant les clés aux nouvelles valeurs. Retourne un tableau. Prend une fonction de mappage dans laquelle vous pouvez traiter l’élément comme #key et la valeur actuelle comme #value. 
*   ``mapAssociation(['bojjus' -> 'gunchus', 'appa' -> 'ammi'], @(key = #key, value = #value)) => [@(key = 'bojjus', value = 'gunchus'), @(key = 'appa', value = 'ammi')]``
___ 


<a name="mapIf" ></a>

### <code>mapIf</code>
<code><b>mapIf (<i>\<value1\></i> : array, <i>\<value2\></i> : binaryfunction, \<value3\>: binaryFunction) => any</b></code><br/><br/>
Mappe conditionnellement un tableau à un autre tableau de même longueur ou plus petit. Les valeurs peuvent être de n’importe quel type de données, y compris structTypes. Elle prend une fonction de filtre dans laquelle vous pouvez traiter l’élément dans le tableau comme #item et l’index actuel comme #index. Pour les mappages profondément imbriqués, vous pouvez référencer les mappages parents à l’aide de la notation ``#item_[n](#item_1, #index_1...)``.
*    ``mapIf([10, 20, 30], #item > 10, #item + 5) -> [25, 35]``
* ``mapIf(['icecream', 'cake', 'soda'], length(#item) > 4, upper(#item)) -> ['ICECREAM', 'CAKE']``
___


<a name="mapIndex" ></a>

### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
Mappe chaque élément du tableau à un nouvel élément à l’aide de l’expression fournie. Map attend une référence à un élément dans la fonction d’expression sous la forme #item et une référence à l’index des éléments sous la forme #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___


<a name="mapLoop" ></a>

### <code>mapLoop</code>
<code><b>mapLoop(<i>\<value1\></i> : integer, <i>\<value2\></i> : unaryfunction) => any</b></code><br/><br/>
Boucle de 1 à la longueur définie pour créer un tableau de cette longueur. Elle prend une fonction de mappage dans laquelle vous pouvez traiter l’index dans le tableau comme #index. Pour les mappages profondément imbriqués, vous pouvez référencer les mappages parents à l’aide de la notation #index_n(#index_1, #index_2...).
*    ``mapLoop(3, #index * 10) -> [10, 20, 30]``
___


<a name="max" ></a>

### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Obtient la valeur maximale d’une colonne.  
* ``max(sales)``  
___


<a name="maxIf" ></a>

### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Selon certains critères, obtient la valeur maximale d’une colonne.  
* ``maxIf(region == 'West', sales)``  
___


<a name="md5" ></a>

### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcule le code de hachage MD5 de l’ensemble de colonnes comprenant différents types de données primitives, et retourne une chaîne hexadécimale de 32 caractères. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne.  
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``  
___


<a name="mean" ></a>

### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtient la moyenne des valeurs d’une colonne. Identique à AVG.  
* ``mean(sales)``  
___


<a name="meanIf" ></a>

### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Selon certains critères, obtient la moyenne des valeurs d’une colonne. Identique à avgIf.  
* ``meanIf(region == 'West', sales)``  
___


<a name="millisecond" ></a>

### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtient la valeur en millisecondes d'une date. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___


<a name="milliseconds" ></a>

### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durée en millisecondes du nombre de millisecondes.  
* ``milliseconds(2) -> 2L``  
___


<a name="min" ></a>

### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Obtient la valeur minimale d’une colonne.  
* ``min(sales)``  
___


<a name="minIf" ></a>

### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Selon certains critères, obtient la valeur minimale d’une colonne.  
* ``minIf(region == 'West', sales)``  
___


<a name="minus" ></a>

### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Soustrait des nombres. Soustrait le nombre de jours à une date. Soustrait une durée d’un timestamp. Soustrait deux timestamps pour obtenir la différence en millisecondes. Identique à l’opérateur -.  
* ``minus(20, 10) -> 10``  
* ``20 - 10 -> 10``  
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``  
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``  
___


<a name="minute" ></a>

### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtient la valeur de minute d’un horodatage. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``  
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``  
___


<a name="minutes" ></a>

### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durée en millisecondes du nombre de minutes.  
* ``minutes(2) -> 120000L``  
___


<a name="mlookup" ></a>

### <code>mlookup</code>
<code><b>mlookup(key, key2, ...) => complex[]</b></code><br/><br/>
Recherche toutes les lignes correspondantes du récepteur mis en cache en utilisant les clés spécifiées qui correspondent aux clés du récepteur mis en cache.
* ``cacheSink#mlookup(movieId)``  
___


<a name="mod" ></a>

### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Module une paire de nombres. Identique à l’opérateur %.  
* ``mod(20, 8) -> 4``  
* ``20 % 8 -> 4``  
___


<a name="month" ></a>

### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtient la valeur de mois d’une date ou d’un horodatage.  
* ``month(toDate('2012-8-8')) -> 8``  
___


<a name="monthsBetween" ></a>

### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
Obtient le nombre de mois entre deux dates. Vous pouvez arrondir le résultat du calcul. Vous pouvez transmettre un fuseau horaire facultatif au format « GMT », « PST », « UTC », « America/Caïman ». Le fuseau horaire local est utilisé par défaut. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``  
___


<a name="multiply" ></a>

### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Multiplie une paire de nombres. Identique à l’opérateur *.  
* ``multiply(20, 10) -> 200``  
* ``20 * 10 -> 200``  
___


<a name="negate" ></a>

### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Inverse un nombre. Transforme les nombres positifs en nombres négatifs, et vice versa.  
* ``negate(13) -> -13``  
___


<a name="nextSequence" ></a>

### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Retourne la séquence unique suivante. Le nombre est consécutif uniquement au sein d’une partition et il est préfixé de partitionId.  
* ``nextSequence() == 12313112 -> false``  
___


<a name="normalize" ></a>

### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalise la valeur de chaîne pour séparer les caractères Unicode accentués.  
* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
___


<a name="not" ></a>

### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Opérateur de négation logique.  
* ``not(true) -> false``  
* ``not(10 == 20) -> true``  
___


<a name="notEquals" ></a>

### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Opérateur de comparaison « N'est pas égal à » Identique à l’opérateur !=.  
* ``12 != 24 -> true``  
* ``'bojjus' != 'bo' + 'jjus' -> false``  
___


<a name="notNull" ></a>

### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Vérifie si la valeur n’est pas Null.  
* ``notNull(NULL()) -> false``  
* ``notNull('') -> true``  
___


<a name="nTile" ></a>

### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
La fonction ```NTile``` divise les lignes de chaque partition de fenêtre en `n` compartiments, compris entre 1 et `n` au maximum. Les valeurs de compartiment varient de 1 au maximum. Si les lignes de la partition ne sont pas réparties de façon uniforme dans les compartiments, les valeurs restantes sont distribuées une par une dans chaque compartiment, en commençant par le premier compartiment. La fonction ```NTile``` est utile pour le calcul des tertiles, quartiles, déciles et autres statistiques de synthèse courantes. La fonction calcule deux variables pendant l’initialisation : Aucune ligne supplémentaire ne sera ajoutée à la taille d’un compartiment habituel. Les deux variables sont basées sur la taille de la partition actuelle. Pendant le processus de calcul, la fonction effectue le suivi du nombre actuel de lignes, du nombre actuel de compartiments et du nombre de lignes où le compartiment va changer (bucketThreshold). Lorsque le nombre actuel de lignes atteint le seuil du compartiment, la valeur du compartiment est augmentée de 1 et le seuil est augmenté par la taille du compartiment (plus 1, si le compartiment actuel est rempli).  
* ``nTile()``  
* ``nTile(numOfBuckets)``  
___


<a name="null" ></a>

### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Retourne une valeur NULL. Utilisez la fonction `syntax(null())` s’il existe une colonne nommée 'null'. Toute opération qui l’utilise entraînera une valeur NULL.  
* ``isNull('dumbo' + null) -> true``  
* ``isNull(10 * null) -> true``  
* ``isNull('') -> false``  
* ``isNull(10 + 20) -> false``  
* ``isNull(10/0) -> true``  
___


<a name="or" ></a>

### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Opérateur OR logique. Identique à ||.  
* ``or(true, false) -> true``  
* ``true || false -> true``  
___


<a name="originColumns" ></a>

### <code>originColumns</code>
<code><b>originColumns(<i>&lt;streamName&gt;</i> : string) => any</b></code><br/><br/>
Obtient toutes les colonnes de sortie pour un flux d’origine où des colonnes ont été créées. Se place au sein d’une autre fonction.
* ``array(toString(originColumns('source1')))``
___  


<a name="output" ></a>

### <code>output</code>
<code><b>output() => any</b></code><br/><br/>
Retourne la première ligne des résultats du récepteur de cache * ``cacheSink#output()``  
___


<a name="outputs" ></a>

### <code>outputs</code>
<code><b>output() => any</b></code><br/><br/>
Retourne la totalité des lignes de sortie des résultats du récepteur de cache * ``cacheSink#outputs()``
___



<a name="partitionId" ></a>

### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Retourne l’ID de partition actuel où se trouve la ligne d’entrée.  
* ``partitionId()``  
___


<a name="pMod" ></a>

### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Module positivement une paire de nombres.  
* ``pmod(-20, 8) -> 4``  
___


<a name="power" ></a>

### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Élève un nombre à la puissance d’un autre nombre.  
* ``power(10, 2) -> 100``  
___


<a name="radians" ></a>

### <code>radians</code>
<code><b>radians(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Convertit les degrés en radians * ``radians(180) => 3.141592653589793``  
___


<a name="random" ></a>

### <code>random</code>
<code><b>random(<i>&lt;value1&gt;</i> : integral) => long</b></code><br/><br/>
Retourne un nombre aléatoire selon une valeur initiale facultative dans une partition. La valeur initiale doit être une valeur fixe et elle est utilisée conjointement avec la valeur partitionId pour produire des valeurs aléatoires * ``random(1) == 1 -> false``
___


<a name="rank" ></a>

### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
Calcule le rang d’une valeur dans un groupe de valeurs spécifiées dans la clause ORDER BY d’une fenêtre. Le résultat est égal à 1 plus le nombre de lignes précédant ou correspondant à la ligne actuelle dans l’ordre de la partition. Les valeurs vont produire des écarts dans la séquence. Le rang fonctionne même quand les données ne sont pas triées, et il recherche les valeurs qui ont été modifiées.  
* ``rank()``  
___


<a name="reassociate" ></a>

### <code>reassociate</code>
<code><b>reassociate(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => map</b></code><br/><br/>
Transforme une carte en associant les clés aux nouvelles valeurs. Prend une fonction de mappage dans laquelle vous pouvez traiter l’élément comme #key et la valeur actuelle comme #value.  
* ``reassociate(['fruit' -> 'apple', 'vegetable' -> 'tomato'], substring(#key, 1, 1) + substring(#value, 1, 1)) => ['fruit' -> 'fa', 'vegetable' -> 'vt']``
___
  


<a name="reduce" ></a>

### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Accumule des éléments dans un tableau. Reduce attend une référence à un accumulateur et un élément dans la première fonction d’expression comme #acc et #item et il attend que la valeur résultante comme #result soit utilisée dans la deuxième fonction d’expression.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___


<a name="regexExtract" ></a>

### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Extrait une sous-chaîne correspondant à un modèle d’expression régulière donné. Le dernier paramètre identifie le groupe de correspondance. S’il est omis, la valeur par défaut est de 1. Utilisez `<regex>` (apostrophe ouvrante) pour rechercher une chaîne sans séquence d’échappement.  
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``  
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``  
___


<a name="regexMatch" ></a>

### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Vérifie si la chaîne correspond au modèle d’expression régulière donné. Utilisez `<regex>` (apostrophe ouvrante) pour rechercher une chaîne sans séquence d’échappement.  
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``  
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``  
___


<a name="regexReplace" ></a>

### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Remplace toutes les occurrences d’un modèle d’expression régulière par une autre sous-chaîne d’une chaîne donnée. Utilisez `<regex>` (apostrophe ouvrante) pour rechercher une chaîne sans séquence d’échappement.  
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``  
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``  
___


<a name="regexSplit" ></a>

### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Fractionne une chaîne selon un délimiteur basé sur une expression régulière et retourne un tableau de chaînes.  
* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``  
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``  
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``  
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``  
___


<a name="replace" ></a>

### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
Remplace toutes les occurrences d’une sous-chaîne par une autre sous-chaîne dans une chaîne donnée. Si le dernier paramètre est omis, sa valeur par défaut est une chaîne vide.  
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``  
* ``replace('doggie dog', 'dog', '') -> 'gie '``  
* ``replace('doggie dog', 'dog') -> 'gie '``  
___


<a name="reverse" ></a>

### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Inverse une chaîne.  
* ``reverse('gunchus') -> 'suhcnug'``  
___


<a name="right" ></a>

### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrait une sous-chaîne comportant un certain nombre de caractères en partant de la droite. Identique à SUBSTRING(str, LENGTH(str) - n, n).  
* ``right('bojjus', 2) -> 'us'``  
* ``right('bojjus', 20) -> 'bojjus'``  
___


<a name="rlike" ></a>

### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Vérifie si la chaîne correspond au modèle d’expression régulière donné.  
* ``rlike('200.50', `(\d+).(\d+)`) -> true``  
* ``rlike('bogus', `M[0-9]+.*`) -> false``  
___


<a name="round" ></a>

### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Arrondit un nombre donné selon une mise à l’échelle et un mode d’arrondi facultatifs. Si la mise à l’échelle est omise, la valeur par défaut 0 est utilisée. Si le mode est omis, la valeur par défaut ROUND_HALF_UP(5) est utilisée. Les valeurs d’arrondi incluent 1 - ROUND_UP 2 - ROUND_DOWN 3 - ROUND_CEILING 4 - ROUND_FLOOR 5 - ROUND_HALF_UP 6 - ROUND_HALF_DOWN 7 - ROUND_HALF_EVEN 8 - ROUND_UNNECESSARY.  
* ``round(100.123) -> 100.0``  
* ``round(2.5, 0) -> 3.0``  
* ``round(5.3999999999999995, 2, 7) -> 5.40``  
___


<a name="rowNumber" ></a>

### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Attribue un numérotage séquentiel aux lignes d’une fenêtre en commençant par 1.  
* ``rowNumber()``  



<a name="rpad" ></a>

### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Décale la chaîne sur la droite selon la marge intérieure fournie, jusqu’à ce qu’elle atteigne une certaine longueur. Si la longueur de la chaîne est supérieure ou égale à cette longueur, elle est tronquée à la longueur.  
* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``  
* ``rpad('dumbo', 4, '-') -> 'dumb'``  
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``  
___


<a name="rtrim" ></a>

### <code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Supprime une chaîne de caractères de début situés sur la droite. Si le deuxième paramètre n’est pas spécifié, cela supprime l’espace blanc. Sinon, cela supprime tout caractère spécifié dans le deuxième paramètre.  
* ``rtrim('  dumbo  ') -> '  dumbo'``  
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``  
___


<a name="second" ></a>

### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtient la deuxième valeur d’une date. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``  
___


<a name="seconds" ></a>

### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durée en millisecondes du nombre de secondes.  
* ``seconds(2) -> 2000L``  
___


<a name="setBitSet" ></a>

### <code>setBitSet</code>
<code><b>setBitSet (<i>\<value1\></i>: array, <i>\<value2\></i>:array) => array</b></code><br/><br/>
Fixe la position des bits dans ce bitset * ``setBitSet(toBitSet([10, 32]), [98]) => [4294968320L, 17179869184L]``
___  


<a name="sha1" ></a>

### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcule le code de hachage SHA-1 de l’ensemble de colonnes comprenant différents types de données primitives, et retourne une chaîne hexadécimale de 40 caractères. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne.  
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``  
___


<a name="sha2" ></a>

### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcule le code de hachage SHA-2 de l’ensemble de colonnes comprenant différents types de données primitives à partir d’une longueur en bits, qui peut uniquement être égale aux valeurs 0(256), 224, 256, 384, 512. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne.  
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``  
___


<a name="sin" ></a>

### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de sinus.  
* ``sin(2) -> 0.9092974268256817``  
___


<a name="sinh" ></a>

### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de sinus hyperbolique.  
* ``sinh(0) -> 0.0``  
___


<a name="size" ></a>

### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></code><br/><br/>
Recherche la taille d’un tableau ou d’un type de mappage * ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___


<a name="skewness" ></a>

### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient l’asymétrie d’une colonne.  
* ``skewness(sales)``  
___


<a name="skewnessIf" ></a>

### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient l’asymétrie d’une colonne.  
* ``skewnessIf(region == 'West', sales)``  
___


<a name="slice" ></a>

### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extrait un sous-ensemble d’un tableau en partant d’une position. La position est de base 1. Si la longueur est omise, elle est définie par défaut sur la fin de la chaîne.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___


<a name="sort" ></a>

### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
Trie le tableau à l’aide de la fonction de prédicat fournie. Sort attend une référence à deux éléments consécutifs dans la fonction d’expression comme #item1 et #item2.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``  
___


<a name="soundex" ></a>

### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Obtient le code ```soundex``` de la chaîne.  
* ``soundex('genius') -> 'G520'``  
___


<a name="split" ></a>

### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Fractionne une chaîne selon un délimiteur et retourne un tableau de chaînes.  
* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``  
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``  
___


<a name="sqrt" ></a>

### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule la racine carrée d’un nombre.  
* ``sqrt(9) -> 3``  
___


<a name="startsWith" ></a>

### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Vérifie si la chaîne commence par la chaîne fournie.  
* ``startsWith('dumbo', 'du') -> true``  
___


<a name="stddev" ></a>

### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient l’écart type d’une colonne.  
* ``stdDev(sales)``  
___


<a name="stddevIf" ></a>

### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient l’écart type d’une colonne.  
* ``stddevIf(region == 'West', sales)``  
___


<a name="stddevPopulation" ></a>

### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient l’écart type de remplissage d’une colonne.  
* ``stddevPopulation(sales)``  
___


<a name="stddevPopulationIf" ></a>

### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient l’écart type de remplissage d’une colonne.  
* ``stddevPopulationIf(region == 'West', sales)``  
___


<a name="stddevSample" ></a>

### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient l’exemple d’écart type d’une colonne.  
* ``stddevSample(sales)``  
___


<a name="stddevSampleIf" ></a>

### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient l’exemple d’écart type d’une colonne.  
* ``stddevSampleIf(region == 'West', sales)``  
___


<a name="subDays" ></a>

### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Soustrait des jours d’une date ou d’un horodatage. Identique à l’opérateur - pour la date.  
* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``  
___


<a name="subMonths" ></a>

### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Soustrait des mois d’une date ou d’un timestamp.  
* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``  
___


<a name="substring" ></a>

### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extrait une sous-chaîne d’une certaine longueur en partant d’une position. La position est de base 1. Si la longueur est omise, elle est définie par défaut sur la fin de la chaîne.  
* ``substring('Cat in the hat', 5, 2) -> 'in'``  
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``  
* ``substring('Cat in the hat', 5) -> 'in the hat'``  
* ``substring('Cat in the hat', 100, 100) -> ''``  
___


<a name="sum" ></a>

### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtient la somme agrégée d’une colonne numérique.  
* ``sum(col)``  
___


<a name="sumDistinct" ></a>

### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtient la somme agrégée de valeurs distinctes appartenant à une colonne numérique.  
* ``sumDistinct(col)``  
___


<a name="sumDistinctIf" ></a>

### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Selon certains critères, obtient la somme agrégée d’une colonne numérique. La condition peut être basée sur n’importe quelle colonne.  
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``  
* ``sumDistinctIf(true, sales)``  
___


<a name="sumIf" ></a>

### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Selon certains critères, obtient la somme agrégée d’une colonne numérique. La condition peut être basée sur n’importe quelle colonne.  
* ``sumIf(state == 'CA' && commission < 10000, sales)``  
* ``sumIf(true, sales)``  
___


<a name="tan" ></a>

### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de tangente.  
* ``tan(0) -> 0.0``  
___


<a name="tanh" ></a>

### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de tangente hyperbolique.  
* ``tanh(0) -> 0.0``  
___


<a name="toBase64" ></a>

### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Encode la chaîne donnée en base64.  
* ``toBase64('bojjus') -> 'Ym9qanVz'``  
___


<a name="toBinary" ></a>

### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Convertit toutes les valeurs numériques/date/timestamp/chaîne en représentation binaire.  
* ``toBinary(3) -> [0x11]``  
___


<a name="toBoolean" ></a>

### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Convertit ('t', 'true', 'y', 'yes', '1') en valeur true, ('f', 'false', 'n', 'no', '0') en valeur false et toute autre valeur en valeur Null.  
* ``toBoolean('true') -> true``  
* ``toBoolean('n') -> false``  
* ``isNull(toBoolean('truthy')) -> true``  
___


<a name="toByte" ></a>

### <code>toByte</code>
<code><b>toByte(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => byte</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur de type octet. Un format décimal Java facultatif peut être utilisé pour la conversion.  
* ``toByte(123)``
* ``123``
* ``toByte(0xFF)``
* ``-1``
* ``toByte('123')``
* ``123``
___


<a name="toDate" ></a>

### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Convertit la chaîne de date d’entrée en date à l’aide d’un format de date d’entrée facultatif. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. Si le format de date d’entrée est omis, le format par défaut est aaaa-[M]M-[j]j. Les formats acceptés sont les suivants :[ aaaa, aaaa-[M]M, aaaa-[M]M-[j]h, aaaa-[M]M-[j]jT* ].  
* ``toDate('2012-8-18') -> toDate('2012-08-18')``  
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``  
___


<a name="toDecimal" ></a>

### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur de type decimal. Si la précision et l’échelle ne sont pas spécifiées, la valeur utilisée par défaut est (10,2). Un format décimal Java facultatif peut être utilisé pour la conversion. Un format de paramètres régionaux facultatif sous forme de langue BCP47, par exemple en-US, de, zh-CN.  
* ``toDecimal(123.45) -> 123.45``  
* ``toDecimal('123.45', 8, 4) -> 123.4500``  
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``  
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``  
___


<a name="toDouble" ></a>

### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur de type double. Un format décimal Java facultatif peut être utilisé pour la conversion. Un format de paramètres régionaux facultatif sous forme de langue BCP47, par exemple en-US, de, zh-CN.  
* ``toDouble(123.45) -> 123.45``  
* ``toDouble('123.45') -> 123.45``  
* ``toDouble('$123.45', '$###.00') -> 123.45``  
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``  
___


<a name="toFloat" ></a>

### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur de type float. Un format décimal Java facultatif peut être utilisé pour la conversion. Tronque toutes les valeurs de type double.  
* ``toFloat(123.45) -> 123.45f``  
* ``toFloat('123.45') -> 123.45f``  
* ``toFloat('$123.45', '$###.00') -> 123.45f``  
___


<a name="toInteger" ></a>

### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur entière. Un format décimal Java facultatif peut être utilisé pour la conversion. Tronque toutes les valeurs de type long, float ou double.  
* ``toInteger(123) -> 123``  
* ``toInteger('123') -> 123``  
* ``toInteger('$123', '$###') -> 123``  
___


<a name="toLong" ></a>

### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur de type long. Un format décimal Java facultatif peut être utilisé pour la conversion. Tronque toutes les valeurs de type float ou double.  
* ``toLong(123) -> 123``  
* ``toLong('123') -> 123``  
* ``toLong('$123', '$###') -> 123``  
___


<a name="toShort" ></a>

### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur de type short. Un format décimal Java facultatif peut être utilisé pour la conversion. Tronque toutes les valeurs de type integer, long, float ou double.  
* ``toShort(123) -> 123``  
* ``toShort('123') -> 123``  
* ``toShort('$123', '$###') -> 123``  
___


<a name="toString" ></a>

### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Convertit un type de données primitif en une chaîne. Vous pouvez spécifier un format pour les nombres et les dates. Si aucun format n’est spécifié, la valeur système par défaut est utilisée. Le format décimal Java est utilisé pour les nombres. Pour connaître tous les formats de date possibles, reportez-vous à Java SimpleDateFormat. Le format par défaut est aaaa-MM-jj.  
* ``toString(10) -> '10'``  
* ``toString('engineer') -> 'engineer'``  
* ``toString(123456.789, '##,###.##') -> '123,456.79'``  
* ``toString(123.78, '000000.000') -> '000123.780'``  
* ``toString(12345, '##0.#####E0') -> '12.345E3'``  
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``  
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``  
* ``toString(4 == 20) -> 'false'``  
___


<a name="toTimestamp" ></a>

### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Convertit une chaîne en timestamp en fonction d’un format de timestamp facultatif. Si l’horodatage est omis, le modèle par défaut est utilisé : aaaa-[M]M-[j]j hh:mm:ss[.f...]. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. L’horodateur prend en charge une précision à la milliseconde avec la valeur 999. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``  
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___


<a name="toUTC" ></a>

### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Convertit l’horodatage au format UTC. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. La valeur par défaut est le fuseau horaire actuel. Reportez-vous à la classe `SimpleDateFormat` de Java pour connaître les formats disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``toUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  



<a name="translate" ></a>

### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Remplace un jeu de caractères par un autre jeu de caractères dans la chaîne. Chaque caractère est remplacé par un seul caractère.  
* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``  
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``  
___


<a name="trim" ></a>

### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Supprime une chaîne de caractères de début et de fin. Si le deuxième paramètre n’est pas spécifié, cela supprime l’espace blanc. Sinon, cela supprime tout caractère spécifié dans le deuxième paramètre.  
* ``trim('  dumbo  ') -> 'dumbo'``  
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``  
___


<a name="true" ></a>

### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Retourne toujours une valeur true. Utilisez la fonction `syntax(true())` s’il existe une colonne nommée 'true'.  
* ``(10 + 20 == 30) -> true``  
* ``(10 + 20 == 30) -> true()``  
___


<a name="typeMatch" ></a>

### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Correspond au type de la colonne. Peut uniquement être utilisé dans les expressions de modèle. « number » peut être de type short, integer, long, double, float ou decimal ; « integral » peut être de type short, integer ou long ; « fractional » peut être de type double, float ou decimal et « datetime » peut être de type date ou timestamp.  
* ``typeMatch(type, 'number')``  
* ``typeMatch('date', 'datetime')``  
___


<a name="unescape" ></a>

### <code>unescape</code>
<code><b>unescape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
Annule l’échappement d’une chaîne conformément à un format. Les valeurs littérales pour le format acceptable sont « json », « xml », « ecmascript », « html », « java ».
* ```unescape('{\\\\\"value\\\\\": 10}', 'json')```
* ```'{\\\"value\\\": 10}'```
___


<a name="unfold" ></a>

### <code>unfold</code>
<code><b>unfold (<i>&lt;value1&gt;</i>: array) => any</b></code><br/><br/>
Déplie un tableau en un ensemble de lignes et répète les valeurs pour la colonne restante dans chaque ligne.
*   ``unfold(addresses) => any``
*   ``unfold( @(name = salesPerson, sales = salesAmount) ) => any``
___  


<a name="unhex" ></a>

### <code>unhex</code>
<code><b>unhex(<i>\<value1\></i>: string) => binary</b></code><br/><br/>
Annule la représentation sous forme de chaîne hexadécimale d'une valeur binaire. Peut être utilisé en conjonction avec sha2, md5 pour convertir une chaîne en représentation binaire *    ``unhex('1fadbe') -> toBinary([toByte(0x1f), toByte(0xad), toByte(0xbe)])``
*    ``unhex(md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))) -> toBinary([toByte(0x4c),toByte(0xe8),toByte(0xa8),toByte(0x80),toByte(0xbd),toByte(0x62),toByte(0x1a),toByte(0x1f),toByte(0xfa),toByte(0xd0),toByte(0xbc),toByte(0xa9),toByte(0x05),toByte(0xe1),toByte(0xbc),toByte(0x5a)])``



<a name="union" ></a>

### <code>union</code>
<code><b>union(<i>&lt;value1&gt;</i>: array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
Retourne un ensemble d’unions d’éléments distincts depuis 2 tableaux.
* ``union([10, 20, 30], [20, 40]) => [10, 20, 30, 40]``
___  
  


<a name="upper" ></a>

### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Convertit une chaîne en majuscules.  
* ``upper('bojjus') -> 'BOJJUS'``  
___


<a name="uuid" ></a>

### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
Retourne l’UUID généré.  
* ``uuid()``  
___


<a name="variance" ></a>

### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient la variance d’une colonne.  
* ``variance(sales)``  
___


<a name="varianceIf" ></a>

### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient la variance d’une colonne.  
* ``varianceIf(region == 'West', sales)``  
___


<a name="variancePopulation" ></a>

### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient la variance de remplissage d’une colonne.  
* ``variancePopulation(sales)``  
___


<a name="variancePopulationIf" ></a>

### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient la variance de remplissage d’une colonne.  
* ``variancePopulationIf(region == 'West', sales)``  
___


<a name="varianceSample" ></a>

### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient la variance non biaisée d’une colonne.  
* ``varianceSample(sales)``  
___


<a name="varianceSampleIf" ></a>

### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient la variance non biaisée d’une colonne.  
* ``varianceSampleIf(region == 'West', sales)``  



<a name="weekOfYear" ></a>

### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtient la semaine de l’année dans une date.  
* ``weekOfYear(toDate('2008-02-20')) -> 8``  
___


<a name="weeks" ></a>

### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Durée en millisecondes du nombre de semaines.  
* ``weeks(2) -> 1209600000L``  
___


<a name="xor" ></a>

### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Opérateur logique XOR. Identique à l’opérateur ^.  
* ``xor(true, false) -> true``  
* ``xor(true, true) -> false``  
* ``true ^ false -> true``  
___


<a name="year" ></a>

### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtient la valeur d’année d’une date.  
* ``year(toDate('2012-8-8')) -> 2012``  

## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment utiliser le Générateur d’expressions](concepts-data-flow-expression-builder.md).