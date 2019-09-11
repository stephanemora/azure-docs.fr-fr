---
title: Traiter des fichiers texte de longueur fixe avec des flux de données de mappage dans Azure Data Factory
description: Découvrez comment traiter des fichiers texte de longueur fixe dans Azure Data Factory à l’aide de flux de données de mappage.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: e1ba09f459152616941071c23f7a6545ec2a8b73
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210507"
---
# <a name="process-fixed-length-text-files-using-data-factory-mapping-data-flows"></a>Traiter des fichiers texte de longueur fixe à l’aide de flux de données de mappage Data Factory

Les flux de données de mappage Data Factory prennent en charge les données de transformation issues de fichiers texte de largeur fixe. Vous allez définir un jeu de données pour un fichier texte sans délimiteur, puis configurer des divisions de sous-chaînes en fonction de la position ordinale.

## <a name="create-a-pipeline"></a>Créer un pipeline

1. Accédez à **+Nouveau pipeline** pour démarrer un nouveau pipeline

2. Ajoutez une activité de flux de données qui sera utilisée pour le traitement des fichiers de largeur fixe

  ![Pipeline de largeur fixe](media/data-flow/fwpipe.png)

3. Dans l’activité de flux de données, sélectionnez Nouveau flux de données de mappage

4. Ajoutez une transformation source, une colonne dérivée, une transformation de sélection et une transformation de réception

  ![Flux de données de largeur fixe](media/data-flow/fw2.png)

5. Configurez la transformation source pour utiliser un nouveau jeu de données de type Texte délimité

6. Ne définissez aucun délimiteur de colonne ni en-tête

Nous allons simplement définir des points de départ et des longueurs de champ pour le contenu de ce fichier :

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

7. Sous l’onglet Projection de votre transformation source, vous devez voir une colonne de chaîne appelée « Column_1 »

8. À présent, dans la colonne dérivée, créez une nouvelle colonne

9. Nous allons fournir des noms simples aux colonnes, tels que col1

10. Ensuite, dans le générateur d’expressions, tapez :

  ```substring(Column_1,1,4)```

  ![Colonne dérivée](media/data-flow/fwderivedcol1.png)

10. Répétez cette opération pour toutes les colonnes que vous devez analyser

12. Cliquez sur l’onglet Inspecter pour afficher les nouvelles colonnes qui seront générées

  ![Inspecter](media/data-flow/fwinspect.png)

13. Utilisez la transformation de sélection (Select) pour supprimer les colonnes dont vous n’avez pas besoin pour la transformation

  ![Transformation de sélection](media/data-flow/fwselect.png)

14. Enfin, utilisez le récepteur (Sink) pour générer les données dans un dossier :

  ![Récepteur de largeur fixe](media/data-flow/fwsink.png)

  La sortie se présente ainsi :

  ![Sortie de largeur fixe](media/data-flow/fxdoutput.png)

  Les données de largeur fixe sont maintenant divisées en groupes de quatre caractères et affectées à Col1, Col2, Col3, Col4, ... Sur la base de l’exemple ci-dessus, je divise les données en 4 colonnes

## <a name="next-steps"></a>Étapes suivantes

* Créer le reste de votre logique de flux de données à l’aide de [transformations](concepts-data-flow-overview.md) de flux de données de mappage
