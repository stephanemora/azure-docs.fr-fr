---
title: Fonctions JavaScript définies par l’utilisateur Azure Stream Analytics
description: Dans ce didacticiel, vous effectuez des requêtes avancées avec les fonctions JavaScript définies par l’utilisateur
author: rodrigoamicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 04/01/2018
ms.openlocfilehash: feb0361b460f5b18b5a8aaa585332e2179023458
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851168"
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>Tutoriel : Fonctions JavaScript définies par l’utilisateur Azure Stream Analytics
 
Azure Stream Analytics prend en charge les fonctions définies par l’utilisateur écrites en JavaScript. Avec le large éventail de méthodes **String**, **RegExp**, **Math**, **Array** et **Date** que fournit JavaScript, les transformations complexes de données des travaux Stream Analytics sont plus faciles à créer.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Définir une fonction JavaScript définie par l’utilisateur
> * Ajouter la fonction dans le portail
> * Définir une requête qui exécute la fonction

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="javascript-user-defined-functions"></a>Fonctions JavaScript définies par l’utilisateur
Les fonctions JavaScript définies par l’utilisateur prennent en charge les fonctions scalaires de calcul pur sans état qui ne nécessitent pas de connectivité externe. La valeur renvoyée par une fonction ne peut être qu’une valeur (unique) scalaire. Une fois que vous avez ajouté une fonction JavaScript définie par l’utilisateur à un travail, vous pouvez utiliser la fonction n’importe où dans la requête, comme une fonction scalaire intégrée.

Voici quelques scénarios dans lesquels les fonctions JavaScript définies par l’utilisateur vous seront utiles :
* Analyse et manipulation de chaînes avec des fonctions d’expressions régulières, par exemple **Regexp_Replace()** et **Regexp_Extract()**
* Décodage de données encodées, par exemple la conversion de binaire en hexadécimal
* Calculs mathématiques avec les fonctions **Math** de JavaScript
* Opérations de tableaux comme le tri, la jointure, la recherche et le remplissage

Voici quelques actions que vous ne pouvez pas effectuer avec une fonction JavaScript définie par l’utilisateur dans Stream Analytics :
* Appel de points de terminaison REST externes, par exemple la recherche inversée d’une adresse IP ou l’extraction de données de référence à partir d’une source externe
* Sérialisation ou désérialisation à un format d’événement personnalisé sur les entrées/sorties
* Création d’agrégats personnalisés

Bien qu’elles ne soient pas bloquées dans la définition des fonctions, évitez d’utiliser des fonctions telles que **Date.GetDate()** ou **Math.random()** . Ces fonctions ne retournent **pas** le même résultat à chaque fois que vous les appelez et le service Azure Stream Analytics ne conserve pas de journal des appels de fonction et des résultats retournés. Si une fonction retourne un résultat différent sur les mêmes événements, la reproductibilité n’est pas garantie lorsqu’un travail est relancé par vos soins ou par le service Stream Analytics.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Ajouter une fonction JavaScript définie par l’utilisateur dans le portail Azure
Pour créer une fonction JavaScript simple définie par l’utilisateur dans un travail Stream Analytics existant, suivez ces étapes :

