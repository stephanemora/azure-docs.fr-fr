---
title: Résoudre les problèmes liés au connecteur Format Parquet
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés au connecteur Format Parquet dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: b12666fdd66b3c85702b7222f2f2edca7136e323
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062749"
---
# <a name="troubleshoot-the-parquet-format-connector-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés au connecteur Format Parquet dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur Format Parquet dans Azure Data Factory et Azure Synapse.

## <a name="error-code-parquetjavainvocationexception"></a>Code d’erreur : ParquetJavaInvocationException

- **Message** : `An error occurred when invoking java, message: %javaException;.`

- **Causes et recommandations** : Différentes causes peuvent être à l’origine de cette erreur. Consultez la liste ci-dessous pour obtenir une analyse des causes possibles et des recommandations associées.

    | Analyse de la cause                                               | Recommandation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Lorsque le message d’erreur contient les chaînes « java.lang.OutOfMemory », « Java heap space » et « doubleCapacity », il s’agit généralement d’un problème de gestion de la mémoire dans une ancienne version du runtime d’intégration. | Si vous utilisez des runtimes d’intégration auto-hébergés et que la version est antérieure à 3.20.7159.1, nous vous recommandons une mise à niveau vers la version la plus récente. |
    | Lorsque le message d’erreur contient la chaîne « java.lang.OutOfMemory », le runtime d’intégration ne dispose pas de suffisamment de ressources pour traiter les fichiers. | Limitez les exécutions simultanées sur le runtime d’intégration. Pour les runtimes d’intégration auto-hébergés, effectuez un scale-up vers un ordinateur puissant doté d’une mémoire égale ou supérieure à 8 Go. |
    | Lorsque le message d’erreur contient la chaîne « NullPointerReference », cela peut être dû à une erreur temporaire. | Retentez l’opération. Si le problème persiste, contactez le support technique. |

## <a name="error-code-parquetinvalidfile"></a>Code d’erreur : ParquetInvalidFile

- **Message** : `File is not a valid Parquet file.`

- **Cause** : Il s’agit d’un problème de fichier Parquet.

- **Recommandation** :  Vérifiez que l’entrée est un fichier Parquet valide.

## <a name="error-code-parquetnotsupportedtype"></a>Code d’erreur : ParquetNotSupportedType

- **Message** : `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Cause** : Le format Parquet n’est pas pris en charge dans Azure Data Factory et les pipelines Synapse.

- **Recommandation** : Vérifiez les données sources en accédant à [Formats de fichier et codecs de compression pris en charge par l’activité de copie](./supported-file-formats-and-compression-codecs.md).

## <a name="error-code-parquetmisseddecimalprecisionscale"></a>Code d’erreur : ParquetMissedDecimalPrecisionScale

- **Message** : `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Cause** : La précision et l’échelle des nombres ont été analysées, mais aucune information de ce type n’a été fournie.

- **Recommandation** :  La source ne retourne pas les informations correctes de précision et d’échelle. Vérifiez les informations dans la colonne posant problème.

## <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Code d’erreur : ParquetInvalidDecimalPrecisionScale

- **Message** : `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Cause** : Le schéma n’est pas valide.

- **Recommandation** :  Vérifiez la précision et l’échelle de la colonne posant problème.

## <a name="error-code-parquetcolumnnotfound"></a>Code d’erreur : ParquetColumnNotFound

- **Message** : `Column %column; does not exist in Parquet file.`

- **Cause** : Le schéma source est incompatible avec le schéma du récepteur.

- **Recommandation** :  Vérifiez les mappages dans l’activité. Assurez-vous que la colonne source peut être mappée à la colonne du récepteur appropriée.

## <a name="error-code-parquetinvaliddataformat"></a>Code d’erreur : ParquetInvalidDataFormat

- **Message** : `Incorrect format of %srcValue; for converting to %dstType;.`

- **Cause** : Les données ne peuvent pas être converties dans le type spécifié dans mappings.source.

- **Recommandation** :  Revérifiez les données sources ou spécifiez le type de données approprié pour cette colonne dans le mappage des colonnes de l’activité de copie. Pour plus d’informations, consultez [Formats de fichier et de codecs de compression pris en charge par l’activité de copie](./supported-file-formats-and-compression-codecs.md).

## <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Code d’erreur : ParquetDataCountNotMatchColumnCount

- **Message** : `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Cause** :  Incompatibilité entre le nombre de colonnes sources et le nombre de colonnes du récepteur.

- **Recommandation** :  Revérifiez que le nombre de colonnes sources est identique au nombre de colonnes du récepteur dans « mappage ».

## <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Code d’erreur : ParquetDataTypeNotMatchColumnType

