---
title: Résoudre les problèmes liés aux flux de données de mappage
description: Découvrez comment résoudre les problèmes de flux de données dans Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: a95cacafc5b1d00b1e4d04fd84cdda2de72b6a59
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523003"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Résoudre les problèmes liés aux flux de données de mappage dans Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article présente des méthodes couramment employées pour résoudre les problèmes liés aux flux de données de mappage dans Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Messages et codes d’erreur courants 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Code d’erreur : DF-Executor-SourceInvalidPayload
- **Message** : L’exécution de l’aperçu des données, du débogage et du flux de données de pipeline a échoué car le conteneur n’existe pas
- **Causes** : Se produit quand le jeu de données contient un conteneur qui n’existe pas dans le stockage
- **Recommandation** : Vérifiez que le conteneur référencé dans votre jeu de données existe ou est accessible.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Code d’erreur : DF-Executor-SystemImplicitCartesian

- **Message** : Le produit cartésien implicite pour la jointure INNER n’est pas pris en charge. Utilisez plutôt CROSS JOIN. Les colonnes utilisées dans la jointure doivent créer une clé unique pour les lignes.
- **Causes** : Le produit cartésien implicite pour la jointure INNER entre plans logiques n’est pas pris en charge. Si les colonnes sont utilisées dans la jointure, la clé unique avec au moins une colonne provenant de chaque côté de la relation est nécessaire.
- **Recommandation** : Pour les jointures non basées sur l’égalité, vous devez opter pour CUSTOM CROSS JOIN.

### <a name="error-code-df-executor-systeminvalidjson"></a>Code d’erreur : DF-Executor-SystemInvalidJson

- **Message** : Erreur d’analyse JSON, encodage ou multiligne non pris en charge.
- **Causes** : Problèmes possibles avec le fichier JSON (encodage non pris en charge, octets endommagés ou utilisation de la source JSON comme document unique sur de nombreuses lignes imbriquées).
- **Recommandation** : Vérifiez que l’encodage du fichier JSON est pris en charge. Sur la transformation source qui utilise un jeu de données JSON, développez « Paramètres JSON » et activez « Un seul document ».
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Code d’erreur : DF-Executor-BroadcastTimeout

- **Message** : Erreur liée à l’expiration de la diffusion de la jointure. Vérifiez que la diffusion en streaming produit des données en moins de 60 s dans les exécutions de débogage et en moins de 300 s dans les exécutions de travaux.
- **Causes** : Le délai d’expiration par défaut pour la diffusion est de 60 s dans les exécutions de débogage et de 300 s dans les exécutions de travaux. Le flux choisi pour la diffusion semble être trop volumineux pour produire des données respectant cette limite.
- **Recommandation** : Sous l’onglet Optimiser, vérifiez les transformations de jointure, d’existence et de recherche de votre flux de données. L’option par défaut pour Diffusion est « Auto ». Si cette option est définie ou que vous définissez manuellement la diffusion du côté gauche ou droit sous « Fixe », vous pouvez soit définir une configuration Azure Integration Runtime plus grande, soit désactiver la diffusion. L’approche recommandée pour des performances optimales dans les flux de données consiste à autoriser Spark à diffuser à l’aide de l’option « Auto » et à utiliser un Azure IR à mémoire optimisée. Si vous exécutez le flux de données dans une exécution de test de débogage à partir d’une exécution de pipeline de débogage, vous pouvez rencontrer cette situation plus fréquemment. En effet, ADF limite le délai d’attente de diffusion à 60 secondes afin de maintenir une expérience de débogage plus rapide. Si vous voulez prolonger ce délai d’expiration à 300 secondes à partir d’une exécution déclenchée, vous pouvez utiliser l’option Déboguer > Utiliser le runtime d’activité pour utiliser le runtime Azure IR défini dans votre activité de pipeline Exécuter un flux de données.

- **Message** : Erreur de délai d’attente de la diffusion de la jonction. Pour éviter ce problème, vous pouvez choisir l’option de diffusion « Désactivé » dans la transformation de recherche/existence/jonction. Si vous envisagez de diffuser l’option de jonction pour améliorer les performances, assurez-vous que le flux de diffusion peut produire des données dans un délai de 60 secondes dans des exécutions de débogage et un délai de 300 secondes dans les exécutions de travaux.
- **Causes** : Le délai d’expiration par défaut pour la diffusion est de 60 s dans les exécutions de débogage et de 300 s dans les exécutions de travaux. Dans la diffusion de la jonction, le flux choisi pour la diffusion semble être trop volumineux pour produire des données respectant cette limite. Si une diffusion de jonction n’est pas utilisée, la diffusion par défaut effectuée par le flux de données peut atteindre la même limite
- **Recommandation** : Désactivez l’option de diffusion ou évitez de diffuser des flux de données volumineux dont le traitement peut prendre plus de 60 s. Choisissez plutôt un flux plus petit à diffuser. Les fichiers sources et les tables SQL/DW volumineux sont généralement de mauvais candidats. En l’absence d’une diffusion de la jonction, utilisez un cluster plus grand si l’erreur se produit.

