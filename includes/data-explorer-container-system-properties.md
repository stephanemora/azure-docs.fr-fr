---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779946"
---
### <a name="event-system-properties-mapping"></a>Mappage des propriétés du système d’événements

Si vous avez sélectionné **Propriétés système d'événement** dans la section **Source de données** du tableau ci-dessus, accédez à l’[interface utilisateur web](https://dataexplorer.azure.com/) pour exécuter la commande KQL appropriée en vue de la création d’un mappage approprié.

   **Pour le mappage csv :**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **Pour le mappage json :**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * Vous devez inclure toutes les propriétés sélectionnées dans le mappage. 
   > * L’ordre des propriétés est important dans le mappage csv. Les propriétés système doivent être répertoriées avant toutes les autres propriétés et dans l’ordre où elles apparaissent dans la liste déroulante **Propriétés système d’événement**.