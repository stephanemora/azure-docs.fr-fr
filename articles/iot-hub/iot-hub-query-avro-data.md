---
title: Interroger les données Avro à l’aide d’Azure Data Lake Analytics | Microsoft Docs
description: Utilisez les propriétés du corps de message pour acheminer les données de télémétrie de l’appareil vers le stockage d’objets blob et interrogez les données au format Avro enregistrées dans le stockage d’objets blob.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: 98a30155c73a937042b4bea6568543fb5152d748
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726634"
---
# <a name="query-avro-data-using-azure-data-lake-analytics"></a>Interroger les données Avro à l’aide d’Azure Data Lake Analytics

Cet article vous explique comment interroger les données Avro afin d’acheminer efficacement les messages entre Azure IoT Hub et les services Azure. Suite à l’annonce du billet de blog [Routage des messages IoT Hub Azure : désormais possible avec le routage sur le corps du message], IoT Hub prend en charge le routage sur les propriétés ou le corps du message. Consultez également [Routage sur les corps de message][Routing on message bodies]. 

La principale difficulté est que lorsque Azure IoT Hub achemine les messages vers le stockage d’objets blob, IoT Hub écrit le contenu au format Avro, qui a à la fois un corps de message et des propriétés de message. Notez que IoT Hub prend uniquement en charge l’écriture de données sur le stockage d’objets blob au format de données Avro, or ce format n’est pas utilisé pour les autres points de terminaison. Reportez-vous à [Lors de l’utilisation de conteneurs de stockage Azure][When using Azure storage containers]. Si le format Avro est idéal pour la conservation des données/messages, il n’est très adapté pour interroger les données. En comparaison, le format JSON ou CSV est beaucoup plus pratique pour interroger des données.

Pour résoudre ce problème, vous pouvez utiliser la plupart des modèles de Big Data pour transformer et mettre à l’échelle les données afin de répondre aux besoins et aux formats en matière de Big Data non relationnelles. Un de ces modèles (un modèle de « paiement à la requête ») est Azure Data Lake Analytics (ADLA). C’est le sujet de cet article. Bien que vous pourriez facilement exécuter la requête dans Hadoop ou d’autres solutions, ADLA est souvent mieux adapté pour cette approche de « paiement à la requête ». Il existe un « extracteur » pour Avro dans U-SQL. Consultez [Exemple U-SQL Avro].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Interroger et exporter les données Avro vers un fichier CSV
La section vous explique comment interroger des données Avro et les exporter vers un fichier CSV dans le stockage d’objets Blob Azure, bien qu’il soit facile de placer les données dans d’autres référentiels ou magasins de données.

1. Configurez Azure IoT Hub pour acheminer les données vers un point de terminaison de stockage d’objets Blob Azure à l’aide d’une propriété dans le corps du message pour sélectionner les messages.

    ![Capture d’écran de l’étape 1a][img-query-avro-data-1a]

    ![Capture d’écran de l’étape 1b][img-query-avro-data-1b]

2. Assurez-vous que votre appareil dispose du codage, du type de contenu et des données nécessaires dans les propriétés ou le corps du message, comme indiqué dans la documentation du produit. Lorsqu’ils sont affichés dans Device Explorer (voir ci-dessous), vous pouvez vérifier que ces attributs sont correctement définis.

    ![Capture d’écran de l’étape 2][img-query-avro-data-2]

3. Configurez Azure Data Lake Store (ADLS) et une instance Azure Data Lake Analytics. Alors que Azure IoT Hub ne prend pas en charge l’acheminement vers Azure Data Lake Store, ADLA le nécessite.

    ![Capture d’écran de l’étape 3][img-query-avro-data-3]

4. Dans ADLA, configurez le stockage d’objets blob Azure comme un magasin supplémentaire, le même stockage d’objets blob que celui vers lequel Azure IoT Hub achemine les données.

    ![Capture d’écran de l’étape 4][img-query-avro-data-4]
 