### <a name="error-code-df-executor-conversion"></a>Code d’erreur : DF-Executor-Conversion

- **Message** : Échec de conversion en date ou heure en raison d’un caractère non valide
- **Causes** : Les données ne sont pas au format attendu
- **Recommandation** : Utilisez le type de données correct

### <a name="error-code-df-executor-invalidcolumn"></a>Code d’erreur : DF-Executor-InvalidColumn
- **Message** : Le nom de colonne doit être spécifié dans la requête, définissez un alias si vous utilisez une fonction SQL
- **Causes** : Aucun nom de colonne n’a été spécifié
- **Recommandation** : Définissez un alias si vous utilisez une fonction SQL telle que min()/max(), etc.

### <a name="error-code-df-executor-drivererror"></a>Code d’erreur : DF-Executor-DriverError
- **Message** : INT96 est un type d’horodatage hérité qui n’est pas pris en charge par le flux de données ADF. Envisagez de mettre à niveau le type de colonne vers les types les plus récents.
- **Causes** : Erreur de pilote
- **Recommandation** : INT96 est un type d’horodatage hérité qui n’est pas pris en charge par le flux de données ADF. Envisagez de mettre à niveau le type de colonne vers les types les plus récents.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Code d’erreur : DF-Executor-BlockCountExceedsLimitError
- **Message** : Le nombre de blocs non validés ne peut pas dépasser la limite maximale de 100 000 blocs. Vérifiez la configuration de l’objet blob.
- **Causes** : Il peut y avoir un maximum de 100 000 blocs non validés dans un objet blob.
- **Recommandation** : Pour plus d’informations à ce sujet, contactez l’équipe produit Microsoft.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Code d’erreur : DF-Executor-PartitionDirectoryError
- **Message** : Le chemin source spécifié comporte plusieurs répertoires partitionnés (par exemple, <Source Path>/<Répertoire racine de partition 1>/a=10/b=20, <Source Path>/<Répertoire racine de partition 2>/c=10/d=30) ou un répertoire partitionné avec un autre fichier ou un répertoire non partitionné (par exemple, <Source Path>/<Répertoire racine de partition 1>/a=10/b=20, <Source Path>/Répertoire 2/fichier1). Supprimez le répertoire racine de la partition du chemin source et lisez-le par le biais d’une transformation source distincte.
- **Causes** : Le chemin source comporte plusieurs répertoires partitionnés ou un répertoire partitionné avec un autre fichier ou un répertoire non partitionné.
- **Recommandation** : Supprimez le répertoire racine partitionné du chemin source et lisez-le par le biais d’une transformation source distincte.

### <a name="error-code-df-executor-outofmemoryerror"></a>Code d’erreur : DF-Executor-OutOfMemoryError
- **Message** : Le cluster a rencontré un problème de mémoire insuffisante pendant l’exécution, réessayez en utilisant un runtime d’intégration avec un nombre de cœurs plus élevé et/ou un type de calcul à mémoire optimisée.
- **Causes** : Le cluster manque de mémoire.
- **Recommandation** : Les clusters de débogage sont destinés au développement. Exploitez l’échantillonnage des données, le type et la taille de calcul appropriés pour exécuter la charge utile. Reportez-vous au [guide des performances des flux de données de mappage](concepts-data-flow-performance.md) pour optimiser les performances.

### <a name="error-code-df-executor-invalidtype"></a>Code d’erreur : DF-Executor-InvalidType
- **Message** : Vérifiez que le type de paramètre correspond au type de valeur transmis. Le passage de paramètres flottants à partir de pipelines n’est pas pris en charge actuellement.
- **Causes** : Types de données incompatibles entre le type déclaré et la valeur de paramètre réelle.
- **Recommandation** : Vérifiez que les valeurs de paramètre passées dans un flux de données correspondent au type déclaré.

