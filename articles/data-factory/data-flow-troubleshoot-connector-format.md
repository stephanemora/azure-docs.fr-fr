---
title: Résoudre les problèmes liés aux connecteurs et à la mise en forme dans le flux de données de mappage
description: Découvrez comment résoudre les problèmes de flux de données liés aux connecteurs et à la mise en forme dans Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 05/24/2021
ms.openlocfilehash: 1dbbbc76cb67adb678cc557c4193c0a25f280540
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952953"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Résoudre les problèmes liés aux connecteurs et à la mise en forme dans le flux de données de mappage dans Azure Data Factory


Cet article explore les méthodes de résolution des problèmes liés aux connecteurs et à la mise en forme pour les flux de données de mappage dans Azure Data Factory (ADF).


## <a name="cosmos-db--json"></a>Cosmos DB & JSON

### <a name="support-customized-schemas-in-the-source"></a>Prendre en charge les schémas personnalisés dans la source

#### <a name="symptoms"></a>Symptômes
Lorsque vous souhaitez utiliser le flux de données ADF pour déplacer ou transférer des données à partir de Cosmos DB/JSON vers d’autres magasins de données, certaines colonnes des données sources peuvent être ignorées. 

#### <a name="cause"></a>Cause 
Pour les connecteurs sans schéma (le numéro de colonne, le nom de colonne et le type de données de colonne de chaque ligne peuvent être différents lors de la comparaison avec d’autres), par défaut, ADF utilise un échantillon de lignes (par exemple les données des 100 ou 1000 premières lignes) pour déduire le schéma, et le résultat déduit sera utilisé comme schéma pour lire les données. Par conséquent, si vos magasins de données ont des colonnes supplémentaires qui n’apparaissent pas dans les échantillons de lignes, les données de ces colonnes supplémentaires ne sont pas lues, déplacées ou transférées dans les magasins de données du récepteur.

#### <a name="recommendation"></a>Recommandation
Pour remplacer le comportement par défaut et importer des champs supplémentaires, ADF fournit des options pour vous permettre de personnaliser le schéma source. Vous pouvez spécifier des colonnes supplémentaires/ignorées qui peuvent être manquantes dans le résultat déduit du schéma dans la projection source du flux de données afin de lire les données, et vous pouvez appliquer l’une des options suivantes pour définir le schéma personnalisé. En règle générale, l’**option 1** est préférable.

