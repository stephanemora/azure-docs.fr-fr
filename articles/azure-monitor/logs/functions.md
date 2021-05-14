---
title: Fonctions dans les requêtes de journal Azure Monitor
description: Cet article décrit l’utilisation des fonctions pour appeler une requête à partir d’une autre requête de journal dans Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/19/2021
ms.openlocfilehash: fecede1d582232ebc75878a687a24818031f0d80
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752850"
---
# <a name="functions-in-azure-monitor-log-queries"></a>Fonctions dans les requêtes de journal Azure Monitor
Une fonction est une requête de journal dans Azure Monitor qui peut être utilisée dans d’autres requêtes de journal comme s’il s’agissait d’une commande. Les fonctions permettent aux développeurs de fournir des solutions à différents clients et vous permettent à vous de réutiliser la logique de requête dans votre propre environnement. Cet article fournit des détails sur l’utilisation des fonctions et sur la création de vos propres fonctions.

## <a name="types-of-functions"></a>Types de fonctions
Il existe deux types de fonctions dans Azure Monitor :

- **Fonction de solution :** Fonctions prédéfinies incluses dans Azure Monitor. Celles-ci sont disponibles dans tous les espaces de travail Log Analytics et ne peuvent pas être modifiées.
- **Fonctions de l’espace de travail :** Fonctions installées dans un espace de travail Log Analytics particulier et pouvant être modifiées et contrôlées par l’utilisateur.

## <a name="viewing-functions"></a>Affichage des fonctions
Vous pouvez afficher les fonctions de la solution et les fonctions de l’espace de travail dans l’espace de travail actuel à partir de l’onglet **Fonctions** dans le volet gauche d’un espace de travail Log Analytics. Utilisez le bouton **Filtre** pour filtrer les fonctions incluses dans la liste et **Regrouper par** pour modifier leur regroupement. Entrez une chaîne dans la zone **Recherche** pour rechercher une fonction précise. Pointez sur une fonction pour en afficher les détails, notamment une description et des paramètres.

:::image type="content" source="media/functions/view-functions.png" alt-text="Afficher la fonction" lightbox="media/functions/view-functions.png":::

## <a name="use-a-function"></a>Utiliser une fonction
Utilisez une fonction dans une requête en tapant son nom avec des valeurs pour les paramètres de la même façon que vous le feriez dans une commande. La sortie de la fonction peut être retournée en tant que résultats ou redirigée vers une autre commande.

Ajoutez une fonction à la requête en cours en double-cliquant sur son nom ou en pointant dessus, puis en sélectionnant **Utiliser dans l’éditeur**. Les fonctions de l’espace de travail sont également incluses dans IntelliSense lorsque vous entrez une requête. 

Si une requête requiert des paramètres, fournissez-les à l’aide de la syntaxe : `function_name(param1,param2,...)`.

:::image type="content" source="media/functions/function-use.png" alt-text="Utiliser une fonction" lightbox="media/functions/function-use.png":::

## <a name="create-a-function"></a>Créer une fonction
Pour créer une fonction à partir de la requête actuelle dans l’éditeur, sélectionnez **Enregistrer**, puis **Enregistrer en tant que fonction**. 

:::image type="content" source="media/functions/function-save.png" alt-text="Créer une fonction" lightbox="media/functions/function-save.png":::

Pour créer une fonction avec Log Analytics sur le portail Azure, cliquez sur **Enregistrer**, puis fournissez les informations indiquées dans le tableau suivant.

