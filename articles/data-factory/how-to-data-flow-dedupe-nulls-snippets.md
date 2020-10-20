---
title: Déduplication de lignes et recherche de valeurs Null à l’aide d’extraits de flux de données
description: Découvrez comment dédupliquer facilement des lignes et rechercher des valeurs Null à l’aide d’extraits de code dans des flux de données
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 841484a647d2737d621c75ebe63f65f2de829a26
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666498"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>Déduplication de lignes et recherche de valeurs Null à l’aide d’extraits de flux de données

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En utilisant des extraits de code dans les flux de données de mappage, vous pouvez très facilement effectuer des tâches courantes telles que la déduplication des données et le filtrage de valeurs Null. Ce guide pratique explique comment ajouter facilement ces fonctions à vos pipelines à l’aide d’extraits de script de flux de données.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Créer un pipeline

1. Sélectionnez **+Nouveau pipeline** pour créer un pipeline.

2. Ajoutez une activité de flux de données.

3. Ajouter une transformation source et la connecter à l’un de vos jeux de données.

    ![Extrait de code source 2](media/data-flow/snippet-adf-2.png)

4. Les extraits de code de vérification de la déduplication et de la valeur NULL utilisent des modèles génériques qui tirent parti de la dérive du schéma de flux de données, de sorte qu’ils fonctionnent avec n’importe quel schéma de votre jeu de données, ou avec des jeux de données qui n’ont pas de schéma prédéfini.

5. [Accédez à la page de documentation du script de flux de données et copiez l’extrait de code pour les lignes distinctes.](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. Dans l’interface utilisateur du concepteur de flux de données, cliquez sur le bouton Script dans le coin supérieur droit pour ouvrir l’éditeur de script derrière le graphique de flux de données.

    ![Extrait de code source 3](media/data-flow/snippet-adf-3.png)

7. Après la définition de ```source1``` dans votre script, appuyez sur Entrée, puis collez-le dans l’extrait de code.

8. Vous allez connecter cet extrait de code collé à la transformation source précédente que vous avez créée dans le graphique en tapant « source1 » devant le code collé.

9. Vous pouvez également connecter la nouvelle transformation dans le concepteur en sélectionnant le flux entrant à partir du nouveau nœud de transformation dans le graphique.

    ![Extrait de code source 4](media/data-flow/snippet-adf-4.png)

10. À présent, votre flux de données supprime les doublons de lignes de votre source à l’aide de la transformation d’agrégation qui regroupe toutes les lignes à l’aide d’un hachage général pour toutes les valeurs de colonne.
    
11. Ensuite, nous allons ajouter un extrait de code pour fractionner vos données en un flux qui contient des lignes avec des valeurs Null et un flux qui n’a pas de valeur Null.

12. [Revenez à la bibliothèque d’extraits de code et cette fois copiez le code pour les contrôles de valeur Null.](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. Dans le concepteur de flux de données, cliquez de nouveau sur Script et collez ce nouveau code de transformation en bas. Connectez-le à votre transformation précédente en tapant le nom de cette transformation devant l’extrait collé.

14. Votre graphique de flux de données doit maintenant ressembler à ceci :

    ![Extrait de code source 1](media/data-flow/snippet-adf-1.png)

  Vous disposez maintenant d’un flux de données de travail avec des déduplications génériques et des contrôles de valeur Null en récupérant les extraits de code existants de la bibliothèque de scripts de Data Flow et en les ajoutant à votre conception existante.

## <a name="next-steps"></a>Étapes suivantes

* Créez le reste de votre logique de flux de données à l’aide de [transformations](concepts-data-flow-overview.md) de flux de données de mappage.
