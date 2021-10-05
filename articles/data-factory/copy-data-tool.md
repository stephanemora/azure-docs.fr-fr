---
title: Outil Copier des données
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez l’outil Copier des données dans Azure Data Factory et Azure Synapse Analytics.
author: dearandyxu
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: yexu
ms.openlocfilehash: d4ae44d3c3121ff5cc0240ea9dac0ed11e1580ce
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760296"
---
# <a name="copy-data-tool-in-azure-data-factory-and-synapse-analytics"></a>Outil Copier des données dans Azure Data Factory et Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L’outil Copier des données facilite et optimise le processus de réception des données dans Azure Data Lake, qui est généralement la première étape dans un scénario d’intégration des données de bout en bout.  Il permet de gagner du temps, surtout lorsque vous utilisez le service pour réceptionner des données à partir d’une source de données pour la première fois. Voici certains des avantages de l’utilisation de cet outil :

- Lorsque vous utilisez l’outil Copier des données , vous n’avez pas besoin de comprendre les définitions de service pour les services, jeux de données, pipelines, activités et déclencheurs liés. 
- Le flux de l’outil Copier des données est intuitif pour le chargement des données dans Data Lake. L’outil crée automatiquement toutes les ressources nécessaires pour copier des données depuis le magasin de données source sélectionné vers le magasin de données de destination/récepteur sélectionné. 
- L’outil Copier des données vous permet de valider les données en cours de réception au moment de la création, et d’éviter ainsi des erreurs potentielles en amont.
- Si vous devez implémenter une logique métier complexe pour charger des données dans Data Lake, vous pouvez toujours modifier les ressources créées par l’outil Copier des données à l’aide de la création par activité dans l’interface utilisateur. 

Le tableau suivant fournit des instructions concernant l’utilisation de Copier des données par rapport à la création par activité dans l’interface utilisateur : 

| Outil Copier des données | Création par activité (activité de copie) |
| -------------- | -------------------------------------- |
| Vous souhaitez créer facilement une tâche de chargement des données sans connaître en détail les entités (services, jeux de données, pipelines liés, etc.). | Vous souhaitez implémenter une logique complexe et flexible pour charger des données dans Data Lake. |
| Vous souhaitez charger rapidement un grand nombre d’artefacts de données dans Data Lake. | Vous souhaitez lier l’activité de copie à d’autres activités suivantes pour le nettoyage ou le traitement des données. |

Pour démarrer l’outil Copier des données, cliquez sur la mosaïque **Ingérer** sur la page d’accueil de votre interface utilisateur Data Factory ou Synapse Studio.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).
:::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Capture d’écran qui montre la page d’accueil - lien vers l’outil Copier des données.":::
# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
:::image type="content" source="./media/doc-common-process/get-started-page-synapse.png" alt-text="Capture d’écran qui montre la page d’accueil - lien vers l’outil Copier des données.":::

---
Après avoir lancé l’outil Copier des données, vous verrez deux types de tâches : **la tâche de copie intégrée** et **la tâche de copie basée sur les métadonnées**. La tâche de copie intégrée vous permet de créer un pipeline en moins de cinq minutes pour répliquer les données sans en apprendre plus sur les entités.  La tâche de copie basée sur les métadonnées facilite la création de pipelines paramétrables et d’une table de contrôle externe afin de pouvoir copier de grandes quantités d’objets (par exemple, des milliers de tables) à l’échelle. Vous pouvez voir plus de détails dans [Copie de données basée sur les métadonnées](copy-data-tool-metadata-driven.md).

## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Flux intuitif pour le chargement des données dans Data Lake
Cet outil vous permet de déplacer facilement et en quelques minutes des données provenant de nombreuses sources différentes vers des destinations, à l’aide d’un flux intuitif :  

1. Configurez les paramètres pour la **source**.
2. Configurez les paramètres pour la **destination**. 
3. Configurez les **paramètres avancés** pour l’opération de copie, notamment le mappage de colonnes, les paramètres de performances et les paramètres de tolérance de panne. 
4. Spécifiez une **planification** pour les tâche de chargement des données. 
5. Examinez le **résumé** des entités à créer. 
6. **Modifiez** le pipeline pour mettre à jour les paramètres de l’activité de copie, selon les besoins. 

   La conception de l’outil est particulièrement adaptée au Big Data, avec la prise en charge de divers types d’objet et de données. Vous pouvez l’utiliser pour déplacer des centaines de dossiers, fichiers ou tables. L’outil prend en charge l’aperçu des données automatique, la capture et le mappage automatique de schéma, ainsi que le filtrage des données.

:::image type="content" source="./media/copy-data-tool/copy-data-tool.png" alt-text="Outil Copier des données":::

## <a name="automatic-data-preview"></a>Aperçu des données automatique
Vous pouvez afficher un aperçu d’une partie des données à partir du magasin de données source sélectionné et valider ainsi les données copiées. En outre, si les données sources sont contenues dans un fichier texte, l’outil Copier des données analyse ce fichier pour détecter les délimiteurs de colonnes et de lignes ainsi que le schéma.

:::image type="content" source="./media/copy-data-tool/file-format-settings.png" alt-text="Paramètres du fichier":::

Après la détection, sélectionnez **Aperçu des données**:

:::image type="content" source="./media/copy-data-tool/after-detection.png" alt-text="Paramètres du fichier détecté et aperçu":::

## <a name="schema-capture-and-automatic-mapping"></a>Capture et mappage automatique du schéma
Souvent, le schéma de la source de données n’est peut-être pas identique au schéma de la destination de données. Le cas échéant, vous devez mapper les colonnes du schéma source aux colonnes du schéma de destination.

L’outil Copier des données surveille votre comportement et s’y adapte lorsque vous mappez des colonnes entre des magasins source et destination. Après avoir sélectionné une ou plusieurs colonnes à partir du magasin de données source et les avoir mappées au schéma de destination, l’outil Copier des données commence à analyser le modèle pour les paires de colonnes que vous avez choisies des deux côtés. Il applique ensuite le même modèle aux autres colonnes. Par conséquent, quelques clics suffisent pour afficher comme vous le souhaitez toutes les colonnes mappées vers la destination.  Si vous n’êtes pas satisfait du choix du mappage de colonnes fourni par l’outil Copier des données, vous pouvez l’ignorer et continuer en mappant manuellement les colonnes. Pendant ce temps, l’outil Copier des données assimile et met à jour continuellement le modèle jusqu’à obtenir le modèle adapté au mappage de colonnes que vous voulez. 

> [!NOTE]
> Lors de la copie de données depuis SQL Server ou Azure SQL Database vers Azure Synapse Analytics, si la table n’existe pas dans le magasin de destination, l’outil Copier des données prend en charge la création automatique de la table à l’aide du schéma source. 

## <a name="filter-data"></a>Filtrer les données
Vous pouvez filtrer les données sources pour sélectionner uniquement celles qui doivent être copiées vers le magasin de données de récepteur. Le filtrage réduit le volume des données à copier vers le magasin de données de récepteur et améliore de ce fait le débit de la copie. L’outil Copier des données fournit un moyen souple de filtrer les données dans une base de données relationnelle à l’aide du langage de requête SQL ou les fichiers d’un dossier d’objets blob Azure. 

### <a name="filter-data-in-a-database"></a>Filtrer les données d’une base de données
La capture d’écran suivante montre une requête SQL permettant de filtrer les données.

:::image type="content" source="./media/copy-data-tool/filter-data-in-database.png" alt-text="Filtrer les données d’une base de données":::

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrer les données dans un dossier d’objets blob Azure
Vous pouvez utiliser des variables dans le chemin d’accès au dossier pour copier des données à partir d’un dossier. Les variables prises en charge sont les suivantes : **{year}** , **{month}** , **{day}** , **{hour}** et **{minute}** . Exemple : inputfolder/{year}/{month}/{day}. 

Supposons que vos dossiers d’entrée présentent le format suivant : 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Cliquez sur le bouton **Parcourir** à côté de **Fichier ou dossier**, accédez à l’un de ces dossiers (par exemple, 2016->03->01->02), puis cliquez sur **Choisir**. Vous devez voir 2016/03/01/02 dans la zone de texte. 

Puis remplacez **2016** par **{year}** , **03** par **{month}** , **01** par **{day}** et **02** par **{hour}** , puis appuyez sur la touche de **tabulation**. Lorsque vous sélectionnez **charge incrémentielle : noms de fichiers/dossiers partitionnés** dans la section **Comportement de chargement de fichier** et sélectionnez **Panification** ou **Fenêtre bascule** sur page **Propriétés** , vous devez voir les listes déroulantes pour sélectionner le format de ces quatre variables :

:::image type="content" source="./media/copy-data-tool/filter-file-or-folder.png" alt-text="Filtrer un fichier ou un dossier":::

L’outil Copier des données génère des paramètres avec des expressions, fonctions et variables système qui peuvent être utilisés pour représenter les variables {year}, {month}, {day}, {hour} et {minute} lors de la création du pipeline.

## <a name="scheduling-options"></a>Options de planification
Vous pouvez effectuer l’opération de copie une seule fois ou la répéter selon une planification établie (horaire, quotidienne et ainsi de suite.). Ces options peuvent être utilisées pour les connecteurs entre différents environnements, notamment les environnements locaux, dans le cloud ou sur un bureau local. 

Une opération de copie unique ne permet le déplacement des données à d’une source vers une destination qu’une seule fois. Elle s’applique aux données de toute taille et de n’importe quel format pris en charge. La copie planifiée vous permet de copier des données selon la récurrence de votre choix. Vous pouvez utiliser les paramètres avancés (nouvelle tentative, délai d’attente, alertes, etc.) pour configurer la copie planifiée.

:::image type="content" source="./media/copy-data-tool/scheduling-options.png" alt-text="Options de planification":::


## <a name="next-steps"></a>Étapes suivantes
Essayez ces didacticiels qui utilisent l’outil Copier des données :

- [Démarrage rapide : créer une fabrique de données à l’aide de l’outil Copier des données](quickstart-create-data-factory-copy-data-tool.md)
- [Didacticiel : copier des données dans Azure à l’aide de l’outil Copier des données](tutorial-copy-data-tool.md) 
- [Didacticiel : copier des données locales dans Azure à l’aide de l’outil Copier des données](tutorial-hybrid-copy-data-tool.md)