- **Message** : `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Cause** : Les données de la source ne peuvent pas être converties dans le type défini dans le récepteur.

- **Recommandation** :  Spécifiez un type correct dans mappage.sink.

## <a name="error-code-parquetbridgeinvaliddata"></a>Code d’erreur : ParquetBridgeInvalidData

- **Message** : `%message;`

- **Cause** : La valeur des données a dépassé la limite.

- **Recommandation** :  Retentez l’opération. Si le problème persiste, contactez-nous.

## <a name="error-code-parquetunsupportedinterpretation"></a>Code d’erreur : ParquetUnsupportedInterpretation

- **Message** : `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Cause** : Ce scénario n’est pas pris en charge.

- **Recommandation** :  « ParquetInterpretFor » ne doit pas être « sparkSql ».

## <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Code d’erreur : ParquetUnsupportFileLevelCompressionOption

- **Message** : `File level compression is not supported for Parquet.`

- **Cause** : Ce scénario n’est pas pris en charge.

- **Recommandation** :  Supprimez « CompressionType » dans la charge utile.

## <a name="error-code-usererrorjniexception"></a>Code d’erreur : UserErrorJniException

- **Message** : `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Cause** : Une machine virtuelle Java ne peut pas être créée, car des arguments (généraux) non conformes sont définis.

- **Recommandation** :  Connectez-vous à la machine qui héberge *chaque nœud* de votre IR auto-hébergé. Vérifiez que la variable système est définie correctement, comme suit : `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G`. Redémarrez tous les nœuds IR, puis réexécutez le pipeline.

## <a name="arithmetic-overflow"></a>Dépassement arithmétique

- **Symptômes** : Un message d’erreur s’est produit pendant la copie de fichiers Parquet : `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Cause** : Pour l’heure, seules les décimales d’une précision <= 38 et une longueur de la partie entière <= 20 sont prises en charge lors de la copie de fichiers d’Oracle vers Parquet. 

- **Résolution** : Pour contourner le problème, vous pouvez convertir toutes les colonnes avec ce problème en VARCHAR2.

## <a name="no-enum-constant"></a>Absence de constante enum

- **Symptômes** : Un message d’erreur s’est affiché pendant la copie des données au format Paquet : `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` ou `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test`.

- **Cause** : 

    Le problème peut être dû à la présence d’espaces blancs ou de caractères spéciaux non pris en charge (comme ,;{}()\n\t=) dans le nom de colonne, car Parquet ne prend pas en charge un tel format. 

    Par exemple, le nom de colonne *contoso(test)* analyse le type entre crochets dans le [code](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");`. L’erreur est levée, car il n’existe pas de type « test ».

    Pour vérifier les types pris en charge, accédez au site GitHub [apache/parquet-mr site](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Résolution** : 

    Revérifiez ce qui suit :
    - Le nom de la colonne du récepteur contient des espaces blancs.
    - La première ligne contenant des espaces blancs sert de nom de colonne.
    - Le type OriginalType est pris en charge. Essayez d’éviter d’utiliser ces caractères spéciaux : `,;{}()\n\t=`. 

## <a name="error-code-parquetdatetimeexceedlimit"></a>Code d’erreur : ParquetDateTimeExceedLimit

- **Message** : `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Cause** : Si la valeur DateHeure est « 0001-01-01 00:00:00 », cela peut être dû à la différence entre le calendrier julien et le calendrier grégorien. Pour plus d’informations, consultez [Différence entre les dates du calendrier julier et du calendrier grégorien proleptique](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Résolution** : Vérifiez la valeur des graduations et évitez d’utiliser la valeur DateTime « 0001-01-01 00:00:00 ».

## <a name="error-code-parquetinvalidcolumnname"></a>Code d’erreur : ParquetInvalidColumnName

- **Message** : `The column name is invalid. Column name cannot contain these character:[,;{}()\n\t=]`

- **Cause** : Le nom du domaine contient des caractères non valides.

- **Résolution** : ajoutez ou modifiez le mappage de colonnes pour que le nom de la colonne réceptrice soit valide.

## <a name="the-file-created-by-the-copy-data-activity-extracts-a-table-that-contains-a-varbinary-max-column"></a>Le fichier créé par l’activité de copie de données extrait une table qui contient une colonne varbinary(max)

- **Symptômes** : Le fichier Parquet créé par l’activité de copie de données extrait une table qui contient une colonne varbinary(max).

- **Cause** : Ce problème est causé par le bogue de la bibliothèque Parquet-mr concernant la lecture des grandes colonnes. 

- **Résolution** : Essayez de générer des fichiers plus petits (taille < 1 Go) avec une limite de 1000 lignes par fichier.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
