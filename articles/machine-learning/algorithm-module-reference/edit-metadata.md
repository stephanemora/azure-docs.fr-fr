---
title: 'Modifier les métadonnées : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module de modifier les métadonnées de service Azure Machine Learning pour modifier les métadonnées associées aux colonnes dans un jeu de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: cfee607aca155b6cf68e5bddc40eb9c752df5e34
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028814"
---
# <a name="edit-metadata-module"></a>Module modifier les métadonnées

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour modifier les métadonnées qui sont associées aux colonnes dans un jeu de données. La valeur et type de données du jeu de données changera après avoir utilisé le **modifier les métadonnées** module. 
 
Les modifications de métadonnées peuvent inclure :
  
+ Traitement des colonnes booléennes ou numériques comme des valeurs catégorielles  
  
+ Indiquant la colonne qui contient le *classe* étiquette, ou les valeurs que vous souhaitez classer ou prédire  
  
+ Marquage des colonnes en tant que fonctionnalités
  
+ Date/heure de modification de valeurs à une valeur numérique, ou vice versa  
  
+ Renommer des colonnes
  
 Utilisez [modifier les métadonnées à chaque fois que vous devez modifier la définition d’une colonne, généralement pour répondre aux besoins d’un module en aval. Par exemple, certains modules peuvent fonctionnent uniquement avec les types de données spécifiques, ou nécessitent des indicateurs sur les colonnes, telles que `IsFeature` ou `IsCategorical`.  
  
 Après avoir effectué l’opération requise, vous pouvez réinitialiser les métadonnées à son état d’origine. 
  
## <a name="configure-edit-metadata"></a>Configurez des métadonnées de l’édition
  
1.  Dans Azure Machine Learning, ajouter [modifier les métadonnées](./edit-metadata.md) module à votre expérience et connectez le jeu de données que vous souhaitez mettre à jour. Vous pouvez le trouver sous **Transformation des données**, dans le **manipuler** catégorie.
  
2.  Cliquez sur **lancer le sélecteur de colonne** et choisissez la colonne ou l’ensemble de colonnes à utiliser. Vous pouvez choisir les colonnes individuellement, par nom ou index, ou vous pouvez choisir un groupe de colonnes, par type.  
  
3.  Sélectionnez le **type de données** option si vous devez affecter un autre type de données aux colonnes sélectionnées. Modification des données de type peut-être être nécessaires pour certaines opérations : par exemple, si votre jeu de données source a des nombres comme du texte, vous devez les modifier pour un type de données numérique avant d’utiliser des opérations mathématiques. 

    + Les types de données pris en charge sont `String`, `Integer`, `Double`, `Boolean`, `DateTime`. 

    + Si plusieurs colonnes sont sélectionnées, vous devez appliquer les modifications de métadonnées à **tous les** colonnes sélectionnées. Par exemple, supposons que vous choisissez les colonnes numériques 2-3. Vous pouvez les modifier tout en un type de données de chaîne et les renommer en une seule opération. Toutefois, vous ne pouvez pas modifier une colonne à un type de données de chaîne et une autre colonne à partir d’une valeur float à un entier.
  
    + Si vous ne spécifiez pas un type de données, les métadonnées de colonne sont inchangées. 
    
    + Le type de colonne et les valeurs seront modifiés après le [modifier les métadonnées](./edit-metadata.md) opération. Vous pouvez récupérer le type de données d’origine à tout moment à l’aide de [modifier les métadonnées](./edit-metadata.md) pour réinitialiser le type de données de colonne.  

    > [!NOTE]
    > Si vous modifiez n’importe quel type de nombre à la **DateTime** tapez, laissez le **DateTime Format** champ vide. Actuellement, il n’est pas possible de spécifier le format de données cible.  

      
4.  Sélectionnez le **catégorie** option pour spécifier que les valeurs dans les colonnes sélectionnées doivent être considérées comme des catégories. 

    Par exemple, vous pourrez avoir une colonne qui contient les nombres 0,1 et 2, mais savez que les numéros signifient réellement « Fumeur », « Non fumeur » et « Inconnu ». Dans ce cas, le fait de marquer la colonne comme catégorielle, vous pouvez garantir que les valeurs ne sont pas utilisées dans les calculs numériques, uniquement pour regrouper les données. 
  
5.  Utilisez le **champs** option si vous souhaitez modifier la façon dont qu’Azure Machine Learning utilise les données dans un modèle.

    + **Caractéristique** : Utilisez cette option pour marquer une colonne en tant que fonctionnalité, pour une utilisation avec les modules qui fonctionnent uniquement sur les colonnes de fonctionnalité. Par défaut, toutes les colonnes sont traitées initialement en tant que fonctionnalités.  
  
    + **Étiquette** : Utilisez cette option pour marquer l’étiquette (également appelé attribut prédictible, ou variable cible). De nombreux modules nécessitent qu’au moins un (et un seul) colonne d’étiquette être présents dans le jeu de données. 
    
        Dans de nombreux cas, Azure Machine Learning peut déduire qu’une colonne contient une étiquette de classe, mais en définissant ces métadonnées, vous pouvez garantir que la colonne est identifiée correctement. Si cette option ne modifie pas les valeurs de données, uniquement la manière dont certains algorithmes d’apprentissage automatique gèrent les données.
  

  
    > [!TIP]
    >  Vous avez les données qui ne tient pas dans ces catégories ?  Par exemple, votre jeu de données peut contenir des valeurs telles que des identificateurs uniques qui ne sont pas utiles en tant que variables. ID entraîne parfois des problèmes lorsqu’il est utilisé dans un modèle. 
    >   
    >  Heureusement « en coulisses », Azure Machine Learning conserve toutes vos données, afin que vous n’êtes pas obligé de supprimer ces colonnes du jeu de données. Lorsque vous avez besoin effectuer des opérations sur un ensemble spécial de colonnes, supprimez simplement toutes les autres colonnes temporairement à l’aide de la [Select Columns in Dataset](./select-columns-in-dataset.md) module. Vous pouvez ultérieurement fusionner les colonnes dans le jeu de données à l’aide de la [Add Columns](./add-columns.md) module.  
  
6. Utilisez les options suivantes pour effacer les sélections précédentes et restaurer des métadonnées pour les valeurs par défaut.  
  
    + **Effacer la fonctionnalité**: Utilisez cette option pour supprimer l’indicateur de fonctionnalité.  
  
         Étant donné que toutes les colonnes sont traitées initialement en tant que fonctionnalités, pour les modules qui effectuent des opérations mathématiques, vous devrez peut-être utiliser cette option pour empêcher des colonnes numériques d’être traités en tant que variables.
  
    + **Effacer l’étiquette**: Utilisez cette option pour supprimer le **étiquette** les métadonnées à partir de la colonne spécifiée.  
  
    + **Effacer le score**: Utilisez cette option pour supprimer le **score** les métadonnées à partir de la colonne spécifiée.  
  
         Actuellement, la possibilité de marquer explicitement une colonne comme un score n’est pas disponible dans Azure Machine Learning. Toutefois, certaines opérations aboutir à une colonne soit marquée comme un score en interne. En outre, un module R personnalisé peut-être générer des valeurs de score.
  
  
7.  Pour **nouveaux noms de colonnes**, tapez le nouveau nom de l’ou les colonnes sélectionnées.  
  
    + Les noms de colonne peuvent utiliser uniquement des caractères qui sont pris en charge par le format UTF-8 de codage. Les chaînes vides, les valeurs NULL ou composée uniquement d’espaces de noms ne sont pas autorisés.  
  
    + Pour renommer plusieurs colonnes, tapez les noms sous forme de liste séparée par des virgules dans l’ordre de l’index de colonne.  
  
    + Toutes les colonnes sélectionnées doivent être renommés. Vous ne pouvez pas omettre ou ignorer des colonnes.  
  
  
8.  Exécutez l’expérience.  

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 