### <a name="error-code-df-executor-columnunavailable"></a>Code d’erreur : DF-Executor-ColumnUnavailable
- **Message** : Le nom de colonne utilisé dans l’expression n’est pas disponible ou n’est pas valide.
- **Causes** : Nom de colonne non valide ou non disponible utilisé dans des expressions
- **Recommandation** : Vérifiez le(s) nom(s) de colonne utilisé(s) dans les expressions.

### <a name="error-code-df-executor-parseerror"></a>Code d’erreur : DF-Executor-ParseError
- **Message** : Impossible d’analyser l’expression.
- **Causes** : L’expression contient des erreurs d’analyse en raison de la mise en forme.
- **Recommandation** : Vérifiez la mise en forme dans l’expression.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Code d’erreur : DF-Executor-SystemImplicitCartesian
- **Message** : Le produit cartésien implicite pour la jointure INNER n’est pas pris en charge. Utilisez plutôt CROSS JOIN. Les colonnes utilisées dans la jointure doivent créer une clé unique pour les lignes.
- **Causes** : Le produit cartésien implicite pour la jointure INNER entre plans logiques n’est pas pris en charge. Si les colonnes utilisées dans la jointure créent la clé unique
- **Recommandation** : Pour les jointures non basées sur l’égalité, vous devez opter pour CROSS JOIN.

### <a name="error-code-df-executor-systeminvalidjson"></a>Code d’erreur : DF-Executor-SystemInvalidJson
- **Message** : Erreur d’analyse JSON, encodage ou multiligne non pris en charge.
- **Causes** : Problèmes possibles avec le fichier JSON (encodage non pris en charge, octets endommagés ou utilisation de la source JSON comme document unique sur de nombreuses lignes imbriquées).
- **Recommandation** : Vérifiez que l’encodage du fichier JSON est pris en charge. Sur la transformation source qui utilise un jeu de données JSON, développez « Paramètres JSON » et activez « Un seul document ».



### <a name="error-code-df-executor-conversion"></a>Code d’erreur : DF-Executor-Conversion
- **Message** : Échec de conversion en date ou heure en raison d’un caractère non valide
- **Causes** : Les données ne sont pas au format attendu
- **Recommandation** :  Utilisez le type de données correct.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Code d’erreur : DF-Executor-BlockCountExceedsLimitError
- **Message** : Le nombre de blocs non validés ne peut pas dépasser la limite maximale de 100 000 blocs. Vérifiez la configuration de l’objet blob.
- **Causes** : Il peut y avoir un maximum de 100 000 blocs non validés dans un objet blob.
- **Recommandation** : Pour plus d’informations à ce sujet, contactez l’équipe produit Microsoft.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Code d’erreur : DF-Executor-PartitionDirectoryError
- **Message** : Le chemin source spécifié comporte plusieurs répertoires partitionnés (par exemple, *<Source Path>/<Répertoire racine de partition 1>/a=10/b=20, <Source Path>/<Répertoire racine de partition 2>/c=10/d=30*) ou un répertoire partitionné avec un autre fichier ou un répertoire non partitionné (par exemple, *<Source Path>/<Répertoire racine de partition 1>/a=10/b=20, <Source Path>/Répertoire 2/fichier1*). Supprimez le répertoire racine de la partition du chemin source et lisez-le par le biais d’une transformation source distincte.
- **Causes** : Le chemin source comporte plusieurs répertoires partitionnés ou un répertoire partitionné avec un autre fichier ou un répertoire non partitionné.
- **Recommandation** : Supprimez le répertoire racine partitionné du chemin source et lisez-le par le biais d’une transformation source distincte.

### <a name="error-code-getcommand-outputasync-failed"></a>Code d’erreur : Échec de GetCommand OutputAsync
- **Message** : Lors du débogage du flux de données et de l’aperçu des données : Échec de GetCommand OutputAsync avec...
- **Causes** : Il s’agit d’une erreur de service principal. Vous pouvez retenter l’opération et redémarrer votre session de débogage.
- **Recommandation** : Si la nouvelle tentative et le redémarrage ne permettent pas de résoudre le problème, contactez le support technique. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Code d’erreur : DF-Executor-OutOfMemoryError
 
- **Message** : Le cluster a rencontré un problème de mémoire insuffisante pendant l’exécution, réessayez en utilisant un runtime d’intégration avec un nombre de cœurs plus élevé et/ou un type de calcul à mémoire optimisée.
- **Causes** : Le cluster manque de mémoire.
- **Recommandation** : Les clusters de débogage sont destinés au développement. Exploitez l’échantillonnage des données, le type et la taille de calcul appropriés pour exécuter la charge utile. Consultez le [Guide des performances de flux de données](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-performance) pour optimiser les performances des flux de données.

