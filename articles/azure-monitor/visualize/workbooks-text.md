---
title: Paramètres texte des classeurs Azure Monitor
description: Créer des rapports complexes en toute simplicité grâce à des classeurs paramétrables prédéfinis et personnalisés. Apprenez-en davantage sur les paramètres de texte de classeur.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: e3beedff4b9d65f5f0b69b5c60bd67d4b134d096
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/04/2021
ms.locfileid: "113287515"
---
# <a name="workbook-text-parameters"></a>Paramètres texte de classeur

Les paramètres de zone de texte offrent un moyen simple de collecter des entrées de texte à partir des utilisateurs du classeur. Ils sont utilisés lorsqu’il n’est pas pratique d’utiliser un menu déroulant pour collecter l’entrée (par exemple, un seuil arbitraire ou des filtres génériques). Les classeurs permettent aux auteurs d’obtenir la valeur par défaut de la zone de texte à partir d’une requête. Cela permet d’expérimenter des scénarios intéressants, tels que la définition du seuil par défaut en fonction du p95 de la métrique.

Les zones de texte sont couramment utilisées en tant que variables internes par d’autres contrôles de classeur. Cela s’effectue en utilisant une requête pour les valeurs par défaut et en masquant le contrôle d’entrée en mode lecture. Par exemple, un utilisateur peut souhaiter qu’un seuil provienne d’une formule (et non d’un utilisateur), puis utiliser le seuil dans les requêtes suivantes.

## <a name="creating-a-text-parameter"></a>Création d’un paramètre de texte
1. Démarrez avec un classeur vide en mode d’édition.
2. Choisissez _Ajouter des paramètres_ dans les liens du classeur.
3. Cliquez sur le bouton bleu _Ajouter un paramètre_.
4. Dans le volet Nouveau paramètre qui apparaît, entrez :
    1. Nom du paramètre : `SlowRequestThreshold`
    2. Type de paramètre : `Text`
    3. Obligatoire : `checked`
    4. Récupérer des données à partir de : `None`
5. Choisissez « Enregistrer » dans la barre d’outils pour créer le paramètre.

    :::image type="content" source="./media/workbooks-text/text-create.png" alt-text="Capture d’écran représentant la création d’un paramètre de texte.":::

Voici à quoi ressemble le classeur en mode lecture.

:::image type="content" source="./media/workbooks-text/text-readmode.png" alt-text="Capture d’écran représentant un paramètre de texte en mode lecture." border="false":::

## <a name="parameter-field-style"></a>Style du champ de paramètre
Le paramètre de texte prend en charge le style de champ suivant :

- Standard : champ de texte à une seule ligne.

     :::image type="content" source="./media/workbooks-text/standard-text.png" alt-text="Capture d’écran montrant le champ de texte standard.":::

- Mot de passe : champ de mot de passe à une seule ligne. La valeur de mot de passe est masquée uniquement sur l’interface utilisateur lorsque l’utilisateur tape. La valeur est toujours entièrement accessible en tant que valeur de paramètre quand elle est référencée et est stockée non chiffrée lorsque le classeur est enregistré.

     :::image type="content" source="./media/workbooks-text/password-text.png" alt-text="Capture d’écran montrant le champ de mot de passe.":::

- Multiligne : champ de texte multiligne avec prise en charge de la fonction IntelliSense enrichie et de la colorisation de syntaxe pour les langages suivants :
    - Texte
    - Markdown
    - JSON
    - SQL
    - TypeScript
    - KQL
    - TOML

    L’utilisateur peut également spécifier la hauteur de l’éditeur multiligne.

     :::image type="content" source="./media/workbooks-text/kql-text.png" alt-text="Capture d’écran montrant le champ de texte multiligne.":::

## <a name="referencing-a-text-parameter"></a>Référencement d’un paramètre de texte
1. Ajoutez un contrôle de requête au classeur en sélectionnant le lien bleu `Add query`, puis sélectionnez une ressource Application Insights.
2. Dans la zone KQL, ajoutez l’extrait de code suivant :
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. En utilisant le paramètre text avec la valeur 500 associée au contrôle de requête, vous exécutez la requête ci-dessous :
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Exécuter la requête pour afficher les résultats

    :::image type="content" source="./media/workbooks-text/text-reference.png" alt-text="Capture d’écran présentant un paramètre de texte référencé dans KQL.":::

> [!NOTE]
> Dans l’exemple ci-dessus, `{SlowRequestThreshold}` représente une valeur entière. Si vous interrogez une chaîne comme `{ComputerName}`, vous devez modifier votre requête Kusto pour ajouter des guillemets `"{ComputerName}"` afin que le champ de paramètre accepte une entrée sans guillemets.

## <a name="setting-default-values-using-queries"></a>Définition de valeurs par défaut à l’aide de requêtes
1. Démarrez avec un classeur vide en mode d’édition.
2. Choisissez _Ajouter des paramètres_ dans les liens du classeur.
3. Cliquez sur le bouton bleu _Ajouter un paramètre_.
4. Dans le volet Nouveau paramètre qui apparaît, entrez :
    1. Nom du paramètre : `SlowRequestThreshold`
    2. Type de paramètre : `Text`
    3. Obligatoire : `checked`
    4. Récupérer des données à partir de : `Query`
5. Dans la zone KQL, ajoutez l’extrait de code suivant :
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Cette requête définit la valeur par défaut de la zone de texte sur la durée du 95e centile de toutes les requêtes dans l’application.
6. Exécuter la requête pour afficher le résultat
7. Choisissez « Enregistrer » dans la barre d’outils pour créer le paramètre.

    :::image type="content" source="./media/workbooks-text/text-default-value.png" alt-text="Capture d’écran représentant un paramètre de texte avec la valeur par défaut de KQL.":::

> [!NOTE]
> Bien que cet exemple interroge les données d’Application Insights, l’approche peut être utilisée pour n’importe quelle source de données basée sur les journaux - Log Analytics, Azure Resource Graph, etc.

## <a name="adding-validations"></a>Ajout de validations 

Pour les paramètres de texte standard et de mot de passe, l’utilisateur peut ajouter des règles de validation qui s’appliquent au champ de texte. Ajoutez une expression régulière valide avec un message d’erreur. Si le message est défini, il s’affiche en tant qu’erreur quand le champ n’est pas valide.

Si match est sélectionné, le champ est valide si value correspond à l’expression régulière et si match n’est pas sélectionné, le champ est valide s’il ne correspond pas à l’expression régulière.

:::image type="content" source="./media/workbooks-text/validation-settings.png" alt-text="Capture d’écran des paramètres de validation de texte.":::

## <a name="format-json-data"></a>Formater les données JSON 

Si JSON est sélectionné comme langue pour le champ de texte multiligne, ce dernier comporte un bouton qui met en forme les données JSON du champ. L’utilisateur peut également utiliser le raccourci `(ctrl + \)` pour formater les données JSON.

Si les données proviennent d’une requête, l’utilisateur peut sélectionner l’option permettant de préformater les données JSON retournées par la requête.

:::image type="content" source="./media/workbooks-text/pre-format-json-data.png" alt-text="Capture d’écran des données JSON au format antérieur.":::

## <a name="next-steps"></a>Étapes suivantes

* [Commencez](./workbooks-overview.md#visualizations) à en apprendre davantage sur les nombreuses options pour les visualisations enrichies des classeurs.
* [Contrôlez](./workbooks-access-control.md) et partagez l’accès à vos ressources de classeur.