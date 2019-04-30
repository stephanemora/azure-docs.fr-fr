---
title: Effectuer des opérations sur les données - Azure Logic Apps | Microsoft Docs
description: Convertir, gérer et manipuler des sorties de données et des formats dans Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 93c24f88fcd6a002493933ef71c5c80bd2ff8c10
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120177"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Effectuer des opérations sur les données dans Azure Logic Apps

Cet article vous explique comment travailler avec des données dans vos applications logiques en ajoutant des actions pour ces tâches et bien plus encore :

* Créez des tables à partir de tableaux.
* Créez des tableaux à partir d’autres tableaux selon une condition.
* Créez des jetons conviviaux à partir des propriétés d’objet JavaScript Objet Notation (JSON) afin de pouvoir facilement utiliser ces propriétés dans votre flux de travail.

Si vous ne trouvez pas l’action souhaitée ici, essayez de parcourir les nombreuses différentes [fonctions de manipulation de données](../logic-apps/workflow-definition-language-functions-reference.md) fournies par Logic Apps. 

Ces tables résument les opérations sur les données que vous pouvez utiliser. Elles sont organisées selon les types de données source sur lesquelles elles fonctionnent, mais chaque description s’affiche par ordre alphabétique.

**Actions de tableau** 

Ces actions vous permettent de travailler avec les données de tableaux.

