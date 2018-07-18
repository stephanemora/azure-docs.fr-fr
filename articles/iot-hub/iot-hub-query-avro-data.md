---
title: Interroger les données Avro à l’aide d’Azure Data Lake Analytics | Microsoft Docs
description: Utilisez les propriétés du corps de message pour acheminer les données de télémétrie de l’appareil vers le stockage Blob et interrogez les données au format Avro enregistrées dans le stockage Blob.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: c56b567498047ee996018675134c252ec1de7e0c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081366"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Interroger les données Avro à l’aide d’Azure Data Lake Analytics

Cet article vous explique comment interroger les données Avro afin d’acheminer efficacement les messages entre Azure IoT Hub et les services Azure. Comme annoncé dans le billet de blog [Routage des messages IoT Hub Azure : désormais possible avec le routage sur le corps du message], IoT Hub prend en charge le routage sur les propriétés ou le corps du message. Pour plus d’informations, consultez [Routage sur les corps de message][Routing on message bodies]. 

La principale difficulté est que lorsque Azure IoT Hub achemine les messages vers le stockage Blob Azure, IoT Hub écrit le contenu au format Avro, qui a à la fois une propriété de corps de message et une propriété de message. IoT Hub prend en charge l’écriture de données sur le stockage Blob uniquement au format de données Avro, or ce format n’est pas utilisé pour les autres points de terminaison. Pour plus d’informations, consultez [Lors de l’utilisation de conteneurs de stockage Azure][When using Azure storage containers]. Bien que le format Avro soit exceptionnellement adapté pour la conservation des messages et des données, il est moins adapté pour l’interrogation des données. En comparaison, le format JSON ou CSV est beaucoup plus pratique pour interroger des données.

Pour résoudre ce problème, vous pouvez utiliser la plupart des modèles de Big Data pour transformer et mettre à l’échelle les données afin de répondre aux besoins et aux formats en matière de Big Data non relationnelles. Un de ces modèles (un modèle de « paiement à la requête ») est Azure Data Lake Analytics, qui constitue l’objet principal de cet article. Bien que vous puissiez facilement exécuter la requête dans Hadoop ou d’autres solutions, Data Lake Analytics est souvent mieux adapté pour cette approche de « paiement à la requête ». 

Il existe un « extracteur » pour Avro dans U-SQL. Pour plus d’informations, consultez [Exemple Avro U-SQL].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Interroger et exporter les données Avro vers un fichier CSV
Dans cette section, vous interrogez des données Avro et les exportez vers un fichier CSV dans le stockage Blob Azure, bien qu’il soit facile de placer les données dans d’autres référentiels ou magasins de données.

1. Configurez Azure IoT Hub pour acheminer les données vers un point de terminaison de stockage Blob Azure à l’aide d’une propriété dans le corps du message pour sélectionner les messages.

    ![Section « Points de terminaison personnalisés »][img-query-avro-data-1a]

    ![Commande Routes][img-query-avro-data-1b]

2. Assurez-vous que votre appareil dispose du codage, du type de contenu et des données nécessaires dans les propriétés ou le corps du message, comme indiqué dans la documentation du produit. Lorsque vous affichez ces attributs dans Device Explorer, comme illustré ici, vous pouvez vérifier qu’ils sont correctement définis.

    ![Volet de données Event Hub][img-query-avro-data-2]

3. Configurez une instance Azure Data Lake Store et une instance Data Lake Analytics. Azure IoT Hub n’achemine pas vers une instance Data Lake Store, mais une instance Data Lake Analytics en requiert une.

    ![Instances Data Lake Store et Data Lake Analytics][img-query-avro-data-3]

4. Dans Data Lake Analytics, configurez le stockage Blob Azure comme un magasin supplémentaire, le même stockage Blob que celui vers lequel Azure IoT Hub achemine les données.

    ![Volet « Sources de données »][img-query-avro-data-4]
 
5. Comme indiqué dans [Exemple Avro U-SQL], vous avez besoin de quatre fichiers DLL. Chargez ces fichiers vers un emplacement dans votre instance Data Lake Store.

    ![Quatre fichiers DLL chargés][img-query-avro-data-5] 

6. Dans Visual Studio, créez un projet U-SQL.
 
    ![Créer un projet U-SQL][img-query-avro-data-6]

7. Collez le contenu du script suivant dans le fichier qui vient d’être créé. Modifiez les trois sections en surbrillance : votre compte Data Lake Analytics, les chemins d’accès aux DLL associés et le chemin d’accès correct à votre compte de stockage.
    
    ![Les trois sections à modifier][img-query-avro-data-7a]

    Le script U-SQL réel pour une sortie simple dans un fichier CSV :
    
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

    Data Lake Analytics a eu besoin de cinq minutes pour exécuter le script suivant, limité à 10 unités d’analytique et 177 fichiers traités. Le résultat est affiché dans la sortie du fichier CSV affiché dans l’image suivante :
    
    ![Résultats de la sortie dans un fichier CSV][img-query-avro-data-7b]

    ![Sortie convertie en fichier CSV][img-query-avro-data-7c]

    Pour analyser le JSON, passez à l’étape 8.
    
8. La plupart des messages IoT sont au format de fichier JSON. En ajoutant les lignes suivantes, vous pouvez analyser le message dans un fichier JSON, ce qui vous permet d’ajouter les clauses WHERE et d’obtenir uniquement les données nécessaires.

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

    La sortie affiche une colonne pour chaque élément dans la commande `SELECT`. 
    
    ![Sortie affichant une colonne pour chaque élément][img-query-avro-data-8]

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez découvert comment interroger les données Avro afin d’acheminer efficacement les messages entre Azure IoT Hub et les services Azure.

Pour obtenir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Accélérateur de solution de surveillance à distance Azure IoT][lnk-iot-sa-land].

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

[Exemple Avro U-SQL]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[Guide du développeur d’IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