> [!NOTE]
> Ces étapes fonctionnent sur les travaux Stream Analytics configurés pour s’exécuter dans le cloud. Si votre travail Stream Analytics est configuré pour s’exécuter sur Azure IoT Edge, utilisez plutôt Visual Studio et [écrivez la fonction définie par l’utilisateur en C#](stream-analytics-edge-csharp-udf.md).

1.  Dans le portail Azure, recherchez votre travail Stream Analytics.

2. Sous l’en-tête **Topologie de la tâche**, sélectionnez **Fonctions**. Une liste vide de fonctions apparaît.

3.  Pour créer une fonction définie par l’utilisateur, sélectionnez **+Ajouter**.

4.  Dans le panneau **Nouvelle fonction**, pour **Type de fonction**, sélectionnez **JavaScript**. Un modèle de fonction par défaut apparaît dans l’éditeur.

5.  Pour l’**alias de fonction définie par l’utilisateur**, entrez **hex2Int**, puis modifiez l’implémentation de la fonction de la façon suivante :

    ```javascript
    // Convert Hex value to integer.
    function hex2Int(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Sélectionnez **Enregistrer**. La fonction apparaît dans la liste des fonctions.
7.  Sélectionnez la nouvelle fonction **hex2Int** pour vérifier sa définition. Toutes les fonctions ont un préfixe **UDF** ajouté à l’alias de la fonction. Vous devez *inclure le préfixe* quand vous appelez la fonction dans votre requête Stream Analytics. Dans ce cas, vous appelez **UDF.hex2Int**.

## <a name="testing-javascript-udfs"></a>Test de fonctions définies par l’utilisateur (UDF) JavaScript 
Vous pouvez tester et déboguer votre logique de fonctions UDF JavaScript dans n’importe quel navigateur. Le débogage et le test de la logique de ces fonctions définies par l’utilisateur ne sont actuellement pas pris en charge dans le portail Stream Analytics. Une fois que la fonction fonctionne comme prévu, vous pouvez l’ajouter au travail Stream Analytics comme indiqué ci-dessus, puis l’appeler directement à partir de votre requête.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Appeler une fonction JavaScript définie par l’utilisateur dans une requête

1. Dans l’éditeur de requêtes, sous l’en-tête **Topologie de la tâche**, sélectionnez **Requête**.
2.  Modifiez votre requête, puis appelez la fonction définie par l’utilisateur comme suit :

    ```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Pour charger l’exemple de fichier de données, cliquez avec le bouton droit sur l’entrée du travail.
4.  Pour tester votre requête, sélectionnez **Test**.


## <a name="supported-javascript-objects"></a>Objets JavaScript pris en charge
Les fonctions JavaScript définies par l’utilisateur Azure Stream Analytics prennent en charge les objets prédéfinis standard du langage JavaScript. Pour obtenir la liste de ces objets, consultez [Objets globaux](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversion de type Stream Analytics et JavaScript

Il existe des différences entre les types pris en charge dans le langage de requête Stream Analytics et JavaScript. Le tableau suivant répertorie les mappages de conversion entre les deux :

Stream Analytics | JavaScript
--- | ---
bigint | Number (JavaScript ne peut représenter les entiers que jusqu’à 2^53 précisément)
DateTime | Date (JavaScript ne prend en charge que les millisecondes)
double | Number
nvarchar(MAX) | String
Enregistrement | Object
Array | Array
NULL | Null


Voici les conversions de JavaScript à Stream Analytics :


JavaScript | Stream Analytics
--- | ---
Number | Bigint (si le nombre est rond et entre long.MinValue et long.MaxValue, sinon, double)
Date | DateTime
String | nvarchar(MAX)
Object | Enregistrement
Array | Array
Null, Undefined | NULL
Un autre type (par exemple une fonction ou une erreur) | Non pris en charge (entraîne une erreur d’exécution)

Le langage JavaScript respecte la casse, et la casse des champs d’objet dans le code JavaScript doit correspondre à la casse des champs dans les données entrantes. Veuillez noter que les tâches avec le niveau de compatibilité 1.0 convertiront les champs de l’instruction SQL SELECT en minuscules. À partir de la compatibilité 1.1 et dans les versions ultérieures, les champs de l’instruction SELECT auront la même casse que celle spécifiée dans la requête SQL.

## <a name="troubleshooting"></a>Dépannage
Les erreurs d’exécution JavaScript sont considérées comme irrécupérables et remontées par le biais du journal d’activité. Pour récupérer le journal, dans le portail Azure, accédez à votre travail et sélectionnez **Journal d’activité**.

## <a name="other-javascript-user-defined-function-patterns"></a>Autres modèles de fonctions JavaScript définies par l’utilisateur

### <a name="write-nested-json-to-output"></a>Écrire du code JSON imbriqué en sortie
Si vous avez une étape de traitement de suivi qui prend la sortie du travail Stream Analytics en entrée et qu’un format JSON est requis, vous pouvez écrire une chaîne JSON comme sortie. L’exemple suivant appelle la fonction **JSON.stringify()** pour compresser toutes les paires nom/valeur de l’entrée, puis les écrire sous la forme d’une valeur de chaîne unique en sortie.

**Définition de la fonction JavaScript définie par l’utilisateur :**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Exemple de requête :**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, le travail de streaming et toutes les ressources associées. La suppression du travail évite la facturation des unités de streaming consommées par le travail. Si vous envisagez d’utiliser le travail à l’avenir, vous pouvez l’arrêter et le redémarrer plus tard lorsque vous en avez besoin. Si vous ne pensez pas continuer à utiliser ce travail, supprimez toutes les ressources créées pendant ce guide de démarrage rapide en procédant comme suit :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée.  
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide supplémentaire, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une tâche Stream Analytics qui exécute une fonction Java définie par l’utilisateur simple. Pour en savoir plus sur Stream Analytics, passez aux articles de scénario en temps réel :

> [!div class="nextstepaction"]
> [Analyse de sentiments Twitter en temps réel dans Azure Stream Analytics](stream-analytics-twitter-sentiment-analysis-trends.md)