| Action | Description | 
|--------|-------------| 
| [**Créer une table CSV**](#create-csv-table-action) | Créez une table CSV (valeurs séparées par des virgules) à partir d’un tableau. | 
| [**Créer une table HTML**](#create-html-table-action) | Créez une table HTML à partir d’un tableau. | 
| [**Filtrer le tableau**](#filter-array-action) | Créez un sous-ensemble de tableau à partir d’un tableau en fonction du filtre ou de la condition que vous spécifiez. | 
| [**Joindre**](#join-action) | Créez une chaîne à partir de tous les éléments d’un tableau et séparez chaque élément à l’aide du caractère spécifié. | 
| [**Sélectionner**](#select-action) | Créez un tableau à partir des propriétés spécifiées pour tous les éléments dans un tableau différent. | 
||| 

**Actions JSON**

Ces actions vous permettent d’utiliser des données au format JavaScript Objet Notation (JSON).

| Action | Description | 
|--------|-------------| 
| [**Composer**](#compose-action) | Créez un message ou une chaîne à partir de plusieurs entrées qui peuvent avoir différents types de données. Vous pouvez ensuite utiliser cette chaîne comme une entrée unique au lieu d’entrer plusieurs fois les mêmes entrées. Par exemple, vous pouvez créer un message JSON unique à partir de diverses entrées. | 
| [**Analyser JSON**](#parse-json-action) | Créez des jetons de données conviviaux pour les propriétés au format JSON afin de pouvoir utiliser plus facilement les propriétés dans vos applications logiques. | 
||| 

Pour créer des transformations JSON plus complexes, consultez [Effectuer des transformations JSON avancées avec des modèles Liquid](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Conditions préalables

Pour suivre les exemples de cet article, vous avez besoin de ces éléments :

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* L’application logique dans laquelle vous effectuerez l’opération pour utiliser les données 

  Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [déclencher](../logic-apps/logic-apps-overview.md#logic-app-concepts) en tant que première étape de votre application logique 

  Les opérations sur les données sont disponibles uniquement en tant qu’actions. Ainsi, avant de pouvoir utiliser ces actions, démarrez votre application logique avec un déclencheur et incluez toutes les actions nécessaires à la création des sorties souhaitées.

<a name="compose-action"></a>

## <a name="compose-action"></a>Action compose

Pour construire une sortie unique tel un objet JSON à partir de plusieurs entrées, vous pouvez utiliser l’action **Opérations sur les données - Composer**. Vos entrées peuvent avoir différents types tels que des entiers, des valeurs booléennes, des tableaux, des objets JSON et autres types natifs pris en charge par Azure Logic Apps, notamment binaire et XML. Vous pouvez ensuite utiliser la sortie dans les actions qui suivent l’action **Composer**. L’action **Composer** vous évite également d’avoir à entrer à plusieurs reprises les mêmes entrées pendant la création du flux de travail de votre application logique. 

Par exemple, vous pouvez construire un message JSON à partir de plusieurs variables, telles que des variables de chaînes qui stockent les prénoms et noms de personnes, et une variable de type entier qui stocke l’âge de ces personnes. Ici, l’action **Composer** accepte ces entrées :

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

et crée cette sortie :

`{"age":35,"fullName":"Owens,Sophie"}`

Par exemple, procédez comme suit à l’aide du Concepteur d’application logique. Ou, si vous préférez travailler dans l’éditeur en mode code, vous pouvez copier l’exemple **Compose** et **initialiser la variable** définitions d’action à partir de cet article dans votre propre application logique sous-jacente du flux de travail Définition : [Exemples de code d’opération de données - composer](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a> ou dans Visual Studio, ouvrez votre application logique dans le Concepteur d’application logique. 

   Cet exemple utilise le portail Azure et une application logique avec un déclencheur **Périodicité** et plusieurs actions **Initialiser la variable**. 
   Ces actions sont définies pour créer deux variables de chaînes et une variable de type entier. Lorsque vous testez ultérieurement votre application logique, vous pouvez exécuter manuellement votre application sans attendre le déclencheur.

   ![Lancement d’un exemple d’application logique](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

2. Dans l’application logique dans laquelle vous souhaitez créer la sortie, effectuez une des opérations suivantes : 

   * Pour ajouter une action à la dernière étape, choisissez **Nouvelle étape** > **Ajouter une action**.

     ![Ajouter une action](./media/logic-apps-perform-data-operations/add-compose-action.png)

   * Pour ajouter une action entre des étapes, positionnez votre souris sur la flèche de connexion de façon à ce que le signe plus (+) s’affiche. 
   Cliquez sur le signe plus, puis choisissez **Ajouter une action**.

3. Dans la zone de recherche, saisissez le filtre « compose ». Dans la liste des actions, sélectionnez cette action : **Composer**

   ![Sélectionner l’action « Composer »](./media/logic-apps-perform-data-operations/select-compose-action.png)

4. Dans la zone **Entrées**, indiquez les entrées que vous souhaitez utiliser pour créer la sortie. 

   Pour cet exemple, lorsque vous cliquez dans la zone **Entrées**, la liste de contenu dynamique s’affiche et vous pouvez sélectionner les variables créées précédemment :

   ![Sélectionner les entrées de la composition](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Voici l’exemple terminé de l’action **Composer** : 

   ![Action « Composer » terminée](./media/logic-apps-perform-data-operations/finished-compose-action.png)

5. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

Pour plus d’informations sur cette action dans votre définition de flux de travail sous-jacente, consultez l’[action Composer](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action). 

### <a name="test-your-logic-app"></a>Tester votre application logique

Pour vérifier si l’action **Composer** produit les résultats attendus, envoyez-vous une notification incluant la sortie de l’action **Composer**.

1. Dans votre application logique, ajoutez une action capable de vous envoyer les résultats de l’action **Composer**.

2. Dans cette action, cliquez à l’endroit où vous souhaitez afficher les résultats. Lorsque la liste de contenu dynamique s’ouvre, sous l’action **Composer**, sélectionnez **Sortie**. 

   Cet exemple utilise l’action **Office 365 Outlook - Envoyer un e-mail** et inclut les champs **Sortie** dans l’objet et le corps du message :

   ![Champs « Sortie » dans l’action « Envoyer un e-mail »](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

3. À présent, exécutez manuellement votre application logique. Dans la barre d’outils du concepteur, choisissez **Exécuter**. 

   Selon le connecteur de messagerie électronique que vous avez utilisé, voici les résultats que vous obtenez :

   ![Message électronique avec les résultats de l’action « Composer »](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Créer une action de table CSV

Pour créer une table de valeurs séparées par des virgules (CSV) avec les propriétés et valeurs d’objets JavaScript Objet Notation (JSON) dans un tableau, utilisez l’action **Opérations sur les données - Créer une table CSV**. Vous pouvez ensuite utiliser la table résultante dans les actions qui suivent l’action **Créer une table CSV**. 

Si vous préférez travailler dans l’éditeur en mode code, vous pouvez copier l’exemple **créer un tableau CSV** et **initialiser la variable** définitions d’action à partir de cet article dans votre propre application logique sous-jacente du flux de travail Définition : [Exemples de code d’opération de données - créer un tableau CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example) 

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a> ou dans Visual Studio, ouvrez votre application logique dans le Concepteur d’application logique. 

   Cet exemple utilise le portail Azure et une application logique avec un déclencheur **Périodicité** et une action **Initialiser la variable**. 
   L’action est configurée pour la création d’une variable dont la valeur initiale est un tableau ayant certaines propriétés et valeurs au format JSON. 
   Lorsque vous testez ultérieurement votre application logique, vous pouvez exécuter manuellement votre application sans attendre le déclencheur.

   ![Lancement d’un exemple d’application logique](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. Dans l’application logique dans laquelle vous souhaitez créer la table CSV, effectuez une des opérations suivantes : 

   * Pour ajouter une action à la dernière étape, choisissez **Nouvelle étape** > **Ajouter une action**.

     ![Ajouter une action](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Pour ajouter une action entre des étapes, positionnez votre souris sur la flèche de connexion de façon à ce que le signe plus (+) s’affiche. 
   Cliquez sur le signe plus, puis choisissez **Ajouter une action**.

3. Dans la zone de recherche, entrez « créer une table csv » comme filtre. Dans la liste des actions, sélectionnez cette action : **Créer un tableau CSV**

   ![Sélectionner l’action « Créer une table CSV »](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

4. Dans la zone **De**, fournissez le tableau ou l’expression pour la création de la table. 

   Pour cet exemple, lorsque vous cliquez dans la zone **De**, la liste de contenu dynamique s’affiche pour vous permettre de sélectionner la variable créée précédemment :

   ![Sélectionner une sortie de tableau pour créer une table CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   Voici l’exemple terminé de l’action **Créer une table CSV** : 

   ![Action « Créer une table CSV » terminée](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

   Par défaut, cette action crée automatiquement les colonnes en fonction des éléments du tableau. 
   Pour créer manuellement les en-têtes de colonnes et les valeurs, choisissez **Afficher les options avancées**. 
   Pour fournir uniquement des valeurs personnalisées, définissez **Colonnes** sur **Personnalisé**. 
   Pour fournir également des en-têtes de colonnes personnalisés, définissez **Inclure les en-têtes** sur **Oui**. 

   > [!TIP]
   > Pour créer des jetons conviviaux pour les propriétés des objets JSON afin de pouvoir sélectionner ces propriétés en tant qu’entrées, utilisez l’action [Analyser JSON](#parse-json-action) avant d’appeler l’action **Créer une table CSV**.

5. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

Pour plus d’informations sur cette action dans votre définition de flux de travail sous-jacente, consultez l’[action Table](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Tester votre application logique

Pour vérifier si l’action **Créer une table CSV** produit les résultats attendus, envoyez-vous une notification incluant la sortie de l’action **Créer une table CSV**.

1. Dans votre application logique, ajoutez une action capable de vous envoyer les résultats de l’action **Créer une table CSV**.

2. Dans cette action, cliquez à l’endroit où vous souhaitez afficher les résultats. Lorsque la liste de contenu dynamique s’ouvre, sous l’action **Créer une table CSV**, sélectionnez **Sortie**. 

   Cet exemple utilise l’action **Office 365 Outlook - Envoyer un e-mail** et inclut le champ **Sortie** dans le corps du message :

   ![Champs « Sortie » dans l’action « Envoyer un e-mail »](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

3. À présent, exécutez manuellement votre application logique. Dans la barre d’outils du concepteur, choisissez **Exécuter**. 

   Selon le connecteur de messagerie électronique que vous avez utilisé, voici les résultats que vous obtenez :

   ![Envoyer un e-mail avec les résultats de l’action « Créer une table CSV »](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Action Créer une table HTML

Pour créer une table HTML avec les propriétés et valeurs d’objets JavaScript Objet Notation (JSON) dans un tableau, utilisez l’action **Opérations sur les données - Créer une table HTML**. Vous pouvez ensuite utiliser la table résultante dans les actions qui suivent l’action **Créer une table HTML**.

Si vous préférez travailler dans l’éditeur en mode code, vous pouvez copier l’exemple **créer un tableau HTML** et **initialiser la variable** définitions d’action à partir de cet article dans votre propre application logique sous-jacente du flux de travail Définition : [Exemples de code d’opération de données - créer un tableau HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a> ou dans Visual Studio, ouvrez votre application logique dans le Concepteur d’application logique. 

   Cet exemple utilise le portail Azure et une application logique avec un déclencheur **Périodicité** et une action **Initialiser la variable**. 
   L’action est configurée pour la création d’une variable dont la valeur initiale est un tableau ayant certaines propriétés et valeurs au format JSON. 
   Lorsque vous testez ultérieurement votre application logique, vous pouvez exécuter manuellement votre application sans attendre le déclencheur.

   ![Lancement d’un exemple d’application logique](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. Dans l’application logique dans laquelle vous souhaitez créer une table HTML, effectuez une des opérations suivantes : 

   * Pour ajouter une action à la dernière étape, choisissez **Nouvelle étape** > **Ajouter une action**.

     ![Ajouter une action](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Pour ajouter une action entre des étapes, positionnez votre souris sur la flèche de connexion de façon à ce que le signe plus (+) s’affiche. 
   Cliquez sur le signe plus, puis choisissez **Ajouter une action**.

3. Dans la zone de recherche, entrez « créer une table html » comme filtre. Dans la liste des actions, sélectionnez cette action : **Créer un tableau HTML**

   ![Sélectionner l’action « Créer une table HTML »](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

4. Dans la zone **De**, fournissez le tableau ou l’expression pour la création de la table. 

   Pour cet exemple, lorsque vous cliquez dans la zone **De**, la liste de contenu dynamique s’affiche pour vous permettre de sélectionner la variable créée précédemment :

   ![Sélectionner une sortie de tableau pour créer une table HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   Voici l’exemple terminé de l’action **Créer une table HTML** : 

   ![Action « Créer une table HTML » terminée](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

   Par défaut, cette action crée automatiquement les colonnes en fonction des éléments du tableau. 
   Pour créer manuellement les en-têtes de colonnes et les valeurs, choisissez **Afficher les options avancées**. 
   Pour fournir uniquement des valeurs personnalisées, définissez **Colonnes** sur **Personnalisé**. 
   Pour fournir également des en-têtes de colonnes personnalisés, définissez **Inclure les en-têtes** sur **Oui**. 

   > [!TIP]
   > Pour créer des jetons conviviaux pour les propriétés des objets JSON afin de pouvoir sélectionner ces propriétés en tant qu’entrées, utilisez l’action [Analyser JSON](#parse-json-action) avant d’appeler l’action **Créer une table HTML**.

5. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

Pour plus d’informations sur cette action dans votre définition de flux de travail sous-jacente, consultez l’[action Table](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Tester votre application logique

Pour vérifier si l’action **Créer une table HTML** produit les résultats attendus, envoyez-vous une notification incluant la sortie de l’action **Créer une table HTML**.

1. Dans votre application logique, ajoutez une action capable de vous envoyer les résultats de l’action **Créer une table HTML**.

2. Dans cette action, cliquez à l’endroit où vous souhaitez afficher les résultats. Lorsque la liste de contenu dynamique s’ouvre, sous l’action **Créer une table HTML**, sélectionnez **Sortie**. 

   Cet exemple utilise l’action **Office 365 Outlook - Envoyer un e-mail** et inclut le champ **Sortie** dans le corps du message :

   ![Champs « Sortie » dans l’action « Envoyer un e-mail »](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)
   
   > [!NOTE]
   > Lorsque vous incluez la sortie de table HTML dans une action d’envoi d’un e-mail, assurez-vous que vous avez défini la propriété **Est HTML** sur **Oui** dans les options avancées de cette action. De cette façon, l’action d’envoi d’un e-mail formate correctement la table HTML.

3. À présent, exécutez manuellement votre application logique. Dans la barre d’outils du concepteur, choisissez **Exécuter**. 

   Selon le connecteur de messagerie électronique que vous avez utilisé, voici les résultats que vous obtenez :

   ![Envoyer un e-mail avec les résultats de l’action « Créer une table HTML »](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Action Filtrer le tableau

Pour créer un tableau plus petit comportant des éléments qui répondent aux critères spécifiques, dans un tableau existant, utilisez l’action **Opérations sur les données - Filtrer le tableau**. Vous pouvez ensuite utiliser le tableau filtré dans les actions qui suivent l’action **Filtrer le tableau**. 

> [!NOTE]
> Le texte que vous utilisez comme filtre dans votre condition respecte la casse. En outre, cette action ne permet pas de modifier le format ou les composants des éléments du tableau. 
> 
> Pour que les actions utilisent la sortie du tableau générée par l’action **Filtrer le tableau**, ces actions doivent accepter des tableaux en tant qu’entrée, ou vous devrez éventuellement convertir le tableau de sortie dans un autre format compatible. 

Si vous préférez travailler dans l’éditeur en mode code, vous pouvez copier l’exemple **Filter array** et **initialiser la variable** définitions d’action à partir de cet article dans votre propre application logique sous-jacente du flux de travail Définition : [Exemples de code d’opération de données - filtrer le tableau](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example) 

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a> ou dans Visual Studio, ouvrez votre application logique dans le Concepteur d’application logique. 

   Cet exemple utilise le portail Azure et une application logique avec un déclencheur **Périodicité** et une action **Initialiser la variable**. 
   L’action est configurée pour la création d’une variable dont la valeur initiale est un tableau ayant certains exemples d’entiers. Lorsque vous testez ultérieurement votre application logique, vous pouvez exécuter manuellement votre application sans attendre le déclencheur.

   > [!NOTE]
   > Bien que cet exemple utilise un simple tableau d’entiers, cette action est particulièrement utile pour les tableaux d’objets JSON que vous pouvez filtrer selon les propriétés et valeurs des objets.

   ![Lancement d’un exemple d’application logique](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

2. Dans l’application logique dans laquelle vous souhaitez créer le tableau filtré, effectuez une des opérations suivantes : 

   * Pour ajouter une action à la dernière étape, choisissez **Nouvelle étape** > **Ajouter une action**.

     ![Ajouter une action](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Pour ajouter une action entre des étapes, positionnez votre souris sur la flèche de connexion de façon à ce que le signe plus (+) s’affiche. 
   Cliquez sur le signe plus, puis choisissez **Ajouter une action**.

3. Dans la zone de recherche, entrez « filtrer tableau » en tant que filtre. Dans la liste des actions, sélectionnez cette action : **Filtrer le tableau**

   ![Sélectionner l’action « Filtrer le tableau »](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

4. Dans la zone **De**, fournissez le tableau ou l’expression que vous souhaitez filtrer. 

   Pour cet exemple, lorsque vous cliquez dans la zone **De**, la liste de contenu dynamique s’affiche pour vous permettre de sélectionner la variable créée précédemment :

   ![Sélectionner une sortie de tableau pour créer un tableau filtré](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

5. Pour la condition, choisissez les éléments du tableau à comparer, sélectionnez l’opérateur de comparaison, puis spécifiez la valeur de comparaison.

   Cet exemple utilise la fonction **item()** pour accéder à chaque élément du tableau lorsque l’action **Filtrer le tableau** recherche les éléments de tableau dont la valeur est supérieure à 1 :
   
   ![Action « Filtrer le tableau » terminée](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

6. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

Pour plus d’informations sur cette action dans votre définition de flux de travail sous-jacente, consultez l’[action Interroger](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Tester votre application logique

Pour vérifier si l’action **Filtrer le tableau** produit les résultats attendus, envoyez-vous une notification incluant la sortie de l’action **Filtrer le tableau**.

1. Dans votre application logique, ajoutez une action capable de vous envoyer les résultats de l’action **Filtrer le tableau**.

2. Dans cette action, cliquez à l’endroit où vous souhaitez afficher les résultats. Lorsque la liste de contenu dynamique s’affiche, choisissez **Expression**. Pour obtenir la sortie du tableau à l’aide de l’action **Filtrer le tableau**, entrez cette expression incluant le nom de l’action **Filtrer le tableau** :

   ```
   @actionBody('Filter_array')
   ```

   Cet exemple utilise l’action **Office 365 Outlook - Envoyer un e-mail** et inclut les sorties de l’expression **actionBody('Filter_array')** dans le corps du message :

   ![Sorties de l’action dans l’action « Envoyer un e-mail »](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

3. À présent, exécutez manuellement votre application logique. Dans la barre d’outils du concepteur, choisissez **Exécuter**. 

   Selon le connecteur de messagerie électronique que vous avez utilisé, voici les résultats que vous obtenez :

   ![Message électronique avec les résultats de l’action « Filtrer un tableau »](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Action Joindre

Pour créer une chaîne contenant tous les éléments d’un tableau et séparer ces éléments à l’aide d’un caractère délimiteur spécifique, utilisez l’action **Opérations sur les données - Joindre**. Vous pouvez ensuite utiliser la chaîne dans les actions qui suivent l’action **Joindre**.

Si vous préférez travailler dans l’éditeur en mode code, vous pouvez copier l’exemple **joindre** et **initialiser la variable** définitions d’action à partir de cet article dans votre propre application logique sous-jacent d’une définition de flux de travail : [Exemples de code d’opération de données - jointure](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example) 

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a> ou dans Visual Studio, ouvrez votre application logique dans le Concepteur d’application logique. 

   Cet exemple utilise le portail Azure et une application logique avec un déclencheur **Périodicité** et une action **Initialiser la variable**. 
   Cette action est configurée pour la création d’une variable dont la valeur initiale est un tableau ayant certains exemples d’entiers. 
   Lorsque vous testez ultérieurement votre application logique, vous pouvez exécuter manuellement votre application sans attendre le déclencheur.

   ![Lancement d’un exemple d’application logique](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

2. Dans l’application logique dans laquelle vous souhaitez créer la chaîne à partir d’un tableau, effectuez une des opérations suivantes : 

   * Pour ajouter une action à la dernière étape, choisissez **Nouvelle étape** > **Ajouter une action**.

     ![Ajouter une action](./media/logic-apps-perform-data-operations/add-join-action.png)

   * Pour ajouter une action entre des étapes, positionnez votre souris sur la flèche de connexion de façon à ce que le signe plus (+) s’affiche. 
   Cliquez sur le signe plus, puis choisissez **Ajouter une action**.

3. Dans la zone de recherche, entrez « joindre » comme filtre. Dans la liste des actions, sélectionnez cette action : **Join**

   ![Sélectionner l’action « Données sur les opérations – Joindre »](./media/logic-apps-perform-data-operations/select-join-action.png)

4. Dans la zone **De**, fournissez le tableau contenant les éléments que vous souhaitez joindre sous forme de chaîne. 

   Pour cet exemple, lorsque vous cliquez dans la zone **De**, la liste de contenu dynamique s’affiche pour vous permettre de sélectionner la variable créée précédemment :  

   ![Sélectionner une sortie de tableau pour créer la chaîne](./media/logic-apps-perform-data-operations/configure-join-action.png)

5. Dans la zone **Joindre avec**, spécifiez le caractère de séparation de chaque élément du tableau. 

   Cet exemple utilise le deux-points (:) comme séparateur.

   ![Spécifier le caractère de séparation](./media/logic-apps-perform-data-operations/finished-join-action.png)

6. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

Pour plus d’informations sur cette action dans votre définition de flux de travail sous-jacente, consultez l’[action Joindre](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Tester votre application logique

Pour vérifier si l’action **Joindre** produit les résultats attendus, envoyez-vous une notification incluant la sortie de l’action **Joindre**. 

1. Dans votre application logique, ajoutez une action capable de vous envoyer les résultats de l’action **Joindre**.

2. Dans cette action, cliquez à l’endroit où vous souhaitez afficher les résultats. Lorsque la liste de contenu dynamique s’ouvre, sous l’action **Joindre**, sélectionnez **Sortie**. 

   Cet exemple utilise l’action **Office 365 Outlook - Envoyer un e-mail** et inclut le champ **Sortie** dans le corps du message :

   ![Champs « Sortie » dans l’action « Envoyer un e-mail »](./media/logic-apps-perform-data-operations/send-email-join-action.png)

3. À présent, exécutez manuellement votre application logique. Dans la barre d’outils du concepteur, choisissez **Exécuter**. 

   Selon le connecteur de messagerie électronique que vous avez utilisé, voici les résultats que vous obtenez :

   ![Message électronique avec les résultats de l’action « Joindre »](./media/logic-apps-perform-data-operations/join-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Action d’analyse de JSON

Pour référencer ou accéder aux propriétés du contenu JavaScript Objet Notation (JSON), vous pouvez créer des champs ou des jetons conviviaux pour ces propriétés à l’aide de l’action **Opérations sur les données - Analyser JSON**.
De cette façon, vous pouvez sélectionner ces propriétés dans la liste de contenu dynamique lorsque vous spécifiez des entrées pour votre application logique. Pour cette action, vous pouvez fournir un schéma JSON ou générer un schéma JSON à partir de votre exemple de contenu JSON ou de charge utile.

Si vous préférez travailler dans l’éditeur en mode code, vous pouvez copier l’exemple **analyser JSON** et **initialiser la variable** définitions d’action à partir de cet article dans votre propre application logique sous-jacent d’une définition de workflow : [Exemples de code d’opération de données - analyser JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example) 

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a> ou dans Visual Studio, ouvrez votre application logique dans le Concepteur d’application logique. 

   Cet exemple utilise le portail Azure et une application logique avec un déclencheur **Périodicité** et une action **Initialiser la variable**. 
   L’action est configurée pour la création d’une variable dont la valeur initiale est un objet JSON avec des propriétés et valeurs. 
   Lorsque vous testez ultérieurement votre application logique, vous pouvez exécuter manuellement votre application sans attendre le déclencheur.

   ![Lancement d’un exemple d’application logique](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

2. Dans l’application logique dans laquelle vous souhaitez analyser le contenu JSON, effectuez une des opérations suivantes : 

   * Pour ajouter une action à la dernière étape, choisissez **Nouvelle étape** > **Ajouter une action**.

     ![Ajouter une action](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Pour ajouter une action entre des étapes, positionnez votre souris sur la flèche de connexion de façon à ce que le signe plus (+) s’affiche. 
   Cliquez sur le signe plus, puis choisissez **Ajouter une action**.

3. Dans la zone de recherche, entrez « analyser json » comme filtre. Dans la liste des actions, sélectionnez cette action : **Analyser JSON**

   ![Sélectionner l’action « Analyser JSON »](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

4. Dans la zone **Contenu**, spécifiez le contenu JSON que vous souhaitez analyser. 

   Pour cet exemple, lorsque vous cliquez dans la zone **Contenu**, la liste de contenu dynamique s’affiche pour vous permettre de sélectionner la variable créée précédemment :

   ![Sélectionner l’objet JSON pour l’action Analyser JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

5. Entrez le schéma JSON qui décrit le contenu JSON que vous analysez. 

   Pour cet exemple, voici le schéma JSON :

   ![Fournir le schéma JSON de l’objet JSON à analyser](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Si vous n’avez pas le schéma, vous pouvez le générer à partir du contenu JSON ou de la *charge utile* que vous analysez. 
   
   1. Dans l’option **Analyser JSON**, sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**.

   2. Sous **Entrer ou coller un exemple de charge utile JSON**, fournissez le contenu JSON, puis choisissez **Terminé**.

      ![Entrer le contenu JSON pour générer le schéma](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

6. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

Pour plus d’informations sur cette action dans votre définition de flux de travail sous-jacente, consultez l’[action Analyser JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Tester votre application logique

Pour vérifier si l’action **Analyser JSON** produit les résultats attendus, envoyez-vous une notification incluant la sortie de l’action **Analyser JSON**.

1. Dans votre application logique, ajoutez une action capable de vous envoyer les résultats de l’action **Analyser JSON**.

2. Dans cette action, cliquez à l’endroit où vous souhaitez afficher les résultats. Lorsque la liste de contenu dynamique s’ouvre, sous l’action **Analyser JSON**, vous pouvez maintenant sélectionner les propriétés à partir du contenu JSON analysé.

   Cet exemple utilise l’action **Office 365 Outlook - Envoyer un e-mail** et inclut les champs **Prénom**, **Nom** et **E-mail** dans le corps du message :

   ![Propriétés JSON de l’action « Envoyer un e-mail »](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Voici l’action d’envoi d’un e-mail terminée :

   ![Action d’envoi d’un e-mail terminée](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

3. À présent, exécutez manuellement votre application logique. Dans la barre d’outils du concepteur, choisissez **Exécuter**. 

   Selon le connecteur de messagerie électronique que vous avez utilisé, voici les résultats que vous obtenez :

   ![Message électronique avec les résultats de l’action « Joindre »](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Action select

Pour créer un tableau contenant des objets JSON générés à partir des valeurs d’un tableau existant, utilisez l’action **Opérations sur les données - Sélectionner**. Par exemple, vous pouvez créer un objet JSON pour chaque valeur d’un tableau d’entiers en spécifiant les propriétés que chaque objet JSON doit avoir et comment mapper les valeurs du tableau source à ces propriétés. Même si vous pouvez modifier les composants de ces objets JSON, le tableau de sortie possède toujours le même nombre d’éléments que le tableau source.

> [!NOTE]
> Pour que les actions utilisent la sortie du tableau générée par l’action **Sélectionner**, ces actions doivent accepter des tableaux en tant qu’entrée, ou vous devrez éventuellement convertir le tableau de sortie dans un autre format compatible. 

Si vous préférez travailler dans l’éditeur en mode code, vous pouvez copier l’exemple **sélectionnez** et **initialiser la variable** définitions d’action à partir de cet article dans votre propre application logique sous-jacent d’une définition de flux de travail : [Exemples de code d’opération de données - sélectionner](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a> ou dans Visual Studio, ouvrez votre application logique dans le Concepteur d’application logique. 

   Cet exemple utilise le portail Azure et une application logique avec un déclencheur **Périodicité** et une action **Initialiser la variable**. 
   L’action est configurée pour la création d’une variable dont la valeur initiale est un tableau ayant certains exemples d’entiers. 
   Lorsque vous testez ultérieurement votre application logique, vous pouvez exécuter manuellement votre application sans attendre le déclencheur.

   ![Lancement d’un exemple d’application logique](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

2. Dans l’application logique dans laquelle vous souhaitez créer le tableau, effectuez une des opérations suivantes : 

   * Pour ajouter une action à la dernière étape, choisissez **Nouvelle étape** > **Ajouter une action**.

     ![Ajouter une action](./media/logic-apps-perform-data-operations/add-select-action.png)

   * Pour ajouter une action entre des étapes, positionnez votre souris sur la flèche de connexion de façon à ce que le signe plus (+) s’affiche. 
   Cliquez sur le signe plus, puis choisissez **Ajouter une action**.

3. Dans la zone de recherche, entrez « sélectionner » comme filtre. Dans la liste des actions, sélectionnez cette action : **Select**

   ![Sélectionner l’action « Sélectionner »](./media/logic-apps-perform-data-operations/select-select-action.png)

4. Dans le champ **De**, choisissez le tableau source.

   Pour cet exemple, lorsque vous cliquez dans la zone **De**, la liste de contenu dynamique s’affiche pour vous permettre de sélectionner la variable créée précédemment :

   ![Choisir le tableau source pour l’action « Sélectionner »](./media/logic-apps-perform-data-operations/configure-select-action.png)

5. Dans la colonne de gauche de la zone **Mapper**, indiquez le nom de propriété que vous souhaitez assigner à chaque valeur du tableau source. Dans la colonne de droite, spécifiez une expression qui représente la valeur que vous souhaitez assigner à la propriété.

   Cet exemple utilise le nom de propriété « Product_ID » qui sera assigné à chaque valeur du tableau d’entiers à l’aide de la fonction **item()** dans une expression qui accède à chaque élément du tableau. 

   ![Spécifier la propriété et les valeurs de l’objet JSON pour le tableau à créer](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Voici l’action terminée :

   ![Action Sélectionner terminée](./media/logic-apps-perform-data-operations/finished-select-action.png)

6. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

Pour plus d’informations sur cette action dans votre définition de flux de travail sous-jacente, consultez l’[action Sélectionner](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Tester votre application logique

Pour vérifier si l’action **Sélectionner** produit les résultats attendus, envoyez-vous une notification incluant la sortie de l’action **Sélectionner**.

1. Dans votre application logique, ajoutez une action capable de vous envoyer les résultats de l’action **Sélectionner**.

2. Dans cette action, cliquez à l’endroit où vous souhaitez afficher les résultats. Lorsque la liste de contenu dynamique s’affiche, choisissez **Expression**. Pour obtenir la sortie du tableau à l’aide de l’action **Sélectionner**, entrez cette expression incluant le nom de l’action **Sélectionner** :

   ```
   @actionBody('Select')
   ```

   Cet exemple utilise l’action **Office 365 Outlook - Envoyer un e-mail** et inclut les sorties de l’expression **actionBody('Select')** dans le corps du message :

   ![Sorties de l’action dans l’action « Envoyer un e-mail »](./media/logic-apps-perform-data-operations/send-email-select-action.png)

3. À présent, exécutez manuellement votre application logique. Dans la barre d’outils du concepteur, choisissez **Exécuter**. 

   Selon le connecteur de messagerie électronique que vous avez utilisé, voici les résultats que vous obtenez :

   ![Message électronique avec les résultats de l’action « Sélectionner »](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [connecteurs Logic Apps](../connectors/apis-list.md)
