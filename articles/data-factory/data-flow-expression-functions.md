---
title: Fonctions d’expression de la fonctionnalité de flux de données de mappage d’Azure Data Factory
description: Découvrez les fonctions d’expression du flux de données de mappage.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 72c516f0a6e377cc16205917967482a29b4fdfbd
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036212"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Expressions de transformation de données dans le flux de données de mappage 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>Fonctions d’expression

Dans Data Factory, utilisez le langage d’expression de la fonctionnalité de flux de données de mappage pour configurer les transformations de données.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Module positivement une paire de nombres.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur inverse de cosinus * ``acos(1) -> 0.0``
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Ajoute une paire de chaînes ou de nombres. Ajoute une date à un nombre de jours. Ajoute un tableau à un autre de type similaire. Identique à l’opérateur + * ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Ajoute des jours à une date ou à un horodatage. Identique à l’opérateur + pour la date * ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Ajoute des mois à une date ou à un horodatage * ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Opérateur logique AND. Identique à && * ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de sinus inverse * ``asin(0) -> 0.0``
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de tangente inverse * ``atan(0) -> 0.0``
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Retourne l’angle (en radians) qui se trouve entre l’axe positif des abscisses d’un plan et le point donné par les coordonnées * ``atan2(0, 0) -> 0.0``
___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtient la moyenne des valeurs d’une colonne * ``avg(sales) -> 7523420.234``
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Selon certains critères, obtient la moyenne des valeurs d’une colonne * ``avgIf(region == 'West', sales) -> 7523420.234``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Sélectionne une valeur de colonne selon le nom dans le flux de données. S’il existe plusieurs correspondances, la première correspondance est retournée. S’il n’existe aucune correspondance, une valeur Null est retournée. Le type de la valeur retournée doit être converti par l’une des fonctions de conversion de type (TO_DATE, TO_STRING, etc.).  Les noms de colonne connus au moment de la conception doivent être traités uniquement selon leur nom. Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser les substitutions de paramètre * ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Sélectionne une valeur de colonne selon sa position relative (base 1) dans le flux. Si la position est hors limites, une valeur Null est retournée. Le type de la valeur retournée doit être converti par l’une des fonctions de conversion de type (TO_DATE, TO_STRING, etc.) Les entrées calculées ne sont pas prises en charge, mais vous pouvez utiliser les substitutions de paramètre * ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Selon des conditions alternantes, applique une valeur ou une autre. Si les entrées sont en nombre égal, l’autre est Null pour la dernière condition * ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule la racine cubique d’un nombre * ``cbrt(8) -> 2.0``
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Retourne le plus petit entier qui n’est pas inférieur au nombre * ``ceil(-0.1) -> 0``
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Renvoie la première valeur non nulle d'un jeu d'entrées. Toutes les entrées doivent être du même type * ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Compare deux valeurs du même type. Renvoie un entier négatif si valeur1 < valeur2, 0 si valeur1 == valeur2, une valeur positive si valeur1 > valeur2 * ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatène un nombre variable de chaînes. Identique à l’opérateur + avec des chaînes * ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatène un nombre variable de chaînes avec un séparateur. Le premier paramètre est le séparateur * ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de cosinus * ``cos(10) -> -0.83907152907``
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule le cosinus hyperbolique d’une valeur * ``cosh(0) -> 1.0``
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Obtient le nombre agrégé de valeurs. Si la ou les colonnes facultatives sont spécifiées, les valeurs Null ne sont pas comptabilisées * ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Obtient la somme agrégée de valeurs distinctes appartenant à un ensemble de colonnes * ``countDistinct(custId, custName) -> 60``
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Selon certains critères, obtient le nombre agrégé de valeurs. Si la colonne facultative est spécifiée, les valeurs Null ne sont pas comptabilisées * ``countIf(state == 'CA' && commission < 10000, name) -> 100``
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Obtient la covariance de remplissage entre deux colonnes * ``covariancePopulation(sales, profit) -> 122.12``
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient la covariance de remplissage de deux colonnes * ``covariancePopulationIf(region == 'West', sales) -> 122.12``
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Obtient l’exemple de covariance de deux colonnes * ``covarianceSample(sales, profit) -> 122.12``
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient l’exemple de covariance de deux colonnes * ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Calcule le code de hachage CRC32 de l’ensemble de colonnes comprenant différents types de données primitives à partir d’une longueur en bits, qui peut uniquement être égale aux valeurs 0(256), 224, 256, 384, 512. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
La fonction CumeDist calcule la position d’une valeur par rapport à toutes les valeurs de la partition. Le résultat correspond au nombre de lignes précédant ou correspondant à la ligne actuelle dans l’ordre de la partition, divisé par le nombre total de lignes de la partition de fenêtre. Toutes les valeurs égales auront la même position.
* ``cumeDist() -> 1``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Obtient la date à laquelle l’exécution du travail commence. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut.
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Obtient l’horodatage du début de l’exécution du travail avec le fuseau horaire local * ``currentTimestamp() -> 12-12-2030T12:12:12``
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Obtient l’horodatage actuel au format UTC. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. La valeur par défaut est le fuseau horaire actuel * ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtient le jour du mois dans une date * ``dayOfMonth(toDate('2018-06-08')) -> 08``
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtient le jour de la semaine dans une date. 1 - dimanche, 2 - lundi..., 7 - samedi * ``dayOfWeek(toDate('2018-06-08')) -> 7``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtient le jour de l’année dans une date * ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Convertit des radians en degrés * ``degrees(3.141592653589793) -> 180``
___
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Calcule le rang d’une valeur dans un groupe de valeurs. Le résultat est égal à 1 plus le nombre de lignes précédant ou correspondant à la ligne actuelle dans l’ordre de la partition. Les valeurs ne vont pas produire d’écarts dans la séquence. Le rang Dense fonctionne même lorsque les données ne sont pas triées, et il recherche les valeurs qui ont été modifiées * ``denseRank(salesQtr, salesAmt) -> 1``
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Divise une paire de nombres. Identique à l’opérateur / * ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Vérifie si la chaîne se termine par la chaîne fournie * ``endsWith('great', 'eat') -> true``
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Opérateur de comparaison « Est égal à » Identique à l’opérateur == * ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Opérateur de comparaison « Est égal à » qui ignore la casse. Identique à l’opérateur <=> * ``'abc'<==>'abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Calcule la factorielle d’un nombre * ``factorial(5) -> 120``
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Retourne toujours une valeur false. Utilisez la fonction syntax(false()) s’il existe une colonne nommée 'false' * ``isDiscounted == false()``
* ``isDiscounted() == false``
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Obtient la première valeur d’un groupe de colonnes. Si le deuxième paramètre ignoreNulls est omis, il est supposé que sa valeur est false * ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Retourne le plus grand entier qui n’est pas supérieur au nombre * ``floor(-0.1) -> -1``
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Convertit vers l’horodatage à partir du temps universel coordonné. Vous pouvez, si vous le souhaitez, passer un fuseau horaire au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. La valeur par défaut est le fuseau horaire actuel * ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Opérateur de comparaison « Supérieur à » Identique à l’opérateur > * ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Opérateur de comparaison « Supérieur ou égal à ». Identique à l’opérateur >= * ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Retourne la valeur la plus élevée parmi celles de la liste des valeurs d’entrée. Retourne la valeur Null si toutes les entrées sont Null * ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtient la valeur d’heure d’un horodatage. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut.
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Selon la condition, applique une valeur ou une autre. Si l’autre valeur n’est pas spécifiée, elle est considérée comme Null. Les deux valeurs doivent être compatibles (numérique, chaîne...) * ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Vérifie si un élément se trouve dans le tableau * ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Convertit la première lettre de chaque mot en majuscules. Les mots sont identifiés comme étant séparés par des espaces blancs * ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Recherche la position (de base 1) de la sous-chaîne au sein d’une chaîne. Si elle est introuvable, 0 est retourné * ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne est marquée pour la suppression. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. La valeur par défaut pour l’index de flux est 1 * ``isDelete() -> true``
* ``isDelete(1) -> false``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne est marquée comme comprenant une erreur. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. La valeur par défaut pour l’index de flux est 1 * ``isError() -> true``
* ``isError(1) -> false``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne est marquée comme devant être ignorée. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. La valeur par défaut pour l’index de flux est 1 * ``isIgnore() -> true``
* ``isIgnore(1) -> false``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne est marquée pour l’insertion. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. La valeur par défaut pour l’index de flux est 1 * ``isInsert() -> true``
* ``isInsert(1) -> false``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne correspond à la recherche. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. La valeur par défaut pour l’index de flux est 1 * ``isMatch() -> true``
* ``isMatch(1) -> false``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Vérifie si la valeur est Null * ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Vérifie si la ligne est marquée pour la mise à jour. Pour les transformations qui acceptent plusieurs flux d’entrée, vous pouvez passer l’index (de base 1) du flux. La valeur par défaut pour l’index de flux est 1 * ``isUpdate() -> true``
* ``isUpdate(1) -> false``
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient l’aplatissement d’une colonne * ``kurtosis(sales) -> 122.12``
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient l’aplatissement d’une colonne * ``kurtosisIf(region == 'West', sales) -> 122.12``
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Obtient la valeur des n lignes évaluées dans le premier paramètre avant la ligne actuelle. Le deuxième paramètre correspond au nombre de lignes précédentes à examiner et la valeur par défaut est 1. S’il n’y a pas autant de lignes, la valeur Null est retournée, sauf si une valeur par défaut est spécifiée. * ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Obtient la dernière valeur d’un groupe de colonnes. Si le deuxième paramètre ignoreNulls est omis, il est supposé que sa valeur est false * ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Obtient le dernier jour du mois dans une date * ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Obtient la valeur des n lignes évaluées dans le premier paramètre après la ligne actuelle. Le deuxième paramètre correspond au nombre de lignes suivantes à examiner et la valeur par défaut est 1. S’il n’y a pas autant de lignes, la valeur Null est retournée, sauf si une valeur par défaut est spécifiée. * ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Opérateur de comparaison « Inférieur ou égal à ». Identique à l’opérateur <= * ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrait une sous-chaîne comportant un certain nombre de caractères en partant de la gauche d’une chaîne au niveau de l’index 1. Identique à SUBSTRING(str, 1, n) * ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Retourne la longueur de la chaîne * ``length('kiddo') -> 5``
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Opérateur de comparaison « Inférieur à » Identique à l’opérateur < * ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Opérateur de comparaison « Inférieur ou égal à ». Identique à l’opérateur <= * ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Obtient la distance levenshtein entre deux chaînes * ``levenshtein('boys', 'girls') -> 4``
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Le modèle est une chaîne qui est mise en correspondance littéralement. Les symboles spéciaux suivants représentent des exceptions : _ correspond à tout caractère de l’entrée (semblable à . dans les expressions régulières posix) ; « % » correspond à zéro, un ou plusieurs caractères de l’entrée (comme les caractères « .* » dans les expressions régulières posix).
Le caractère d’échappement est « '' ». Si un caractère d’échappement précède un caractère spécial ou un autre caractère d’échappement, le caractère qui suit a une correspondance littérale. Aucun autre caractère ne peut être échappé.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Recherche la position (de base 1) de la sous-chaîne au sein d’une chaîne, en partant d’une certaine position. Si la position est omise, on considère qu’elle est située au début de la chaîne. Si elle est introuvable, 0 est retourné * ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Calcule la valeur de journal. Une base facultative peut être fournie, voire un nombre Euler, le cas échéant * ``log(100, 10) -> 2``
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule la valeur de journal sur une base 10 * ``log10(100) -> 2``
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Convertit une chaîne en minuscules * ``lower('GunChus') -> 'gunchus'``
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Décale la chaîne sur la gauche selon la marge intérieure fournie, jusqu’à ce qu’elle atteigne une certaine longueur. Si la chaîne est supérieure ou égale à cette longueur, elle est considérée comme un no-op * ``lpad('great', 10, '-') -> '___--great'``
* ``lpad('great', 4, '-') -> 'great'``
* ``lpad('great', 8, '<>') -> '<><great'``
___
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Supprime une chaîne de caractères de début situés sur la gauche. Si le deuxième paramètre n’est pas spécifié, cela supprime l’espace blanc. Sinon, cela supprime tout caractère spécifié dans le deuxième paramètre * ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Obtient la valeur maximale d’une colonne * ``MAX(sales) -> 12312131.12``
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Selon certains critères, obtient la valeur maximale d’une colonne * ``maxIf(region == 'West', sales) -> 99999.56``
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcule le code de hachage MD5 de l’ensemble de colonnes comprenant différents types de données primitives, et retourne une chaîne hexadécimale de 32 caractères. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtient la moyenne des valeurs d’une colonne. Identique à AVG * ``mean(sales) -> 7523420.234``
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Selon certains critères, obtient la moyenne des valeurs d’une colonne. Identique à avgIf * ``meanIf(region == 'West', sales) -> 7523420.234``
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtient la valeur en millisecondes d'une date. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut.
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Obtient la valeur minimale d’une colonne * ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Selon certains critères, obtient la valeur minimale d’une colonne * ``minIf(region == 'West', sales) -> 00.01``
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Soustrait des nombres. Soustrait un nombre de jours à partir d’une date. Identique à l’opérateur - * ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtient la valeur de minute d’un horodatage. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut.
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Module une paire de nombres. Identique à l’opérateur % * ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtient la valeur de mois d’une date ou d’un horodatage * ``month(toDate('2012-8-8')) -> 8``
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
Obtient le nombre de mois entre deux dates. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut.
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Multiplie une paire de nombres. Identique à l’opérateur * * ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
La fonction NTile divise les lignes de chaque partition de fenêtre en compartiments `n`,compris entre 1 et `n` au maximum. Les valeurs de compartiment varient de 1 au maximum. Si les lignes de la partition ne sont pas réparties de façon uniforme dans les compartiments, les valeurs restantes sont distribuées une par une dans chaque compartiment, en commençant par le premier compartiment. La fonction NTile est utile pour le calcul des tertiles, quartiles, déciles et autres statistiques de résumé courantes. La fonction calcule deux variables pendant l’initialisation : Aucune ligne supplémentaire ne sera ajoutée à la taille d’un compartiment habituel. Les deux variables sont basées sur la taille de la partition actuelle. Pendant le processus de calcul, la fonction effectue le suivi du nombre actuel de lignes, du nombre actuel de compartiments et du nombre de lignes où le compartiment va changer (bucketThreshold). Lorsque le nombre actuel de lignes atteint le seuil du compartiment, la valeur du compartiment est augmentée de 1 et le seuil est augmenté par la taille du compartiment (plus 1, si le compartiment actuel est rempli).
* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Inverse un nombre. Transforme les nombres positifs en nombres négatifs, et vice versa * ``negate(13) -> -13``
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Retourne la séquence unique suivante. Le nombre est consécutif uniquement au sein d’une partition et il est préfixé de partitionId * ``nextSequence() -> 12313112``
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalisez la valeur de chaîne pour séparer les caractères Unicode accentués * ``normalize('boys') -> 'boys'``
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Opérateur de négation logique * ``not(true) -> false``
* ``not(premium)``
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Opérateur de comparaison « N'est pas égal à » Identique à l’opérateur != * ``12!=24 -> true``
* ``'abc'!='abc' -> false``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Retourne une valeur NULL. Utilisez la fonction syntax(null()) s’il existe une colonne nommée 'null'. Toute opération qui l’utilise entraînera une valeur Null * ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Opérateur logique OR. Identique à || * ``or(true, false) -> true``
* ``true || false -> true``
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Module positivement une paire de nombres.
* ``pmod(-20, 8) -> 4``
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Élève un nombre à la puissance d’un autre nombre * ``power(10, 2) -> 100``
___
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Calcule le rang d’une valeur dans un groupe de valeurs. Le résultat est égal à 1 plus le nombre de lignes précédant ou correspondant à la ligne actuelle dans l’ordre de la partition. Les valeurs vont produire des écarts dans la séquence. Le rang fonctionne même lorsque les données ne sont pas triées, et il recherche les valeurs qui ont été modifiées * ``rank(salesQtr, salesAmt) -> 1``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Extrait une sous-chaîne correspondant à un modèle d’expression régulière donné. Le dernier paramètre identifie le groupe de correspondance. S’il est omis, la valeur par défaut est de 1. Utilisez `<regex>` (apostrophe ouvrante) pour rechercher une chaîne sans séquence d’échappement * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Vérifie si la chaîne correspond au modèle d’expression régulière donné. Utilisez `<regex>` (apostrophe ouvrante) pour rechercher une chaîne sans séquence d’échappement * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Remplace toutes les occurrences d’un modèle d’expression régulière par une autre sous-chaîne d’une chaîne donnée. Utilisez `<regex>` (apostrophe ouvrante) pour rechercher une chaîne sans séquence d’échappement * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Fractionne une chaîne selon un délimiteur basé sur une expression régulière et retourne un tableau de chaînes * ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Remplace toutes les occurrences d’une sous-chaîne par une autre sous-chaîne dans une chaîne donnée * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Inverse une chaîne * ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrait une sous-chaîne comportant un certain nombre de caractères en partant de la droite. Identique à SUBSTRING(str, LENGTH(str) - n, n) * ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Vérifie si la chaîne correspond au modèle d’expression régulière donné * ``rlike('200.50', '(\d+).(\d+)') -> true``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Arrondit un nombre donné selon une mise à l’échelle et un mode d’arrondi facultatifs. Si la mise à l’échelle est omise, la valeur par défaut 0 est utilisée.  Si le mode est omis, la valeur par défaut ROUND_HALF_UP(5) est utilisée. Les valeurs d’arrondi incluent 1 - ROUND_UP 2 - ROUND_DOWN 3 - ROUND_CEILING 4 - ROUND_FLOOR 5 - ROUND_HALF_UP 6 - ROUND_HALF_DOWN 7 - ROUND_HALF_EVEN 8 - ROUND_UNNECESSARY * ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Attribue un numérotage séquentiel aux lignes d’une fenêtre en commençant par 1 * ``rowNumber() -> 1``
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Décale la chaîne sur la droite selon la marge intérieure fournie, jusqu’à ce qu’elle atteigne une certaine longueur. Si la chaîne est supérieure ou égale à cette longueur, elle est considérée comme un no-op * ``rpad('great', 10, '-') -> 'great___--'``
* ``rpad('great', 4, '-') -> 'great'``
* ``rpad('great', 8, '<>') -> 'great<><'``
___
### <code>rtrim</code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Supprime une chaîne de caractères de début situés sur la droite. Si le deuxième paramètre n’est pas spécifié, cela supprime l’espace blanc. Sinon, cela supprime tout caractère spécifié dans le deuxième paramètre * ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtient la deuxième valeur d’une date. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. Le fuseau horaire local est utilisé par défaut.
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcule le code de hachage SHA-1 de l’ensemble de colonnes comprenant différents types de données primitives, et retourne une chaîne hexadécimale de 40 caractères. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcule le code de hachage SHA-2 de l’ensemble de colonnes comprenant différents types de données primitives à partir d’une longueur en bits, qui peut uniquement être égale aux valeurs 0(256), 224, 256, 384, 512. Il peut être utilisé dans le but de calculer une empreinte digitale pour une ligne * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de sinus * ``sin(2) -> 0.90929742682``
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de sinus hyperbolique * ``sinh(0) -> 0.0``
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient l’asymétrie d’une colonne * ``skewness(sales) -> 122.12``
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient l’asymétrie d’une colonne * ``skewnessIf(region == 'West', sales) -> 122.12``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extrait un sous-ensemble d’un tableau en partant d’une position. La position est de base 1. Si la longueur est omise, elle est définie par défaut sur la fin de la chaîne * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Obtient le code soundex de la chaîne * ``soundex('genius') -> 'G520'``
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Fractionne une chaîne selon un délimiteur et retourne un tableau de chaînes * ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule la racine carrée d’un nombre * ``sqrt(9) -> 3``
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Vérifie si la chaîne commence par la chaîne fournie * ``startsWith('great', 'gr') -> true``
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient l’écart type d’une colonne * ``stdDev(sales) -> 122.12``
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient l’écart type d’une colonne * ``stddevIf(region == 'West', sales) -> 122.12``
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient l’écart type de remplissage d’une colonne * ``stddevPopulation(sales) -> 122.12``
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient l’écart type de remplissage d’une colonne * ``stddevPopulationIf(region == 'West', sales) -> 122.12``
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient l’exemple d’écart type d’une colonne * ``stddevSample(sales) -> 122.12``
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient l’exemple d’écart type d’une colonne * ``stddevSampleIf(region == 'West', sales) -> 122.12``
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Soustrait des mois à partir d’une date. Identique à l’opérateur - pour la date * ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Soustrait des mois à partir d’une date ou d’un horodatage * ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extrait une sous-chaîne d’une certaine longueur en partant d’une position. La position est de base 1. Si la longueur est omise, elle est définie par défaut sur la fin de la chaîne * ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtient la somme agrégée d’une colonne numérique * ``sum(col) -> value``
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtient la somme agrégée de valeurs distinctes appartenant à une colonne numérique * ``sumDistinct(col) -> value``
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Selon certains critères, obtient la somme agrégée d’une colonne numérique. La condition peut être basée sur n’importe quelle colonne * ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Selon certains critères, obtient la somme agrégée d’une colonne numérique. La condition peut être basée sur n’importe quelle colonne * ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de tangente * ``tan(0) -> 0.0``
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcule une valeur de tangente hyperbolique * ``tanh(0) -> 0.0``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Convertit ('t', 'true', 'y', 'yes', '1') en valeur true, ('f', 'false', 'n', 'no', '0') en valeur false et toute autre valeur en valeur Null * ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Convertit une chaîne en une date selon un format de date facultatif. Pour connaître tous les formats possibles, reportez-vous à Java SimpleDateFormat. Si le format de date est omis, les combinaisons suivantes sont acceptées. [ aaaa, aaaa-[M]M, aaaa-[M]M-[j]j, aaaa-[M]M-[j]j, aaaa-[M]M-[j]j, aaaa-[M]M-[j]jT* ] * ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur de type decimal. Si la précision et l’échelle ne sont pas spécifiées, la valeur utilisée par défaut est (10,2). Un format décimal Java facultatif peut être utilisé pour la conversion. Un format de paramètres régionaux facultatif sous forme de langue BCP47, par exemple en-US, de, zh-CN * ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur de type double. Un format décimal Java facultatif peut être utilisé pour la conversion. Un format de paramètres régionaux facultatif sous forme de langue BCP47, par exemple en-US, de, zh-CN * ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur de type float. Un format décimal Java facultatif peut être utilisé pour la conversion. Tronque toutes les valeurs de type double * ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur entière. Un format décimal Java facultatif peut être utilisé pour la conversion. Tronque toutes les valeurs de type long, float ou double * ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur de type long. Un format décimal Java facultatif peut être utilisé pour la conversion. Tronque toutes les valeurs de type float ou double * ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Convertit une valeur numérique ou une chaîne en une valeur de type short. Un format décimal Java facultatif peut être utilisé pour la conversion. Tronque toutes les valeurs de type integer, long, float ou double * ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Convertit un type de données primitif en une chaîne. Vous pouvez spécifier un format pour les nombres et les dates. Si aucun format n’est spécifié, la valeur système par défaut est utilisée. Le format décimal Java est utilisé pour les nombres. Pour connaître tous les formats de date possibles, reportez-vous à Java SimpleDateFormat. Le format par défaut est aaaa-MM-jj * ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Convertit une chaîne en une date selon un format d’horodatage facultatif. Pour connaître tous les formats possibles, reportez-vous à Java SimpleDateFormat. Si l’horodatage est omis, le modèle par défaut est utilisé : aaaa-[M]M-[j]j hh:mm:ss[.f...] * ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Convertit l’horodatage au format UTC. Vous pouvez passer un fuseau horaire facultatif au format 'GMT', 'PST', 'UTC', 'Amérique/Caïmans'. La valeur par défaut est le fuseau horaire actuel * ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Remplace un jeu de caractères par un autre jeu de caractères dans la chaîne. Chaque caractère est remplacé par un seul caractère * ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Supprime une chaîne de caractères de début et de fin. Si le deuxième paramètre n’est pas spécifié, cela supprime l’espace blanc. Sinon, cela supprime tout caractère spécifié dans le deuxième paramètre * ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Retourne toujours une valeur true. Utilisez la fonction syntax(true()) s’il existe une colonne nommée 'true' * ``isDiscounted == true()``
* ``isDiscounted() == true``
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Correspond au type de la colonne. Peut uniquement être utilisé dans les expressions de modèle. « number » peut être de type short, integer, long, double, float ou decimal ; « integral » peut être de type short, integer ou long ; « fractional » peut être de type double, float ou decimal et « datetime » peut être de type date ou timestamp* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Convertit une chaîne en majuscules * ``upper('bojjus') -> 'BOJJUS'``
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient la variance d’une colonne * ``variance(sales) -> 122.12``
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient la variance d’une colonne * ``varianceIf(region == 'West', sales) -> 122.12``
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient la variance de remplissage d’une colonne * ``variancePopulation(sales) -> 122.12``
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient la variance de remplissage d’une colonne * ``variancePopulationIf(region == 'West', sales) -> 122.12``
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtient la variance non biaisée d’une colonne * ``varianceSample(sales) -> 122.12``
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Selon certains critères, obtient la variance non biaisée d’une colonne * ``varianceSampleIf(region == 'West', sales) -> 122.12``
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtient la semaine de l’année dans une date * ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Opérateur logique XOR. Identique à l’opérateur ^ * ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtient la valeur d’année d’une date * ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment utiliser le Générateur d’expressions](concepts-data-flow-expression-builder.md).