### <a name="error-code-df-executor-illegalargument"></a>Code d’erreur : DF-Executor-illegalArgument
- **Message** : Vérifiez que la clé d’accès de votre service lié est correcte.
- **Causes** : Nom de compte ou clé d’accès incorrect(e).
- **Recommandation** : Indiquez le nom du compte ou la clé d’accès approprié(e).

- **Message** : Vérifiez que la clé d’accès de votre service lié est correcte.
- **Causes** : Nom de compte ou clé d’accès incorrects
- **Recommandation** : Vérifiez que le nom du compte ou la clé d’accès spécifiés dans votre service lié sont corrects. 

### <a name="error-code-df-executor-invalidtype"></a>Code d’erreur : DF-Executor-InvalidType
- **Message** : Vérifiez que le type de paramètre correspond au type de valeur transmis. Le passage de paramètres flottants à partir de pipelines n’est pas pris en charge actuellement.
- **Causes** : Types de données incompatibles entre le type déclaré et la valeur de paramètre réelle.
- **Recommandation** : Indiquez les types de données appropriés.

### <a name="error-code-df-executor-columnunavailable"></a>Code d’erreur : DF-Executor-ColumnUnavailable
- **Message** : Le nom de colonne utilisé dans l’expression n’est pas disponible ou n’est pas valide.
- **Causes** : Nom de colonne non valide ou non disponible utilisé dans des expressions.
- **Recommandation** : Vérifiez le(s) nom(s) de colonne utilisé(s) dans les expressions.


### <a name="error-code-df-executor-parseerror"></a>Code d’erreur : DF-Executor-ParseError
- **Message** : Impossible d’analyser l’expression.
- **Causes** : L’expression contient des erreurs d’analyse en raison de la mise en forme.
- **Recommandation** : Vérifiez la mise en forme dans l’expression.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Code d’erreur : DF-Executor-OutOfDiskSpaceError
- **Message** : Erreur interne du serveur
- **Causes** : Le cluster manque d’espace disque.
- **Recommandation** : Relancez le pipeline. Si le problème persiste, contactez le support.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Code d’erreur : DF-Executor-StoreIsNotDefined
- **Message** : La configuration de magasin n’est pas définie. Cette erreur est peut-être provoquée par une attribution incorrecte de paramètre dans le pipeline.
- **Causes** : Indéterminé
- **Recommandation** : Vérifiez l’attribution de la valeur du paramètre dans le pipeline. L’expression de paramètre peut contenir des caractères non valides.

### <a name="error-code-df-excel-invalidconfiguration"></a>Code d’erreur : DF-Excel-InvalidConfiguration
- **Message** : Le nom ou l’index de la feuille de calcul Excel est requis.
- **Causes** : Indéterminé
- **Recommandation** : Vérifiez la valeur du paramètre et spécifiez le nom ou l’index de la feuille pour lire les données Excel.

- **Message** : Le nom et l’index d’une feuille Excel ne peuvent pas exister en même temps.
- **Causes** : Indéterminé
- **Recommandation** : Vérifiez la valeur du paramètre et spécifiez le nom ou l’index de la feuille pour lire les données Excel.