| Paramètre | Description |
|:---|:---|
| Nom de fonction  | Nom de la fonction. Celle-ci ne peut pas inclure d’espace ni de caractères spéciaux. Elle ne peut pas non plus commencer par un trait de soulignement (_), car ce caractère est réservé aux fonctions de la solution. |
| Catégorie héritée | Catégorie définie par l’utilisateur pour aider à filtrer et à regrouper des fonctions.   |
| Enregistrer en tant que groupe d’ordinateurs | Enregistrer cette requête comme [groupe d'ordinateurs](computer-groups.md).  |
| Paramètres | Ajoutez un paramètre pour chaque variable de la fonction qui requiert une valeur lorsqu’elle est utilisée. Pour plus d’informations, consultez [Paramètres de fonction](#function-parameters). |

:::image type="content" source="media/functions/function-details.png" alt-text="Détails de la fonction" lightbox="media/functions/function-details.png":::

## <a name="function-parameters"></a>Paramètres de fonction 
Vous pouvez ajouter des paramètres à une fonction pour pouvoir fournir des valeurs pour certaines variables lors de son appel. Cela permet d’utiliser la même fonction dans des requêtes différentes, chacune fournissant des valeurs différentes pour les paramètres. Les paramètres sont définis par les propriétés suivantes.

| Paramètre | Description |
|:---|:---|
| Type  | Type de données pour la valeur. |
| Nom  | Nom du paramètre. Il s’agit du nom qui doit être utilisé dans la requête à remplacer par la valeur du paramètre.  |
| Valeur par défaut | Valeur à utiliser pour le paramètre si aucune valeur n’est fournie. |

Les paramètres sont ordonnés tels qu’ils sont créés avec tous les paramètres qui n’ont pas de valeur par défaut placés devant ceux qui ont une valeur par défaut.

## <a name="working-with-function-code"></a>Utiliser le code de fonction
Vous pouvez afficher le code d’une fonction pour avoir un aperçu de son fonctionnement ou pour modifier le code d’une fonction de l’espace de travail. Sélectionnez **Charger le code de la fonction** pour ajouter le code de la fonction à la requête actuelle dans l’éditeur. Si vous l’ajoutez à une requête vide ou à la première ligne d’une requête existante, le nom de la fonction est ajouté à l’onglet. S’il s’agit d’une fonction d’espace de travail, cette option permet de modifier les détails de la fonction.

:::image type="content" source="media/functions/function-code.png" alt-text="Charger le code de la fonction" lightbox="media/functions/function-code.png":::

## <a name="edit-a-function"></a>Modifier une fonction
Modifiez les propriétés ou le code d’une fonction en créant une nouvelle requête, puis pointez sur le nom de la fonction et sélectionnez **charger le code de la fonction**. Apportez les modifications souhaitées au code, sélectionnez **Enregistrer**, puis **Modifier les détails de la fonction**. Apportez les modifications souhaitées aux propriétés et aux paramètres de la fonction avant de cliquer sur **Enregistrer**.

:::image type="content" source="media/functions/function-edit.png" alt-text="Modifier la fonction" lightbox="media/functions/function-edit.png":::
## <a name="example"></a>Exemple
L’exemple de fonction suivant retourne tous les événements du journal d’activité Azure depuis une date précise et qui correspondent à une catégorie précise. 

Commencez par la requête suivante en utilisant des valeurs codées en dur. Cela vérifie que la requête fonctionne comme prévu.

```Kusto
AzureActivity
| where CategoryValue == "Administrative"
| where TimeGenerated > todatetime("2021/04/05 5:40:01.032 PM")
```

:::image type="content" source="media/functions/example-query.png" alt-text="Exemple de fonction - requête initiale" lightbox="media/functions/example-query.png":::

Ensuite, remplacez les valeurs codées en dur par des noms de paramètres, puis enregistrez la fonction en sélectionnant **Enregistrer**, puis **Enregistrer en tant que fonction**.

```Kusto
AzureActivity
| where CategoryValue == CategoryParam
| where TimeGenerated > DateParam
```

:::image type="content" source="media/functions/example-save-function.png" alt-text="Exemple de fonction - enregistrer la fonction" lightbox="media/functions/example-save-function.png":::

 Indiquez les valeurs suivantes pour les propriétés de la fonction :

| Propriété | Valeur |
|:---|:---|
| Nom de la fonction | AzureActivityByCategory |
| Catégorie héritée | Fonctions de démonstration |

Définissez les paramètres suivants avant d’enregistrer la fonction.

| Type | Nom | Valeur par défaut |
|:---|:---|:---|
| string   | CategoryParam | « Administratif » |
| DATETIME | DateParam     | |

:::image type="content" source="media/functions/example-function-properties.png" alt-text="Exemple de fonction - propriétés de la fonction" lightbox="media/functions/example-function-properties.png":::

Créez une nouvelle requête et affichez la nouvelle fonction en pointant dessus. Notez l’ordre des paramètres, car il s’agit de l’ordre dans lequel ils doivent être spécifiés lorsque vous utilisez la fonction.

:::image type="content" source="media/functions/example-view-details.png" alt-text="Exemple de fonction - afficher les détails" lightbox="media/functions/example-view-details.png":::

Sélectionnez **Utiliser dans l’éditeur** pour ajouter la nouvelle fonction à une requête, puis ajoutez des valeurs pour les paramètres. Notez que vous n’avez pas besoin de spécifier une valeur pour CategoryParam, car une valeur par défaut est présente.

:::image type="content" source="media/functions/example-use-function.png" alt-text="Exemple de fonction - utiliser la fonction" lightbox="media/functions/example-use-function.png":::



## <a name="next-steps"></a>Étapes suivantes
Reportez-vous à d'autres leçons pour écrire des requêtes de journal Azure Monitor :

- [Opérations de chaîne](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)

