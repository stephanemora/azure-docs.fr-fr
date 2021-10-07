---
title: Traiter des fichiers texte de longueur fixe avec des flux de données de mappage dans Azure Data Factory
description: Découvrez comment traiter des fichiers texte de longueur fixe dans Azure Data Factory à l’aide de flux de données de mappage.
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 58bd77eb815a510328c49ca7c614b0bc680a4b94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571425"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Traiter des fichiers texte de longueur fixe à l’aide de flux de données de mappage Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Vous pouvez transformer les données de fichiers texte de longueur fixe en utilisant des flux de données de mappage dans Microsoft Azure Data Factory. Dans le cadre de la tâche suivante, nous allons définir un jeu de données pour un fichier texte sans délimiteur, puis configurer des divisions de sous-chaînes en fonction de la position ordinale.

## <a name="create-a-pipeline"></a>Créer un pipeline

1. Sélectionnez **+Nouveau pipeline** pour créer un pipeline.

2. Ajoutez une activité de flux de données qui sera utilisée pour le traitement des fichiers de longueur fixe :

    :::image type="content" source="media/data-flow/fwpipe.png" alt-text="Pipeline de largeur fixe":::

3. Dans l’activité de flux de données, sélectionnez **Nouveau flux de données de mappage**.

4. Ajoutez une transformation de source, de colonne dérivée, de sélection et de récepteur :

    :::image type="content" source="media/data-flow/fw2.png" alt-text="Flux de données de largeur fixe":::

5. Configurez la transformation de source pour utiliser un nouveau jeu de données de type Texte délimité.

6. Ne définissez pas d’en-tête ou de délimiteur de colonne.

   Nous allons à présent définir des points de départ et des longueurs de champ pour le contenu de ce fichier :

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. Sous l’onglet **Projection** de votre transformation de source, vous devez voir une colonne de chaîne nommée *Column_1*.

8. Créez une colonne dans la colonne dérivée.

9. Nous allons donner des noms simples aux colonnes, tels que *col1*.

10. Dans le générateur d’expressions, tapez la chaîne suivante :

    `substring(Column_1,1,4)`

    :::image type="content" source="media/data-flow/fwderivedcol1.png" alt-text="colonne dérivée":::

11. Répétez l’étape 10 pour toutes les colonnes que vous devez analyser.

12. Sélectionnez l’onglet **Inspecter** pour afficher les nouvelles colonnes qui seront générées :

    :::image type="content" source="media/data-flow/fwinspect.png" alt-text="inspecter":::

13. Utilisez la transformation de sélection (Select) pour supprimer les colonnes dont vous n’avez pas besoin pour la transformation :

    :::image type="content" source="media/data-flow/fwselect.png" alt-text="transformation de sélection":::

14. Utilisez le récepteur (Sink) pour générer les données dans un dossier :

    :::image type="content" source="media/data-flow/fwsink.png" alt-text="Récepteur de largeur fixe":::

    Voici à quoi ressemble la sortie :

    :::image type="content" source="media/data-flow/fxdoutput.png" alt-text="Sortie de largeur fixe":::

  Les données de longueur fixe sont maintenant divisées en groupes de quatre caractères et attribuées à Col1, Col2, Col3, Col4, etc. Les données sont divisées en quatre colonnes, conformément à l’exemple précédent.

## <a name="next-steps"></a>Étapes suivantes

* Créez le reste de votre logique de flux de données à l’aide de [transformations](concepts-data-flow-overview.md) de flux de données de mappage.