- **Message** : Une plage non valide est fournie.
- **Causes** : Indéterminé
- **Recommandation** : Vérifiez la valeur du paramètre et spécifiez une plage valide par référence : [Propriétés Excel](https://docs.microsoft.com/azure/data-factory/format-excel#dataset-properties).

- **Message** : Un fichier Excel non valide est fourni alors que seuls les fichiers .xlsx et .xls sont pris en charge
- **Causes** : Indéterminé
- **Recommandation** : Assurez-vous que l’extension du fichier Excel est .xlsx ou .xls.


 ### <a name="error-code-df-excel-invaliddata"></a>Code d’erreur : DF-Excel-InvalidData
- **Message** : La feuille de calcul Excel n’existe pas.
- **Causes** : Indéterminé
- **Recommandation** : Vérifiez la valeur du paramètre et spécifiez un nom ou un index valide pour la feuille afin de lire les données Excel.

- **Message** : La lecture des fichiers Excel avec un schéma différent n’est pas prise en charge pour le moment.
- **Causes** : Indéterminé
- **Recommandation** : Utilisez un fichier Excel correct.

- **Message** : Type de données non pris en charge.
- **Causes** : Indéterminé
- **Recommandation** : Utilisez les types de données appropriés au fichier Excel.

### <a name="error-code-4502"></a>Code d’erreur : 4502
- **Message** : Des exécutions de MappingDataflow simultanées importantes entraînent des échecs en raison de la limitation sous le runtime d’intégration.
- **Causes** : Un grand nombre d’exécutions d’activités de dataflow se déroulent simultanément sur le runtime d’intégration. Découvrez-en plus sur les [limites d’Azure Data Factory](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#data-factory-limits).
- **Recommandation** : Si vous cherchez à exécuter davantage d’activités de flux de données en parallèle, distribuez-les sur plusieurs runtimes d’intégration.


### <a name="error-code-invalidtemplate"></a>Code d’erreur : InvalidTemplate
- **Message** : L’expression de pipeline ne peut pas être évaluée.
- **Causes** : L’expression de pipeline passée dans l’activité de dataflow n’est pas traitée correctement en raison d’une erreur de syntaxe.
- **Recommandation** : Dans la supervision des activités, vérifiez l’expression liée à votre activité.

### <a name="error-code-2011"></a>Code d’erreur : 2011
- **Message** : L’activité était exécutée sur Azure Integration Runtime et n’a pas pu déchiffrer les informations d’identification du magasin de données ou du calcul connecté via un runtime d’intégration auto-hébergé. Vérifiez la configuration des services liés associés à cette activité et veillez à utiliser le type de runtime d’intégration approprié.
- **Causes** : Le flux de données ne prend pas en charge les services liés avec le runtime d’intégration auto-hébergé.
- **Recommandation** : Configurez le flux de données pour qu’il s’exécute sur le runtime d’intégration avec « Réseau virtuel managé ».

## <a name="miscellaneous-troubleshooting-tips"></a>Conseils pour la résolution de problèmes divers
- **Problème** : Exception inattendue et échec de l’exécution
    - **Message** : Lors de l’exécution de l’activité de flux de données : Exception inattendue et échec de l’exécution.
    - **Causes** : Il s’agit d’une erreur de service principal. Vous pouvez retenter l’opération et redémarrer votre session de débogage.
    - **Recommandation** : Si la nouvelle tentative et le redémarrage ne permettent pas de résoudre le problème, contactez le support technique.

-  **Problème** : Déboguer l’aperçu des données Aucune donnée de sortie lors de la jointure
    - **Message** : Il existe un nombre élevé de valeurs Null ou manquantes qui peuvent être dues à l’échantillonnage d’un trop petit nombre de lignes. Essayez de mettre à jour la limite de lignes de débogage et d’actualiser les données.
    - **Causes** : La condition de jointure ne correspond à aucune ligne ou a généré un nombre élevé de valeurs NULL lors de l’aperçu des données.
    - **Recommandation** : Accédez aux paramètres de débogage et augmentez le nombre de lignes dans la limite de lignes sources. Assurez-vous d’avoir sélectionné un runtime d’intégration Azure avec un cluster de flux de données suffisamment important pour traiter plus de données.
    
- **Problème** : Erreur de validation à la source avec des fichiers CSV multilignes 
    - **Message** : Vous pourriez voir l’un des messages d’erreur suivants :
        - La dernière colonne est null ou manquante.
        - Échec de la validation de schéma à la source.
        - L’importation de schéma ne s’affiche pas correctement dans l’interface utilisateur et la dernière colonne contient un caractère de nouvelle ligne dans le nom.
    - **Causes** : Actuellement, dans le flux de données de mappage, la source CSV multiligne ne fonctionne pas avec le délimiteur de ligne \r\n. Parfois, des lignes supplémentaires au niveau des retours chariot renvoient des valeurs sources limites. 
    - **Recommandation** : Générez le fichier à la source avec \n comme délimiteur de ligne plutôt que \r\n. Vous pouvez aussi utiliser l’activité de copie pour convertir un fichier CSV utilisant le délimiteur de ligne \r\n en fichier utilisant le délimiteur \n .

## <a name="general-troubleshooting-guidance"></a>Instructions générales pour la résolution des problèmes
1. Vérifiez l’état de vos connexions de jeu de données. Dans chaque transformation de source et récepteur, visitez le service lié pour chaque jeu de données que vous utilisez et testez les connexions.
2. Vérifiez l’état de vos connexions aux fichiers et aux tables à partir du concepteur de flux de données. Activez le débogage, puis cliquez sur Aperçu des données dans vos transformations de la source pour vérifier que vous pouvez accéder à vos données.
3. Si tout semble correct dans l’aperçu des données, accédez au concepteur de pipeline et mettez votre flux de données dans une activité de pipeline. Déboguez le pipeline pour un test de bout en bout.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