- **Option 1** : comparé aux données sources d’origine qui peuvent être un fichier, une table ou un conteneur volumineux contenant des millions de lignes avec des schémas complexes, vous pouvez créer une table/conteneur temporaire avec quelques lignes qui contiennent toutes les colonnes que vous souhaitez lire, puis passer à l’opération suivante : 

    1. Utilisez les **paramètres de débogage** de la source de flux de données pour effectuer une **projection d’importation** avec des exemples de fichiers/tables afin d’obtenir le schéma complet. Vous pouvez suivre les étapes décrites dans l’image suivante :<br/>

        ![Capture d’écran qui montre la première partie de la première option pour personnaliser le schéma source.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. Sélectionnez **Paramètres de débogage** dans le canevas du flux de données.
         1. Dans le volet contextuel, sélectionnez **Exemple de table** sous l’onglet **cosmosSource**, puis entrez le nom de votre table dans le bloc **Table**.
         1. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.
         1. Sélectionnez **Importer la projection**.<br/>  
    
    1. Remodifiez les **paramètres de débogage** de façon à utiliser le jeu de données source pour le déplacement/la transformation des données restantes. Vous pouvez passer à la procédure indiquée dans l’image suivante :<br/>

        ![Capture d’écran qui montre la deuxième partie de la première option pour personnaliser le schéma source.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. Sélectionnez **Paramètres de débogage** dans le canevas du flux de données.
         1. Dans le volet contextuel, sélectionnez **Jeu de données source** sous l’onglet **cosmosSource**.
         1. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.<br/>
    
    Par la suite, le runtime de flux de données ADF honorera et utilisera le schéma personnalisé pour lire les données à partir du magasin de données d’origine. <br/>

- **Option 2** : si vous connaissez le schéma et le langage DSL des données sources, vous pouvez mettre à jour manuellement le script source du flux de données afin d’ajouter des colonnes supplémentaires/ignorées pour lire les données. Un exemple est présenté dans l’image suivante : 

    ![Capture d’écran qui montre la deuxième option pour personnaliser le schéma source.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

### <a name="consume-json-files-generated-by-copy-activities"></a>Consommer les fichiers JSON générés par les activités de copie

#### <a name="symptoms"></a>Symptômes

Si vous utilisez l’activité de copie pour générer des fichiers JSON, puis essayez de lire ces fichiers dans des flux de données, vous échouez avec le message d’erreur suivant : `JSON parsing error, unsupported encoding or multiline`.

#### <a name="cause"></a>Cause

Les limitations suivantes s’appliquent à JSON pour les copies et les flux de données respectivement :

- Pour les fichiers JSON encodés en Unicode (UTF-8, UTF-16, UTF-32), les activités de copie génèrent toujours les fichiers JSON avec une marque d’ordre d’octet.
- La source JSON du flux de données avec l’option « Document individuel » activée ne prend pas en charge l’encodage Unicode avec marque d’ordre d’octet.

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/enabled-single-document.png" alt-text="Capture d’écran montrant l’option « Document individuel » activée."::: 


Vous rencontrerez donc des problèmes si les critères suivants sont réunis :

- Le jeu de données de récepteur utilisé par l’activité de copie est défini sur « Encodage Unicode » (UTF-8, UTF-16, UTF-16BE, UTF-32, UTF-32BE), ou la valeur par défaut est utilisée.
- Le récepteur de copie est configuré pour utiliser le modèle de fichier « Tableau d’objets », comme illustré dans l’image suivante, que l’option « Document individuel » soit activée ou non dans la source JSON du flux de données. 

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/array-of-objects-pattern.png" alt-text="Capture d’écran montrant le modèle « Tableau d’objets » défini."::: 
   
#### <a name="recommendation"></a>Recommandation

- Utilisez toujours le modèle de fichier par défaut ou le modèle « Jeu d’objets » explicite dans le récepteur de copie si les fichiers générés sont utilisés dans des flux de données.
- Désactivez l’option « Document individuel » dans la source JSON du flux de données.

>[!Note]
> L’utilisation de l’option « Jeux d’objets » est également la pratique recommandée du point de vue des performances. Étant donné que l’option JSON « Document individuel » dans le flux de données ne permet pas la lecture parallèle de fichiers individuels volumineux, cette recommandation n’a pas d’impact négatif.

### <a name="the-query-with-parameters-does-not-work"></a>La requête avec paramètres ne fonctionne pas

#### <a name="symptoms"></a>Symptômes

Les flux de données de mappage dans Azure Data Factory prennent en charge l’utilisation de paramètres. Les valeurs des paramètres sont définies par le pipeline appelant via l’activité Exécuter un flux de données, et l’utilisation de paramètres rend vos flux de données polyvalents, flexibles et réutilisables. Vous pouvez paramétrer les paramètres de flux de données et les expressions à l’aide de ces paramètres : [Paramétrage de flux de données de mappage](./parameters-data-flow.md).

Après que vous ayez défini des paramètres et les ayez utilisés dans la requête de la source du flux de données, ils ne prennent pas effet.

#### <a name="cause"></a>Cause

Vous rencontrez cette erreur en raison d’une configuration incorrecte.

#### <a name="recommendation"></a>Recommandation

Utilisez les règles suivantes pour définir les paramètres de la requête. Pour obtenir des informations plus détaillées, reportez-vous à [Générer des expressions dans un flux de données de mappage](./concepts-data-flow-expression-builder.md).

1. Appliquez des guillemets doubles au début de l’instruction SQL.
2. Utilisez des guillemets simples autour du paramètre.
3. Utilisez des lettres minuscules pour toutes les instructions CLAUSE.

Par exemple :

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/set-parameter-in-query.png" alt-text="Capture d’écran montrant le paramètre défini dans la requête."::: 

## <a name="cdm"></a>CDM

### <a name="modeljson-files-with-special-characters"></a>Fichiers Model.Json contenant des caractères spéciaux

#### <a name="symptoms"></a>Symptômes 
Vous pouvez rencontrer un problème selon lequel le nom final du fichier model.json contient des caractères spéciaux.  

#### <a name="error-message"></a>Message d’erreur  
`at Source 'source1': java.lang.IllegalArgumentException: java.net.URISyntaxException: Relative path in absolute URI: PPDFTable1.csv@snapshot=2020-10-21T18:00:36.9469086Z. ` 

#### <a name="recommendation"></a>Recommandation  
Remplacez les caractères spéciaux dans le nom du fichier, ce qui fonctionnera dans Synapse, mais pas dans ADF.  

### <a name="no-data-output-in-the-data-preview-or-after-running-pipelines"></a>Aucune sortie de données dans l’aperçu des données ou après l’exécution des pipelines

#### <a name="symptoms"></a>Symptômes
Lorsque vous utilisez le manifest.json pour CDM, aucune donnée n’est affichée dans l’aperçu des données ou après l’exécution d’un pipeline. Seuls les en-têtes sont affichés. Vous pouvez voir ce problème dans l’image ci-dessous.<br/>

![Capture d’écran montrant le symptôme « Aucune sortie de données ».](./media/data-flow-troubleshoot-connector-format/no-data-output.png)

#### <a name="cause"></a>Cause
Le document manifeste décrit le dossier CDM, par exemple, les entités que vous avez dans le dossier, les références de ces entités et les données qui correspondent à cette instance. Il manque à votre document manifeste l’information `dataPartitions` qui indique à ADF où lire les données et, dans la mesure où elle est vide, aucune donnée n’est renvoyée. 

#### <a name="recommendation"></a>Recommandation
Mettez à jour votre document manifeste afin d’y inclure l’information `dataPartitions`. Vous pouvez vous référer à cet exemple de document manifeste pour mettre à jour votre document : [Métadonnées Common Data Model : introduction au manifeste ; section Exemple de document manifeste](/common-data-model/cdm-manifest#example-manifest-document).

### <a name="json-array-attributes-are-inferred-as-separate-columns"></a>Les attributs des tableaux JSON sont considérés comme des colonnes distinctes

#### <a name="symptoms"></a>Symptômes 
Vous pouvez rencontrer un problème où un attribut (type chaîne) de l’entité CDM a un tableau JSON comme données. Lorsque ces données sont rencontrées, ADF les identifie de manière incorrecte comme des colonnes distinctes. Comme vous pouvez le voir dans les images suivantes, un seul attribut présenté dans la source (msfp_otherproperties) est considéré comme une colonne distincte dans l’aperçu du connecteur CDM.<br/> 

- Dans les données sources du CSV (reportez-vous à la deuxième colonne) : <br/>

    ![Capture d’écran montrant l’attribut dans les données sources du CSV.](./media/data-flow-troubleshoot-connector-format/json-array-csv.png)

- Dans l’aperçu des données sources CDM : <br/>

    ![Capture d’écran montrant la colonne distincte dans les données sources CDM.](./media/data-flow-troubleshoot-connector-format/json-array-cdm.png)

 
Vous pouvez également essayer de mapper les colonnes dérivées et utiliser l’expression de flux de données pour transformer cet attribut en tableau. Toutefois, étant donné que cet attribut est lu comme une colonne distincte lors de la lecture, la transformation en tableau ne fonctionne pas.  

#### <a name="cause"></a>Cause
Ce problème est probablement dû à des virgules dans la valeur de votre objet JSON pour cette colonne. Comme votre fichier de données est censé être un fichier CSV, la virgule indique qu’il s’agit de la fin de la valeur d’une colonne. 

#### <a name="recommendation"></a>Recommandation
Pour résoudre ce problème, vous devez placer votre colonne JSON entre guillemets doubles et éviter les guillemets internes avec une barre oblique inverse (`\`). De cette façon, le contenu de la valeur de cette colonne peut être lu comme une seule colonne.  
  
>[!Note]
>CDM n’indique pas que le type de données de la valeur de colonne est JSON, mais il informe qu’il s’agit d’une chaîne et qu’elle est analysée comme telle.

### <a name="unable-to-fetch-data-in-the-data-flow-preview"></a>Impossible d’extraire des données dans l’aperçu du flux de données

#### <a name="symptoms"></a>Symptômes
Vous utilisez CDM avec un model.json généré par Power BI. Lorsque vous prévisualisez les données CDM à l’aide de l’aperçu du flux de données, vous rencontrez l’erreur suivante : `No output data.`.

#### <a name="cause"></a>Cause
 Le code suivant existe dans les partitions du fichier model.json généré par le flux de données Power BI.
```json
"partitions": [  
{  
"name": "Part001",  
"refreshTime": "2020-10-02T13:26:10.7624605+00:00",  
"location": "https://datalakegen2.dfs.core.windows.net/powerbi/salesEntities/salesPerfByYear.csv @snapshot=2020-10-02T13:26:10.6681248Z"  
}  
```
Pour ce fichier model.json, le problème est que le schéma d’affectation de noms du fichier de partition de données comporte des caractères spéciaux et que les chemins d’accès des fichiers sous-jacents avec « @ » n’existent pas actuellement.  

#### <a name="recommendation"></a>Recommandation
Supprimez la partie `@snapshot=2020-10-02T13:26:10.6681248Z` du nom de fichier de la partition de données et du fichier model.json, puis réessayez. 

### <a name="the-corpus-path-is-null-or-empty"></a>Le chemin d’accès du corpus est null ou vide

#### <a name="symptoms"></a>Symptômes
Quand vous utilisez CDM dans le flux de données avec le format de modèle, vous ne pouvez pas prévisualiser les données, et vous rencontrez l’erreur suivante : `DF-CDM_005 The corpus path is null or empty`. L’erreur est illustrée dans l’image suivante :  

![Capture d’écran montrant l’erreur de chemin d’accès du corpus.](./media/data-flow-troubleshoot-connector-format/corpus-path-error.png)

#### <a name="cause"></a>Cause
Le chemin d’accès de votre partition de données dans le model.json pointe vers un emplacement de stockage blob et non vers votre lac de données. L’emplacement doit avoir l’URL de base **.dfs.core.windows.net** pour ADLS Gen2. 

#### <a name="recommendation"></a>Recommandation
Pour résoudre ce problème, vous pouvez vous référer à cet article : [ADF ajoute la prise en charge des jeux de données inlined et de Common Data Model aux flux de données](https://techcommunity.microsoft.com/t5/azure-data-factory/adf-adds-support-for-inline-datasets-and-common-data-model-to/ba-p/1441798). L’illustration suivante montre la façon de corriger l’erreur de chemin d’accès du corpus dans cet article.

![Capture d’écran montrant comment corriger l’erreur de chemin d’accès du corpus.](./media/data-flow-troubleshoot-connector-format/fix-format-issue.png)

### <a name="unable-to-read-csv-data-files"></a>Impossible de lire les fichiers de données CSV

#### <a name="symptoms"></a>Symptômes 
Vous utilisez le jeu de données inlined comme modèle de données commun avec le manifeste comme source, et vous avez fourni le fichier manifeste d’entrée, le chemin d’accès racine et le nom et le chemin d’accès de l’entité. Dans le manifeste, vous disposez des partitions de données avec l’emplacement du fichier CSV. Pendant ce temps, le schéma d’entité et le schéma CSV sont identiques et toutes les validations ont réussi. Toutefois, dans l’aperçu des données, seul le schéma est chargé, et non les données, et ces dernières sont invisibles, comme le montre l’image suivante :

![Capture d’écran montrant l’impossibilité de lire les fichiers de données.](./media/data-flow-troubleshoot-connector-format/unable-read-data.png)

#### <a name="cause"></a>Cause
Votre dossier CDM n’est pas séparé en modèles logiques et physiques, et seuls les modèles physiques existent dans le dossier CDM. Les deux articles suivants décrivent la différence : [Définitions logiques](/common-data-model/sdk/logical-definitions) et [Résolution d’une définition d’entité logique](/common-data-model/sdk/convert-logical-entities-resolved-entities).<br/> 

#### <a name="recommendation"></a>Recommandation
Pour le flux de données utilisant CDM comme source, essayez d’utiliser un modèle logique comme référence d’entité, et utilisez le manifeste qui décrit l’emplacement des entités physiques résolues et les emplacements des partitions de données. Vous pouvez voir quelques exemples de définitions d’entités logiques dans le référentiel public CDM de GitHub : [CDM-schemaDocuments](https://github.com/microsoft/CDM/tree/master/schemaDocuments).<br/>

Un bon point de départ pour la création de votre corpus consiste à copier les fichiers dans le dossier de documents de schéma (juste ce niveau dans le référentiel GitHub) et à placer ces fichiers dans un dossier. Ensuite, vous pouvez utiliser l’une des entités logiques prédéfinies dans le référentiel (comme point de départ ou de référence) pour créer votre modèle logique.<br/>

Une fois le corpus configuré, il est recommandé d’utiliser CDM comme récepteur dans les flux de données, afin qu’un dossier CDM bien formé puisse être correctement créé. Vous pouvez utiliser votre jeu de données CSV comme source, puis l’intégrer au modèle CDM que vous avez créé.

## <a name="delta"></a>Delta

### <a name="the-sink-does-not-support-the-schema-drift-with-upsert-or-update"></a>Le récepteur ne prend pas en charge la dérive de schéma avec un upsert ou une mise à jour

#### <a name="symptoms"></a>Symptômes
Vous pouvez être confronté au problème où le récepteur delta dans les flux de données de mappage ne prend pas en charge la dérive de schéma avec un upsert ou une mise à jour. Le problème est que la dérive de schéma ne fonctionne pas lorsque le delta est la cible dans un flux de données de mappage et que l’utilisateur configure un upsert ou une mise à jour. 

Si une colonne est ajoutée à la source après une charge « initiale » dans le delta, les travaux suivants échouent avec une erreur indiquant qu’il est impossible de trouver la nouvelle colonne, ce qui se produit lorsque vous faites un upsert ou une mise à jour à l’aide de la modification de ligne. Cela semble fonctionner uniquement pour les insertions.

#### <a name="error-message"></a>Message d’erreur
`DF-SYS-01 at Sink 'SnkDeltaLake': org.apache.spark.sql.AnalysisException: cannot resolve target.BICC_RV in UPDATE clause given columns target. `

#### <a name="cause"></a>Cause
Il s’agit d’un problème de format delta en raison de la limitation de la bibliothèque delta d’E/S utilisée dans le runtime du flux de données. Ce problème est toujours en cours de résolution.

#### <a name="recommendation"></a>Recommandation
Pour résoudre ce problème, vous devez d’abord mettre à jour le schéma, puis écrire les données. Procédez comme suit : <br/>
1. Créez un seul flux de données qui comprend un récepteur delta en insertion seule avec l’option de fusion de schéma pour mettre à jour le schéma. 
1. Après l’étape 1, utilisez Supprimer/Faire un upsert/Mettre à jour pour modifier le récepteur cible sans modifier le schéma. <br/>

## <a name="azure-postgresql"></a>Azure PostgreSQL

### <a name="encounter-an-error-failed-with-exception-handshake_failure"></a>Erreur : Échec avec l’exception : handshake_failure 

#### <a name="symptoms"></a>Symptômes
Vous utilisez Azure PostgreSQL comme source ou récepteur dans le flux de données, par exemple l’aperçu des données et le débogage ou le déclenchement de l’exécution, et vous pouvez constater que la travail échoue avec le message d’erreur suivant : 

   `PSQLException: SSL error: Received fatal alert: handshake_failure `<br/>
   `Caused by: SSLHandshakeException: Received fatal alert: handshake_failure.`

#### <a name="cause"></a>Cause 
Si vous utilisez le serveur flexible ou Hyperscale (Citus) pour votre serveur Azure PostgreSQL, puisque le système est créé via Spark sur le cluster Azure Databricks, il y a une limitation où Azure Databricks bloque notre système pour se connecter au serveur flexible ou Hyperscale (Citus). Vous pouvez consulter les deux liens suivants à titre de référence.
- [Handshake fails trying to connect from Azure Databricks to Azure PostgreSQL with SSL](/answers/questions/170730/handshake-fails-trying-to-connect-from-azure-datab.html) (L’établissement d’une liaison échoue lors de la tentative de connexion d’Azure Databricks à Azure PostgreSQL avec SSL)
 
- [MCW-Real-time-data-with-Azure-Database-for-PostgreSQL-Hyperscale](https://github.com/microsoft/MCW-Real-time-data-with-Azure-Database-for-PostgreSQL-Hyperscale/blob/master/Hands-on%20lab/HOL%20step-by%20step%20-%20Real-time%20data%20with%20Azure%20Database%20for%20PostgreSQL%20Hyperscale.md)<br/>
    Reportez-vous au contenu de l’image suivante dans cet article :<br/>

    ![Captures d’écran illustrant le contenu de référence dans l’article ci-dessus.](./media/data-flow-troubleshoot-connector-format/handshake-failure-cause-2.png)

#### <a name="recommendation"></a>Recommandation
Vous pouvez essayer d’utiliser des activités de copie pour débloquer ce problème. 

## <a name="csv-and-excel"></a>CSV et Excel

### <a name="set-the-quote-character-to-no-quote-char-is-not-supported-in-the-csv"></a>Le CSV ne prend pas en charge le caractère de guillemet défini sur « pas de guillemets »
 
#### <a name="symptoms"></a>Symptômes

Plusieurs problèmes ne sont pas pris en charge dans le CSV lorsque le caractère de guillemet est défini sur « pas de guillemets » :

1. Lorsque le caractère de guillemet est défini sur « pas de guillemets », le délimiteur de colonne à plusieurs caractères ne peut pas commencer et se terminer par les mêmes lettres.
2. Lorsque le caractère de guillemet est défini sur « pas de guillemets », le délimiteur de colonne à plusieurs caractères ne peut pas contenir le caractère d’échappement : `\`.
3. Lorsque le caractère de guillemet est défini sur « pas de guillemets », la valeur de la colonne ne peut pas contenir de délimiteur de ligne.
4. Le caractère de guillemet et le caractère d’échappement ne peuvent pas tous les deux être vides (pas de guillemets ni séquence d’échappement) si la valeur de la colonne contient un délimiteur de colonne.

#### <a name="cause"></a>Cause

Les causes des symptômes sont indiquées ci-dessous avec leurs exemples respectifs :
1. Commence et se termine par les mêmes lettres.<br/>
`column delimiter: $*^$*`<br/>
`column value: abc$*^    def`<br/>
`csv sink: abc$*^$*^$*def ` <br/>
`will be read as "abc" and "^&*def"`<br/>

2. Le délimiteur à plusieurs caractères contient des caractères d’échappement.<br/>
`column delimiter: \x`<br/>
`escape char:\`<br/>
`column value: "abc\\xdef"`<br/>
Le caractère d’échappement échappe le délimiteur de colonne ou le caractère d’échappement.

3. La valeur de la colonne contient un délimiteur de ligne. <br/>
`We need quote character to tell if row delimiter is inside column value or not.`

4. Le caractère de guillemet et le caractère d’échappement sont tous les deux vides et la valeur de la colonne contient des délimiteurs de colonne.<br/>
`Column delimiter: \t`<br/>
`column value: 111\t222\t33\t3`<br/>
`It will be ambigious if it contains 3 columns 111,222,33\t3 or 4 columns 111,222,33,3.`<br/>

#### <a name="recommendation"></a>Recommandation
Le premier symptôme et le second symptôme ne peuvent pas être résolus actuellement. Pour les troisième et quatrième symptômes, vous pouvez appliquer les méthodes suivantes :
- Pour le symptôme 3, n’utilisez pas l’option« pas de guillemets » pour un fichier CSV multiligne.
- Pour le symptôme 4, définissez le caractère de guillemet ou le caractère d’échappement comme non vide, ou vous pouvez supprimer tous les délimiteurs de colonne dans vos données.

### <a name="read-files-with-different-schemas-error"></a>Erreur de lecture des fichiers avec des schémas différents

#### <a name="symptoms"></a>Symptômes

Lorsque vous utilisez des flux de données pour lire des fichiers tels que des fichiers CSV et Excel avec des schémas différents, le débogage de flux de données, le bac à sable ou l’exécution d’activité échouent.
- Pour CSV, le mauvais alignement des données existe lorsque le schéma des fichiers est différent. 

    ![Capture d’écran montrant la première erreur de schéma.](./media/data-flow-troubleshoot-connector-format/schema-error-1.png)

- Pour Excel, une erreur se produit lorsque le schéma du fichier est différent.

    ![Capture d’écran montrant la seconde erreur de schéma.](./media/data-flow-troubleshoot-connector-format/schema-error-2.png)

#### <a name="cause"></a>Cause

La lecture de fichiers avec des schémas différents dans le flux de données n’est pas prise en charge.

#### <a name="recommendation"></a>Recommandation

Si vous souhaitez toujours transférer des fichiers tels que des fichiers CSV et Excel avec des schémas différents dans le flux de données, vous pouvez utiliser les méthodes suivantes pour contourner le problème :

- Pour CSV, vous devez fusionner manuellement le schéma des différents fichiers pour obtenir le schéma complet. Par exemple, file_1 contient des colonnes `c_1, c_2, c_3` tandis que file_2 contient des colonnes `c_3, c_4,... c_10`, donc le schéma fusionné et complet est `c_1, c_2... c_10`. Ensuite, faites en sorte que les autres fichiers aient également le même schéma complet, même s’ils ne contiennent pas de données. Par exemple, file_x contient uniquement des colonnes `c_1, c_2, c_3, c_4` : ajoutez les colonnes supplémentaires `c_5, c_6, ... c_10` au fichier, et cela pourra alors fonctionner.

- Pour Excel, vous pouvez résoudre ce problème en appliquant l’une des options suivantes :

    - **Option 1** : Vous devez fusionner manuellement le schéma des différents fichiers pour obtenir le schéma complet. Par exemple, file_1 contient des colonnes `c_1, c_2, c_3` tandis que file_2 contient des colonnes `c_3, c_4,... c_10`, donc le schéma fusionné et complet est `c_1, c_2... c_10`. Ensuite, faites en sorte que les autres fichiers aient également le même schéma, même s’ils ne contiennent pas de données. Par exemple, file_x avec la feuille « SHEET_1 » contient uniquement des colonnes `c_1, c_2, c_3, c_4` : ajoutez les colonnes supplémentaires `c_5, c_6, ... c_10` également dans la feuille, et cela pourra alors fonctionner.
    - **Option 2** : Utilisez la **plage (par exemple, A1:G100) + firstRowAsHeader=false**, et il peut alors charger les données de tous les fichiers Excel, même si le nom et le nombre de colonnes sont différents.

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

### <a name="serverless-pool-sql-on-demand-related-issues"></a>Problèmes liés au pool serverless (SQL à la demande)

#### <a name="symptoms"></a>Symptômes
Vous utilisez Azure Synapse Analytics, et le service lié est en fait un pool serverless Synapse. Son ancien nom est « pool SQL à la demande », et il peut être distingué par le nom du serveur contenant `ondemand`, par exemple, `space-ondemand.sql.azuresynapse.net`. Vous pouvez être confronté à plusieurs défaillances uniques comme indiqué ci-dessous :<br/>

1. Lorsque vous souhaitez utiliser un pool serverless Synapse en tant que récepteur, vous êtes confronté à l’erreur suivante :<br/>
`Sink results in 0 output columns. Please ensure at least one column is mapped`
1. Lorsque vous sélectionnez « Activer la mise en lots » dans la source, vous êtes confronté à l’erreur suivante : `shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: Incorrect syntax near 'IDENTITY'.`.
1. Lorsque vous souhaitez extraire des données d’une table externe, vous êtes confronté à l’erreur suivante : `shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: External table 'dbo' is not accessible because location does not exist or it is used by another process.`.
1. Lorsque vous souhaitez extraire des données de Cosmos DB par le biais d’un pool serverless à partir d’une requête ou d’un affichage, vous êtes confronté à l’erreur suivante : `Job failed due to reason: Connection reset.`.
1. Lorsque vous souhaitez extraire des données à partir d’un affichage, vous pouvez être confronté à différentes erreurs.

#### <a name="cause"></a>Cause
Les causes des symptômes sont indiquées ci-dessous, respectivement :
1. Le pool serverless ne peut pas être utilisé comme récepteur. Il ne prend pas en charge l’écriture de données dans la base de données.
1. Le pool serverless ne prend pas en charge le chargement de données mises en lots, donc l’option « Activer la mise en lots » n’est pas prise en charge. 
1. La méthode d’authentification que vous utilisez ne dispose pas d’une autorisation correcte pour la source de données externe à laquelle la table externe fait référence.
1. Il existe une limitation connue dans le pool serverless Synapse qui vous empêche d’extraire des données Cosmos DB des flux de données.
1. L’affichage est une table virtuelle basée sur une instruction SQL. La cause racine se trouve dans l’instruction de l’affichage.

#### <a name="recommendation"></a>Recommandation

Vous pouvez appliquer les étapes suivantes pour résoudre vos problèmes en conséquence.
1. Il est préférable de ne pas utiliser le pool serverless comme récepteur.
1. N’utilisez pas « Activer la mise en lots » dans la source pour le pool serverless.
1. Seul le principal de service ou seule l’identité managée qui dispose de l’autorisation sur les données de la table externe peut l’interroger. Vous devez accorder l’autorisation « Contributeur aux données Blob du stockage » à la source de données externe pour la méthode d’authentification que vous utilisez dans ADF.
    >[!Note]
    > L’authentification par mot de passe utilisateur ne peut pas interroger les tables externes. Vous pouvez vous référer à cet article pour plus d’informations : [Modèle de sécurité](../synapse-analytics/metadata/database.md#security-model).

1. Vous pouvez utiliser l’activité de copie pour extraire des données Cosmos DB du pool serverless.
1. Vous pouvez fournir l’instruction SQL qui crée l’affichage à l’équipe du support technique responsable de l’ingénierie, qui pourra vous aider à analyser si l’instruction rencontre un problème d’authentification ou autre.


### <a name="load-small-size-data-to-data-warehouse-without-staging-is-slow"></a>Le chargement de données de petite taille dans Data Warehouse sans mise en lots est lent 

#### <a name="symptoms"></a>Symptômes
Lorsque vous chargez des données de petite taille dans Data Warehouse sans mise en lots, cela prend beaucoup de temps. Par exemple, la taille des données est de 2 Mo, mais le chargement prend plus d’une heure. 

#### <a name="cause"></a>Cause
Ce problème est dû au nombre de lignes plutôt qu’à la taille. Le nombre de lignes est de quelques milliers, et chaque insertion doit être empaquetée dans une requête indépendante, accéder au nœud de contrôle, démarrer une nouvelle transaction, obtenir des verrous et accéder au nœud de distribution à plusieurs reprises. Le chargement en masse obtient le verrou une seule fois, et chaque nœud de distribution effectue l’insertion en effectuant un traitement par lot dans la mémoire de manière efficace.

Si 2 Mo sont insérés sous forme de quelques enregistrements, l’opération sera rapide. Par exemple, ce serait rapide si chaque enregistrement est de 500 ko * 4 lignes. 

#### <a name="recommendation"></a>Recommandation
Vous devez activer la mise en lots pour améliorer les performances.


### <a name="read-empty-string-value--as-null-with-the-enable-staging"></a>Lire une chaîne vide ("") comme NULL avec l’activation de la mise en lots 

#### <a name="symptoms"></a>Symptômes
Quand vous utilisez Synapse comme source dans le flux de données, par exemple l’aperçu des données et le débogage ou le déclenchement de l’exécution, et que vous activez la mise en lots pour utiliser PolyBase, si la valeur de votre colonne contient une chaîne vide (`""`), elle est remplacée par Null.

#### <a name="cause"></a>Cause
Le back end du flux de données utilise Parquet comme format PolyBase, et il existe une limitation connue dans le pool Synapse SQL de 2e génération, qui remplacera automatiquement la valeur de chaîne vide par Null.

#### <a name="recommendation"></a>Recommandation
Vous pouvez essayer de résoudre ce problème à l’aide des méthodes suivantes :
1. Si la taille des données n’est pas énorme, vous pouvez désactiver l’option **Activer la mise en lots** dans la source, mais les performances seront affectées.
1. Si vous devez activer la mise en lots, vous pouvez utiliser la fonction **iifNull()** pour remplacer manuellement la colonne spécifique Null par une valeur de chaîne vide.

### <a name="managed-service-identity-error"></a>Erreur d’identité du service géré

#### <a name="symptoms"></a>Symptômes
Lorsque vous utilisez Synapse comme source ou récepteur dans le flux de données pour afficher un aperçu des données, déboguer ou déclencher l’exécution, etc., que vous activez la mise en lots pour utiliser PolyBase et que le service lié du magasin de mise en lots (Blob, Gen2, etc.) est créé pour utiliser l’authentification par identité managée, votre travail peut échouer avec l’erreur suivante illustrée dans l’image : <br/>

![Captures d’écran montrant l’erreur d’identité du service.](./media/data-flow-troubleshoot-connector-format/service-identity-error.png)

#### <a name="error-message"></a>Message d’erreur
`shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: Managed Service Identity has not been enabled on this server. Please enable Managed Service Identity and try again.`

#### <a name="cause"></a>Cause
1. Si le pool SQL est créé à partir de l’espace de travail Synapse, l’authentification par identité managée sur le magasin de mise en lots avec PolyBase n’est pas prise en charge pour l’ancien pool SQL. 
1. Si le pool SQL est l’ancienne version de Data Warehouse (DWH), l’identité managée du serveur SQL n’est pas attribuée au magasin de mise en lots.

#### <a name="recommendation"></a>Recommandation
Vous devez vérifier si le pool SQL est créé à partir de l’espace de travail Synapse.

- Si le pool SQL est créé à partir de l’espace de travail Synapse, vous devez réinscrire l’identité managée de l’espace de travail. Vous pouvez appliquer les étapes suivantes pour contourner ce problème en réinscrivant l’identité managée de l’espace de travail :
    1. Accédez à votre espace de travail Synapse sur le portail Azure.
    1. Accédez au panneau des **identités managées**.
    1. Si l’option **Autoriser les pipelines** est déjà activée, vous devez désactiver ce paramètre et enregistrer.
    1. Cochez l’option **Autoriser les pipelines** et enregistrez.

- Si le pool SQL est l’ancienne version de DWH, activez uniquement l’identité managée pour votre serveur SQL et attribuez l’autorisation du magasin de mise en lots à l’identité managée de votre serveur SQL. Vous pouvez vous reporter aux étapes décrites dans cet article à titre d’exemple : [Utiliser des points de terminaison de service de réseau virtuel et des règles pour serveurs dans Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md#steps).

### <a name="failed-with-an-error-sqlserverexception-not-able-to-validate-external-location-because-the-remote-server-returned-an-error-403"></a>Échec avec une erreur : « SQLServerException : Impossible de valider l’emplacement externe, car le serveur distant a renvoyé une erreur : (403) »

#### <a name="symptoms"></a>Symptômes

Lorsque vous utilisez SQLDW comme récepteur pour déclencher et exécuter des activités de flux de données, l’activité peut échouer avec une erreur telle que : `"SQLServerException: Not able to validate external location because the remote server returned an error: (403)"`.

#### <a name="cause"></a>Cause
1. Lorsque vous utilisez l’identité managée dans la méthode d’authentification du compte ADLS Gen2 comme mise en lots, il se peut que cx ne définisse pas correctement la configuration de l’authentification.
1. Avec le runtime d’intégration de réseau virtuel, vous devez utiliser l’identité managée dans la méthode d’authentification du compte ADLS Gen2 comme mise en lots. Si votre stockage Azure de mise en lots est configuré avec un point de terminaison de service de réseau virtuel, vous devez utiliser l’authentification par identité managée et activer l’option « Autoriser le service Microsoft approuvé » sur le compte de stockage.
1. Vérifiez si le nom de votre dossier contient une espace ou d’autres caractères spéciaux, par exemple : `Space " < > # % |`.
Actuellement, les noms de dossiers qui contiennent certains caractères spéciaux ne sont pas pris en charge dans la commande de copie de Data Warehouse.

#### <a name="recommendation"></a>Recommandation

Pour la cause 1, vous pouvez vous référer au document [Utiliser des points de terminaison de service de réseau virtuel et des règles pour serveurs dans Azure SQL Database, Étapes](../azure-sql/database/vnet-service-endpoint-rule-overview.md#steps) pour résoudre ce problème.

Pour la cause 2, contournez le problème à l’aide de l’une des options suivantes :

- Option 1 : Si vous utilisez le runtime d’intégration de réseau virtuel, vous devez utiliser l’identité managée dans la méthode d’authentification du compte ADLS Gen2 comme mise en lots.

- Option 2 : Si votre stockage Azure de mise en lots est configuré avec un point de terminaison de service de réseau virtuel, vous devez utiliser l’authentification par identité managée et activer « Autoriser le service Microsoft approuvé » sur le compte de stockage. Pour plus d’informations, vous pouvez vous référer à ce document : [Copie intermédiaire à l’aide de PolyBase](./connector-azure-sql-data-warehouse.md#staged-copy-by-using-polybase).

Pour la cause 3, contournez le problème à l’aide de l’une des options suivantes :

- Option 1 : Renommez le dossier et évitez d’utiliser des caractères spéciaux dans le nom du dossier.
- Option 2 : Supprimez la propriété `allowCopyCommand:true` dans le script de flux de données, par exemple :

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/remove-allow-copy-command-true.png" alt-text="Capture d’écran montrant comment supprimer « allowcopycommand:true »."::: 


### <a name="failed-with-an-error-this-operation-is-not-permitted-on-a-non-empty-directory"></a>Échec avec une erreur : « Cette opération n’est pas autorisée sur un répertoire non vide »

#### <a name="symptoms"></a>Symptômes

Lorsque vous utilisez Azure Synapse Analytics comme récepteur dans le flux de données pour afficher un aperçu des données, déboguer ou déclencher l’exécution, etc. et que l’option « Activer la mise en lots » est définie sur true, votre travail peut échouer avec le message d’erreur suivant :

`DF-SYS-01 at Sink 'sink': Unable to stage data before write. Check configuration/credentials of storage.`<br/>
`org.apache.hadoop.fs.azure.AzureException: com.microsoft.azure.storage.StorageException: This operation is not permitted on a non-empty directory.`

#### <a name="cause"></a>Cause
Vous utilisez Stockage Blob Azure comme service lié de mise en lots pour établir un lien avec un compte de stockage dont l’espace de noms hiérarchique est activé, et ce compte utilise une authentification par clé dans le service lié.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/storage-account-configuration.png" alt-text="Capture d’écran montrant la configuration du compte de stockage."::: 

#### <a name="recommendation"></a>Recommandation
Créez un service lié Azure Data Lake Gen2 pour le stockage, puis sélectionnez le stockage Gen2 comme service lié de mise en lots dans les activités de flux de données.


## <a name="azure-blob-storage"></a>Stockage Blob Azure

### <a name="account-kind-of-storage-general-purpose-v1-doesnt-support-service-principal-and-mi-authentication"></a>Le type de compte de stockage (v1 universel) ne prend pas en charge l’authentification par principal de service et par identité managée

#### <a name="symptoms"></a>Symptômes

Dans les flux de données, si vous utilisez Stockage Blob Azure (v1 universel) avec l’authentification par principal de service ou par identité managée, vous pouvez rencontrer le message d’erreur suivant :

`com.microsoft.dataflow.broker.InvalidOperationException: ServicePrincipal and MI auth are not supported if blob storage kind is Storage (general purpose v1)`

#### <a name="cause"></a>Cause

Lorsque vous utilisez le service lié Blob Azure dans un flux de données, l’authentification par identité managée ou par principal de service n’est pas prise en charge lorsque le type de compte est vide ou « Stockage ». Cette situation est illustrée dans les images 1 et 2 ci-dessous.

Image 1 : Type de compte dans le service lié Stockage Blob Azure

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/storage-account-kind.png" alt-text="Capture d’écran montrant le type de compte de stockage dans le service lié Stockage Blob Azure."::: 

Image 2 : Page du compte de stockage

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/storage-account-page.png" alt-text="Capture d’écran montrant la page du compte de stockage." lightbox="./media/data-flow-troubleshoot-connector-format/storage-account-page.png"::: 


#### <a name="recommendation"></a>Recommandation

Pour résoudre ce problème, suivez les recommandations suivantes :

- Si le type de compte de stockage est **Aucun** dans le service lié Blob Azure, spécifiez le type de compte approprié et reportez-vous à l’image 3 ci-dessous pour le faire. En outre, reportez-vous à l’image 2 pour obtenir le type de compte de stockage, puis vérifiez et confirmez que le type de compte n’est pas « Stockage (v1 universel) ».

    Image 3 : Spécifier le type de compte de stockage dans le service lié Stockage Blob Azure

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/specify-storage-account-kind.png" alt-text="Capture d’écran montrant comment spécifier le type de compte de stockage dans le service lié Stockage Blob Azure."::: 
    

- Si le type de compte est « Stockage (v1 universel) », mettez à niveau votre compte de stockage vers **v2 universel** ou choisissez une authentification différente.

    Image 4 : Mettre à niveau le compte de stockage vers v2 universel

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/upgrade-storage-account.png" alt-text="Capture d’écran montrant comment mettre à niveau le compte de stockage vers v2 universel." lightbox="./media/data-flow-troubleshoot-connector-format/upgrade-storage-account.png"::: 
    

## <a name="snowflake"></a>Snowflake

### <a name="unable-to-connect-to-the-snowflake-linked-service"></a>Impossible de se connecter au service lié Snowflake

#### <a name="symptoms"></a>Symptômes

Vous rencontrez l’erreur suivante lorsque vous créez le service lié Snowflake dans le réseau public et que vous utilisez le runtime d’intégration de résolution automatique.

`ERROR [HY000] [Microsoft][Snowflake] (4) REST request for URL https://XXXXXXXX.east-us- 2.azure.snowflakecomputing.com.snowflakecomputing.com:443/session/v1/login-request?requestId=XXXXXXXXXXXXXXXXXXXXXXXXX&request_guid=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` 

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/connection-fail-error.png" alt-text="Capture d’écran montrant l’erreur Échec de connexion."::: 

#### <a name="cause"></a>Cause

Vous n’avez pas appliqué le nom du compte dans le format indiqué dans le document du compte Snowflake (y compris les segments supplémentaires qui identifient la région et la plateforme cloud), par exemple `XXXXXXXX.east-us-2.azure`. Pour plus d’informations, vous pouvez vous référer à ce document : [Propriétés du service lié](./connector-snowflake.md#linked-service-properties).

#### <a name="recommendation"></a>Recommandation

Pour résoudre le problème, modifiez le format du nom de compte. Le rôle doit être l’un des rôles indiqués dans l’image suivante, mais le rôle par défaut est **Public**.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/account-role.png" alt-text="Capture d’écran montrant les rôles de compte."::: 

### <a name="sql-access-control-error-insufficient-privileges-to-operate-on-schema"></a>Erreur de contrôle d’accès SQL : « Privilèges insuffisants pour agir sur le schéma »

#### <a name="symptoms"></a>Symptômes

Lorsque vous essayez d’utiliser « Importer une projection », « Aperçu des données », etc. dans la source Snowflake des flux de données, vous rencontrez des erreurs telles que `net.snowflake.client.jdbc.SnowflakeSQLException: SQL access control error: Insufficient privileges to operate on schema`.

#### <a name="cause"></a>Cause

Vous rencontrez cette erreur en raison d’une configuration incorrecte. Lorsque vous utilisez le flux de données pour lire les données Snowflake, le runtime Azure Databricks (ADB) ne sélectionne pas directement la requête jusqu’à Snowflake. Au lieu de cela, une étape temporaire est créée et les données sont tirées (pull) des tables vers l’étape, puis compressées et tirées (pull) par ADB. Ce processus est illustré dans l’image ci-dessous.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/snowflake-data-read-model.png" alt-text="Capture d’écran montrant le modèle de lecture des données Snowflake."::: 

Par conséquent, l’utilisateur ou le rôle utilisé dans ADB doit disposer de l’autorisation nécessaire pour effectuer cette opération dans Snowflake. Mais en général, l’utilisateur/le rôle n’a pas l’autorisation puisque la base de données est créée sur le partage. 

#### <a name="recommendation"></a>Recommandation
Pour résoudre ce problème, vous pouvez créer une base de données différente et créer des affichages en haut de la base de données partagée pour y accéder à partir d’ADB. Pour plus d’informations, référez-vous à [Snowflake](https://community.snowflake.com/s/question/0D50Z000095ktE4SAI/insufficient-privileges-to-operate-on-schema).

### <a name="failed-with-an-error-snowflakesqlexception-ip-xxxx-is-not-allowed-to-access-snowflake-contact-your-local-security-administrator"></a>Échec avec une erreur : « SnowflakeSQLException : L’adresse IP x.x.x.x n’est pas autorisée à accéder à Snowflake. Contactez votre administrateur local de la sécurité. »

#### <a name="symptoms"></a>Symptômes

Lorsque vous utilisez Snowflake dans Azure Data Factory, vous pouvez utiliser test-connection dans le service lié Snowflake, preview-data/import-schema sur le jeu de données Snowflake et run copy/lookup/get-metadata ou d’autres activités avec celui-ci. Toutefois, lorsque vous utilisez Snowflake dans l’activité de flux de données, vous pouvez rencontrer des erreurs telles que `SnowflakeSQLException: IP 13.66.58.164 is not allowed to access Snowflake. Contact your local security administrator.`.

#### <a name="cause"></a>Cause

Le flux de données Azure Data Factory ne prend pas en charge l’utilisation de plages d’adresses IP fixes. Vous pouvez vous référer à [Adresses IP Azure Integration Runtime](./azure-integration-runtime-ip-addresses.md) pour obtenir des informations plus détaillées.

#### <a name="recommendation"></a>Recommandation

Pour résoudre ce problème, vous pouvez modifier les paramètres du pare-feu du compte Snowflake en procédant comme suit :

1. Vous pouvez obtenir la liste de plages d’adresses IP des étiquettes de service à partir du « lien de téléchargement de la plage d’adresses IP des étiquettes de service » : [Détection de balises de service à l’aide de fichiers JSON téléchargeables](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/ip-range-list.png" alt-text="Capture d’écran montrant la liste des plages d’adresses IP."::: 

1. Si vous exécutez un flux de données dans la région « southcentralus », vous devez autoriser l’accès à partir de toutes les adresses portant le nom « AzureCloud.southcentraluss », par exemple :

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/allow-access-with-name.png" alt-text="Capture d’écran montrant comment autoriser l’accès à partir de toutes les adresses portant un certain nom."::: 

### <a name="queries-in-the-source-does-not-work"></a>Les requêtes dans la source ne fonctionnent pas

#### <a name="symptoms"></a>Symptômes

Lorsque vous tentez de lire des données à partir de Snowflake à l’aide d’une requête, vous pouvez rencontrer une erreur telle que :

1. `SQL compilation error: error line 1 at position 7 invalid identifier 'xxx'`
2. `SQL compilation error: Object 'xxx' does not exist or not authorized.`

#### <a name="cause"></a>Cause

Vous rencontrez cette erreur en raison d’une configuration incorrecte.

#### <a name="recommendation"></a>Recommandation

Snowflake applique les règles suivantes pour stocker les identificateurs au moment de leur création ou définition et les résoudre dans les requêtes et autres instructions SQL :

Lorsqu’un identificateur (nom de table, nom de schéma, nom de colonne, etc.) est sans guillemets, il est stocké et résolu en majuscules par défaut et respecte la casse. Par exemple :

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/unquoted-identifier.png" alt-text="Capture d’écran montrant l’exemple d’un identificateur sans guillemets."lightbox="./media/data-flow-troubleshoot-connector-format/unquoted-identifier.png"::: 

Comme elle respecte la casse, vous pouvez utiliser la requête suivante pour lire les données Snowflake, le résultat étant le même :<br/>
- `Select MovieID, title from Public.TestQuotedTable2`<br/>
- `Select movieId, title from Public.TESTQUOTEDTABLE2`<br/>
- `Select movieID, TITLE from PUBLIC.TESTQUOTEDTABLE2`<br/>

Lorsqu’un identificateur (nom de table, nom de schéma, nom de colonne, etc.) est placé entre guillemets, il est stocké et résolu exactement comme il a été saisi, y compris la casse. Vous pouvez voir un exemple dans l’image suivante. Pour plus d’informations, consultez ce document : [Identifier Requirements](https://docs.snowflake.com/en/sql-reference/identifiers-syntax.html#identifier-requirements).

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/double-quoted-identifier.png" alt-text="Capture d’écran montrant l’exemple d’un identificateur entre guillemets doubles." lightbox="./media/data-flow-troubleshoot-connector-format/double-quoted-identifier.png"::: 

Étant donné que l’identificateur qui respecte la casse (nom de la table, nom du schéma, nom de la colonne, etc.) a un caractère minuscule, vous devez placer l’identificateur entre guillemets pendant la lecture des données à l’aide de la requête, par exemple : <br/>

- Sélectionnez **"movieId"** , **"title"** de Public. **"testQuotedTable2"** .

Si vous rencontrez une erreur avec la requête Snowflake, vérifiez si certains identificateurs (nom de la table, nom du schéma, nom de la colonne, etc.) respectent la casse à l’aide des étapes suivantes :

1. Connectez-vous au serveur Snowflake (`https://{accountName}.azure.snowflakecomputing.com/`, remplacez {accountName} par le nom de votre compte) pour vérifier l’identificateur (nom de la table, nom du schéma, nom de la colonne, etc.).

1. Créez des feuilles de calcul pour tester et valider la requête :
    - Exécutez `Use database {databaseName}`, remplacez {databaseName} par le nom de votre base de données.
    - Exécutez une requête avec une table, par exemple : `select "movieId", "title" from Public."testQuotedTable2"`.
    
1. Une fois que la requête SQL de Snowflake est testée et validée, vous pouvez l’utiliser directement dans la source Snowflake de flux de données.

## <a name="azure-sql-database"></a>Azure SQL Database
 
### <a name="unable-to-connect-to-the-sql-database"></a>Impossible de se connecter à la base de données SQL

#### <a name="symptoms"></a>Symptômes

Votre base de données Azure SQL peut fonctionner correctement dans la copie de données, l’affichage d’un aperçu des données et le test de connexion du jeu de données dans le service lié, mais elle échoue lorsque la même base de données Azure SQL est utilisée comme source ou récepteur dans le flux de données avec une erreur telle que `Cannot connect to SQL database: 'jdbc:sqlserver://powerbasenz.database.windows.net;..., Please check the linked service configuration is correct, and make sure the SQL database firewall allows the integration runtime to access`.

#### <a name="cause"></a>Cause

Les paramètres du pare-feu de votre serveur Azure SQL Database sont incorrects, de sorte qu’il ne peut pas se connecter par l’intermédiaire du runtime de flux de données. Actuellement, lorsque vous essayez d’utiliser le flux de données pour lire/écrire dans Azure SQL Database, Azure Databricks est utilisé pour créer le cluster Spark afin d’exécuter le travail, mais il ne prend pas en charge les plages d’adresses IP fixes. Pour plus d’informations, consultez [Adresses IP Azure Integration Runtime](./azure-integration-runtime-ip-addresses.md).

#### <a name="recommendation"></a>Recommandation

Vérifiez les paramètres du pare-feu de votre instance Azure SQL Database et définissez-les sur « Autoriser l’accès aux services Azure » au lieu de définir une plage d’adresses IP fixes.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/allow-access-to-azure-service.png" alt-text="Capture d’écran montrant comment autoriser l’accès au service Azure dans les paramètres du pare-feu."::: 

### <a name="syntax-error-when-using-queries-as-input"></a>Erreur de syntaxe lors de l’utilisation de requêtes en entrée

#### <a name="symptoms"></a>Symptômes

Lorsque vous utilisez des requêtes en entrée dans la source du flux de données avec Azure SQL, vous échouez avec le message d’erreur suivant :

`at Source 'source1': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: Incorrect syntax XXXXXXXX.`

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/error-detail.png" alt-text="Capture d’écran montrant les détails de l’erreur."::: 

#### <a name="cause"></a>Cause

La requête utilisée dans la source du flux de données doit pouvoir s’exécuter en tant que sous-requête. La raison de cet échec est que la syntaxe de la requête est incorrecte ou qu’elle ne peut pas s’exécuter en tant que sous-requête. Vous pouvez exécuter la requête suivante dans SQL Server Management Studio pour la vérifier :

`SELECT top(0) * from ($yourQuery) as T_TEMP`

#### <a name="recommendation"></a>Recommandation

Fournissez une requête correcte et testez-la d’abord dans SQL Server Management Studio.

### <a name="failed-with-an-error-sqlserverexception-111212-operation-cannot-be-performed-within-a-transaction"></a>Échec avec une erreur : « SQLServerException : 111212 ; L’opération ne peut pas être effectuée dans une transaction. »

#### <a name="symptoms"></a>Symptômes

Lorsque vous utilisez Azure SQL Database comme récepteur dans le flux de données pour afficher un aperçu des données, déboguer ou déclencher l’exécution, etc., vous pouvez constater que votre travail échoue avec le message d’erreur suivant :

`{"StatusCode":"DFExecutorUserError","Message":"Job failed due to reason: at Sink 'sink': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: 111212;Operation cannot be performed within a transaction.","Details":"at Sink 'sink': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: 111212;Operation cannot be performed within a transaction."}`

#### <a name="cause"></a>Cause
L’erreur « `111212;Operation cannot be performed within a transaction.` » se produit uniquement dans le pool SQL dédié de Synapse. Mais vous commettez l’erreur d’utiliser Azure SQL Database comme connecteur à la place.

#### <a name="recommendation"></a>Recommandation
Vérifiez si votre instance SQL Database est un pool SQL dédié de Synapse. Si c’est le cas, utilisez Azure Synapse Analytics comme connecteur, tel qu’illustré dans l’image ci-dessous.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/synapse-analytics-connector.png" alt-text="Capture d’écran montrant le connecteur Azure Synapse Analytics."::: 

### <a name="data-with-the-decimal-type-become-null"></a>Les données de type décimal deviennent Null

#### <a name="symptoms"></a>Symptômes

Vous souhaitez insérer des données dans une table de la base de données SQL. Si les données contiennent le type décimal et doivent être insérées dans une colonne avec le type décimal dans la base de données SQL, la valeur des données peut être remplacée par Null.

Si vous effectuez un aperçu, dans les étapes précédentes, cela affichera la valeur comme ci-dessous :

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/value-in-previous-stage.png" alt-text="Capture d’écran montrant la valeur des étapes précédentes."::: 

À l’étape du récepteur, la valeur devient Null, ce qui est indiqué dans l’image ci-dessous.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/value-in-sink-stage.png" alt-text="Capture d’écran montrant la valeur à l’étape du récepteur."::: 

#### <a name="cause"></a>Cause
Le type décimal a des propriétés d’échelle et de précision. Si votre type de données ne correspond pas à celui de la table de récepteur, le système vérifie que le décimal cible est plus grand que celui d’origine et que la valeur d’origine ne dépasse pas le décimal cible. Par conséquent, la valeur sera castée en Null.

#### <a name="recommendation"></a>Recommandation
Vérifiez et comparez le type décimal entre les données et la table dans la base de données SQL et modifiez l’échelle et la précision pour qu’elles soient identiques.

Vous pouvez utiliser toDecimal (IDecimal, échelle, précision) pour déterminer si les données d’origine peuvent être castées à l’échelle et à la précision cibles. Si la fonction renvoie la valeur Null, cela signifie que les données ne peuvent pas être castées et approfondies lors de l’insertion.

## <a name="adls-gen2"></a>ADLS Gen2

### <a name="failed-with-an-error-error-while-reading-file-xxx-it-is-possible-the-underlying-files-have-been-updated"></a>Échec avec une erreur : « Erreur lors de la lecture du fichier XXX. Il est possible que les fichiers sous-jacents aient été mis à jour »

#### <a name="symptoms"></a>Symptômes

Lorsque vous utilisez ADLS Gen2 comme récepteur dans le flux de données (pour afficher un aperçu des données, déboguer ou déclencher l’exécution, etc.) et que le paramètre de partition de l’onglet **Optimiser** à l’étape **Récepteur** n’est pas défini par défaut, le travail peut échouer avec le message d’erreur suivant :

`Job failed due to reason: Error while reading file abfss:REDACTED_LOCAL_PART@prod.dfs.core.windows.net/import/data/e3342084-930c-4f08-9975-558a3116a1a9/part-00000-tid-7848242374008877624-5df7454e-7b14-4253-a20b-d20b63fe9983-1-1-c000.csv. It is possible the underlying files have been updated. You can explicitly invalidate the cache in Spark by running 'REFRESH TABLE tableName' command in SQL or by recreating the Dataset/DataFrame involved.`

#### <a name="cause"></a>Cause

1. Vous n’avez pas attribué une autorisation appropriée à votre authentification par identité managée ou par principal de service.
1. Vous avez peut-être un travail personnalisé pour traiter les fichiers dont vous ne voulez pas, ce qui aura une incidence sur la sortie intermédiaire du flux de données.

#### <a name="recommendation"></a>Recommandation
1. Vérifiez si votre service lié dispose de l’autorisation R/W/E pour Gen2. Si vous utilisez l’authentification par identité managée ou par principal de service, accordez au moins le rôle Contributeur aux données Blob du stockage dans le contrôle d’accès (IAM).
1. Vérifiez si vous avez des travaux spécifiques qui suppriment des fichiers ou les déplacent vers un autre emplacement dont le nom ne correspond pas à votre règle. Étant donné que les flux de données consignent d’abord les fichiers de partition dans le dossier cible, puis effectuent les opérations de fusion et de renommage, le nom du fichier intermédiaire peut ne pas correspondre à votre règle.

## <a name="adls-gen1"></a>ADLS Gen1

### <a name="fail-to-create-files-with-service-principle-authentication"></a>Échec de la création de fichiers avec l’authentification par principal de service

#### <a name="symptoms"></a>Symptômes
Lorsque vous essayez de déplacer ou de transférer des données de différentes sources vers le récepteur ADLS Gen1, si la méthode d’authentification du service lié est l’authentification par principal de service, votre travail peut échouer avec le message d’erreur suivant :

`org.apache.hadoop.security.AccessControlException: CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.). [2b5e5d92-xxxx-xxxx-xxxx-db4ce6fa0487] failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)`

#### <a name="cause"></a>Cause

L’autorisation RWX ou la propriété du jeu de données n’est pas définie correctement.

#### <a name="recommendation"></a>Recommandation

- Si le dossier cible ne dispose pas des autorisations appropriées, reportez-vous à ce document pour attribuer l’autorisation appropriée dans Gen1 : [Utiliser une authentification par principal de service](./connector-azure-data-lake-store.md#use-service-principal-authentication).

- Si le dossier cible possède l’autorisation appropriée et que vous utilisez la propriété du nom de fichier dans le flux de données pour cibler le dossier et le nom de fichier appropriés, mais que la propriété du chemin d’accès au fichier du jeu de données n’est pas définie sur le chemin d’accès au fichier cible (généralement, à ne pas définir), comme illustré dans les images suivantes, vous rencontrerez cet échec parce que le système principal tente de créer des fichiers en fonction du chemin d’accès au fichier du jeu de données et que celui-ci ne dispose pas de l’autorisation appropriée.
    
    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/file-path-property.png" alt-text="Capture d’écran montrant la propriété du chemin d’accès au fichier"::: 
    
    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/file-name-property.png" alt-text="Capture d’écran montrant la propriété du nom de fichier"::: 

    
    Il existe deux méthodes pour résoudre ce problème :
    1. Attribuez l’autorisation WX au chemin d’accès au fichier du jeu de données.
    1. Définissez le chemin d’accès au fichier du jeu de données en tant que dossier avec l’autorisation WX, puis définissez le chemin d’accès au dossier REST et le nom de fichier dans les flux de données.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une aide supplémentaire sur la résolution des problèmes, consultez les ressources suivantes :

*  [Résoudre les problèmes liés aux flux de données de mappage dans Azure Data Factory](data-flow-troubleshoot-guide.md)
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)