5. Comme indiqué dans la section [Exemple U-SQL Avro], 4 DLL sont nécessaires.  Téléchargez ces fichiers vers un emplacement dans votre ADLS.

    ![Capture d’écran de l’étape 5][img-query-avro-data-5] 

6. Dans Visual Studio, créez un projet U-SQL.
 
    ![Capture d’écran de l’étape 6][img-query-avro-data-6]

7. Copiez le contenu du script suivant et collez-le dans le fichier qui vient d’être créé. Modifiez les 3 sections en surbrillance : votre compte ADLA, les chemins d’accès aux DLL associés et le chemin d’accès à votre compte de stockage.
    
    ![Capture d’écran de l’étape 7a][img-query-avro-data-7a]

    Le script U-SQL réel pour une sortie simple au format CSV :
    
    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
        ""type"":""record"",
        ""name"":""Message"",
        ""namespace"":""Microsoft.Azure.Devices"",
        ""fields"":[{
        ""name"":""EnqueuedTimeUtc"",
        ""type"":""string""
        },
        {
        ""name"":""Properties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""SystemProperties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""Body"",
        ""type"":[""null"",""bytes""]
        }
        ]
        }");

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    En exécutant le script ci-dessous, ADLA a mis 5 minutes avec une limitation à 10 unités d’analyse et a traité 177 fichiers, dont la sortie est résumée dans un fichier CSV.
    
    ![Capture d’écran de l’étape 7b][img-query-avro-data-7b]

    En affichant la sortie, vous pouvez voir que le contenu Avro a été converti en un fichier CSV. Si vous souhaitez analyser le format JSON, passez à l’étape 8.
    
    ![Capture d’écran de l’étape 7c][img-query-avro-data-7c]

    
8. La plupart des messages IoT sont au format JSON.  En ajoutant les lignes suivantes, vous pouvez analyser le message au format JSON, ce qui vous permet d’ajouter les clauses WHERE et obtenir uniquement les données nécessaires.

    ```sql
       @jsonify = SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

9. En affichant la sortie, vous pouvez maintenant voir les colonnes de chaque élément dans la commande select. 
    
    ![Capture d’écran de l’étape 8][img-query-avro-data-8]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous allez savoir comment interroger les données Avro afin d’acheminer efficacement les messages entre Azure IoT Hub et les services Azure.

Pour voir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Accélérateur de solution de surveillance à distance Azure IoT][lnk-iot-sa-land].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur d’IoT Hub].

Pour en savoir plus sur le routage des messages dans IoT Hub, consultez [Envoyer et recevoir des messages avec IoT Hub][lnk-devguide-messaging].

<!-- Images -->
[img-query-avro-data-1a]: ./media/iot-hub-query-avro-data/query-avro-data-1a.png
[img-query-avro-data-1b]: ./media/iot-hub-query-avro-data/query-avro-data-1b.png
[img-query-avro-data-2]: ./media/iot-hub-query-avro-data/query-avro-data-2.png
[img-query-avro-data-3]: ./media/iot-hub-query-avro-data/query-avro-data-3.png
[img-query-avro-data-4]: ./media/iot-hub-query-avro-data/query-avro-data-4.png
[img-query-avro-data-5]: ./media/iot-hub-query-avro-data/query-avro-data-5.png
[img-query-avro-data-6]: ./media/iot-hub-query-avro-data/query-avro-data-6.png
[img-query-avro-data-7a]: ./media/iot-hub-query-avro-data/query-avro-data-7a.png
[img-query-avro-data-7b]: ./media/iot-hub-query-avro-data/query-avro-data-7b.png
[img-query-avro-data-7c]: ./media/iot-hub-query-avro-data/query-avro-data-7c.png
[img-query-avro-data-8]: ./media/iot-hub-query-avro-data/query-avro-data-8.png

<!-- Links -->
[Routage des messages IoT Hub Azure : désormais possible avec le routage sur le corps du message]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[Exemple U-SQL Avro]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.md
[Guide du développeur d’IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
