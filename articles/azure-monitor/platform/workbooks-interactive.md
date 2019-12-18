---
title: Classeurs Azure Monitor dotés de paramètres personnalisés
description: Créer des rapports complexes en toute simplicité grâce à des classeurs paramétrables prédéfinis et personnalisés
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 19fd8c108e8075d30ca494ca75d52952849c284a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872840"
---
# <a name="interactive-workbooks"></a>Classeurs interactifs

Les classeurs permettent aux auteurs de créer des rapports et des expériences interactifs pour leurs consommateurs. L’interactivité est prise en charge de plusieurs façons.

## <a name="parameter-changes"></a>Modifications des paramètres
Lorsqu’un utilisateur du classeur met à jour un paramètre, tout contrôle qui utilise le paramètre est automatiquement actualisé et redessiné pour refléter le nouvel état. C’est ainsi que la plupart des rapports de Portail Azure prennent en charge l’interactivité. Les classeurs fournissent cela très simplement avec un effort utilisateur minimal.

En savoir plus sur [Paramètres dans Workbooks](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Clics sur la ligne de grille
Les classeurs permettent aux auteurs de construire des scénarios où le clic sur une ligne dans une grille met à jour les graphiques suivants en fonction du contenu de la ligne. 

Par exemple, un utilisateur peut disposer d’une grille qui affiche une liste de requêtes et certaines statistiques telles que le nombre d’échecs. Il pourrait la configurer de telle sorte qu’en cliquant sur une ligne correspondant à une requête, il obtiendrait des tableaux détaillés ci-dessous mis à jour afin de filtrer les données jusqu’à la définition de cette requête.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Configuration de l’interactivité lors d’un clic sur une ligne de grille
1. Basculez le classeur en mode édition en cliquant sur l’élément _Modifier_ de la barre d’outils.
2. Utilisez le lien _Ajouter une requête_ pour ajouter un contrôle de requête de journal au classeur. 
3. Sélectionnez le type de requête _Journal_, le type de ressource (par exemple, Application Insights) et les ressources à cibler.
4. Utiliser l’éditeur de requête pour saisir le KQL pour votre analyse
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query` pour afficher les résultats
6. Cliquez sur l’icône _Paramètres avancés_ dans le pied de page de la requête (l’icône ressemble à un engrenage). Le volet Paramètres avancés s’ouvre alors. 
7. Vérifier le paramètre : `When an item is selected, export a parameter`
    1. Champ à exporter : `Request`
    2. Nom du paramètre : `SelectedRequest`
    3. Valeur par défaut : `All requests`
    
    ![Image représentant l’éditeur avancé avec les paramètres d’exportation des champs en tant que paramètres](./media/workbooks-interactive/advanced-settings.png)

8. Cliquez sur `Done Editing`.
9. Ajoutez un autre contrôle de requête à l’aide des étapes 2 et 3.
10. Utiliser l’éditeur de requête pour saisir le KQL pour votre analyse
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query` pour afficher les résultats.
12. Placer _Visualization_ sur `Area chart`
12. Cliquez sur une ligne dans la première grille. Notez la manière dont le graphique en aires ci-dessous filtre la requête sélectionnée.

Le rapport résultant ressemble à ce qui suit en mode d’édition :

![Image représentant la création d’une expérience interactive à l’aide de clics sur la ligne de grille](./media/workbooks-interactive//grid-click-create.png)

L’image ci-dessous représente un rapport interactif plus élaboré en mode lecture basé sur les mêmes principes. Le rapport utilise des clics de grille pour exporter des paramètres, qui à son tour est utilisé dans deux graphiques et un bloc de texte.

![Image représentant la création d’une expérience interactive à l’aide de clics sur la ligne de grille](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Exportation du contenu d’une ligne entière
Il est parfois souhaitable d’exporter le contenu entier de la ligne sélectionnée au lieu d’une seule colonne particulière. Dans ce cas, laissez la propriété `Field to export` non définie à l’étape 7.1 ci-dessus. Les classeurs exportent le contenu de la ligne entière en tant que JSON vers le paramètre. 

Sur le contrôle KQL de référence, utilisez la fonction `todynamic` pour analyser le code JSON et accéder aux colonnes individuelles.

 ## <a name="grid-cell-clicks"></a>Clics de cellule de grille
Les classeurs permettent aux auteurs d’ajouter de l’interactivité via un type spécial de convertisseur de colonne de grille appelé `link renderer`. Un convertisseur de liens convertit une cellule de grille en lien hypertexte en fonction du contenu de la cellule. Les classeurs prennent en charge de nombreux types de convertisseurs de liens, y compris ceux qui permettent d’ouvrir les panneaux de vue d’ensemble des ressources, les visionneuses de conteneurs de propriétés, la recherche App Insights, l’utilisation, le suivi des transactions, etc.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Configuration de l’interactivité à l’aide de clics dans des cellules de grille
1. Basculez le classeur en mode édition en cliquant sur l’élément _Modifier_ de la barre d’outils.
2. Utilisez le lien _Ajouter une requête_ pour ajouter un contrôle de requête de journal au classeur. 
3. Sélectionnez le type de requête _Journal_, le type de ressource (par exemple, Application Insights) et les ressources à cibler.
4. Utiliser l’éditeur de requête pour saisir le KQL pour votre analyse
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query` pour afficher les résultats
6. Cliquez sur _Paramètres de colonne_ pour ouvrir le volet des paramètres.
7. Dans la section _Colonnes_, définissez :
    1. _Exemple_ - Renderer de colonne : `Link`, Affichage à ouvrir : `Cell Details`, Étiquette de lien : `Sample`
    2. _Nombre_ - Renderer de colonne : `Bar`, Palette de couleurs : `Blue`, Valeur minimale : `0`
    3. _Requête_ - Renderer de colonne : `Automatic`
    4. Cliquez sur _Enregistrer et fermer_ pour appliquer les modifications
8. Cliquez sur l’un des liens `Sample` dans la grille. Cela ouvre un volet de propriétés avec les détails d’une requête échantillonnée.

    ![Image représentant la création d’une expérience interactive à l’aide de clics sur la cellule de grille](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Actions de rendu de lien
| Action de lien | Action en cas de clic |
|:------------- |:-------------|
| `Generic Details` | Affiche les valeurs de ligne dans un panneau du contexte de la grille des propriétés |
| `Cell Details` | Affiche la valeur de la cellule dans un panneau du contexte de la grille des propriétés. Utile lorsque la cellule contient un type dynamique avec des informations (par exemple, JSON avec des propriétés de requête telles que l’emplacement, l’instance de rôle, etc.). |
| `Cell Details` | Affiche la valeur de la cellule dans un panneau du contexte de la grille des propriétés. Utile lorsque la cellule contient un type dynamique avec des informations (par exemple, JSON avec des propriétés de requête telles que l’emplacement, l’instance de rôle, etc.). |
| `Custom Event Details` | Ouvre les détails de la recherche Application Insights avec l’ID d’événement personnalisé (itemId) dans la cellule |
| `* Details` | Similaire à Détails d’événement personnalisés, sauf pour les dépendances, les exceptions, les affichages de page, les requêtes et les traces. |
| `Custom Event User Flows` | Ouvre l’expérience Application Insights User Flows pivotée sur le nom de l’événement personnalisé dans la cellule |
| `* User Flows` | Similaire aux flux d’utilisateurs d’événements personnalisés, à l’exception des exceptions, des affichages de pages et des requêtes |
| `User Timeline` | Ouvre la chronologie de l’utilisateur avec l’ID d’utilisateur (user_Id) dans la cellule |
| `Session Timeline` | Ouvre l’expérience de recherche Application Insights pour la valeur dans la cellule (par exemple, recherchez le texte « abc » où abc est la valeur dans la cellule) |
| `Resource overview` | Ouvrir la vue d’ensemble de la ressource dans le portail en fonction de la valeur de l’ID de la ressource dans la cellule |

## <a name="conditional-visibility"></a>Visibilité conditionnelle
Le classeur permet aux utilisateurs de faire apparaître ou disparaître certains contrôles en fonction des valeurs des paramètres. Cela permet aux auteurs d’avoir des rapports différents en fonction de l’état des entrées de l’utilisateur ou de la télémétrie. Un exemple propose un résumé aux clients lorsque tout va bien mais affiche les informations complètes en cas de problème.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Configuration de l’interactivité à l’aide de la visibilité conditionnelle
1. Suivez les étapes de la section `Setting up interactivity on grid row click` pour configurer deux contrôles interactifs.
2. Ajoutez un nouveau paramètre en haut :
    1. Nom : `ShowDetails`
    2. Type de paramètre : `Drop down`
    3. Obligatoire : `checked`
    4. Récupérer des données à partir de : `JSON`
    5. Entrée JSON : `["Yes", "No"]`
    6. Enregistrez pour valider les modifications.
3. Définir la valeur du paramètre sur `Yes`
4. Dans le contrôle de requête avec le graphique en aires, cliquez sur l’icône _Paramètres avancés_ (icône d’engrenage)
5. Vérifier le paramètre `Make this item conditionally visible`
    1. Cet élément est visible si la valeur de paramètre `ShowDetails` `equals` `Yes`
6. Cliquez sur _Fin de l’édition_ pour valider les modifications.
7. Cliquez sur _Fin de l’édition_ sur la barre d’outils du classeur pour passer en mode lecture.
8. Basculez la valeur du paramètre `ShowDetails` sur `No`. Notez que le graphique ci-dessous disparaît.

L’image ci-dessous représente le cas visible dans lequel `ShowDetails` est `Yes`

![Image montrant la visibilité conditionnelle dans laquelle le graphique est visible](./media/workbooks-interactive/conditional-visibility.png)

L’image ci-dessous représente le cas masqué dans lequel `ShowDetails` est `No`

![Image montrant la visibilité conditionnelle dans laquelle le graphique est masqué](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Étapes suivantes


* [Commencez](workbooks-visualizations.md) à en apprendre davantage sur les nombreuses options pour les visualisations enrichies des classeurs.
* [Contrôlez](workbooks-access-control.md) et partagez l’accès à vos ressources de classeur.
