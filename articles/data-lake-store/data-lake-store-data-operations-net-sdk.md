---
title: 'Kit de développement logiciel (SDK) .NET : opérations de gestion du système de fichiers sur Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Utilisez le Kit de développement logiciel (SDK) .NET Azure Data Lake Storage Gen1 pour effectuer des opérations de gestion du système de fichiers sur Data Lake Storage Gen1, comme la création de dossiers, etc.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 929ce0d984e53586c46f15f21b9e5c90c6a34771
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402300"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Opérations de gestion du système de fichiers sur Azure Data Lake Storage Gen1 à l’aide de .NET
> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET](data-lake-store-data-operations-net-sdk.md)
> * [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Dans cet article, vous allez découvrir comment réaliser des opérations de gestion du système de fichiers sur Data Lake Storage Gen1 avec le SDK .NET. Les opérations de gestion du système de fichiers comprennent la création de dossiers dans un compte Data Lake Storage Gen1, le chargement et le téléchargement de fichiers, etc.

Pour obtenir des instructions sur l’exécution des opérations de gestion des comptes sur Data Lake Storage Gen1 à l’aide du kit de développement logiciel (SDK) .NET, consultez la section relative aux [opérations de gestion des comptes sur Data Lake Storage Gen1 à l’aide du kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Prérequis
* **Visual Studio 2013, 2015 ou 2017**. Les instructions ci-dessous reposent sur Visual Studio 2017.

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Compte Azure Data Lake Storage Gen1**. Pour savoir comment créer un compte, consultez [Prise en main d’Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>Créer une application .NET
L’exemple de code disponible [sur GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) vous guide tout au long du processus de création de fichiers dans le magasin, de concaténation de fichiers, de téléchargement d’un fichier et de suppression de certains fichiers du compte. Cette section de l’article vous guide tout au long des principales parties du code.

1. Ouvrez Visual Studio et créez une application console.
2. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.
3. Dans **Nouveau projet**, entrez ou sélectionnez les valeurs suivantes :

   | Propriété | Valeur |
   | --- | --- |
   | Catégorie |Modèles/Visual C#/Windows |
   | Modèle |Application console |
   | NOM |CreateADLApplication |

4. Cliquez sur **OK** pour créer le projet.

5. Ajoutez les packages NuGet à votre projet.

   1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis cliquez sur **Gérer les packages NuGet**.
   2. Dans l’onglet **Gestionnaire de package NuGet**, vérifiez que **Source du package** a la valeur **nuget.org** et que la case **Inclure la version préliminaire** est cochée.
   3. Recherchez et installez les packages NuGet suivants :

      * `Microsoft.Azure.DataLake.Store` : ce didacticiel utilise v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` : ce didacticiel utilise v2.3.1.
    
    Fermez le **Gestionnaire de package NuGet**.

6. Ouvrez **Program.cs**, supprimez le code existant, puis insérez les instructions suivantes pour ajouter des références aux espaces de noms.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Déclarez les variables comme indiqué ci-dessous, et fournissez les valeurs des espaces réservés. En outre, assurez-vous que le chemin d’accès local et le nom de fichier que vous fournissez ici existent sur l’ordinateur.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";        
            }
        }

Dans les sections suivantes de cet article, vous pouvez découvrir comment utiliser les méthodes .NET pour effectuer des opérations telles que l’authentification des utilisateurs et le chargement de fichiers.

## <a name="authentication"></a>Authentification

* Pour en savoir plus sur l’authentification des utilisateurs accédant à votre application, consultez la section relative à [l’authentification de l’utilisateur avec Data Lake Storage Gen1 à l’aide du Kit de développement logiciel (SDK) .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Pour en savoir plus sur l’authentification entre les services dans le cadre de votre application, consultez la section relative à [l’authentification entre les services avec Data Lake Storage Gen1 à l’aide du Kit de développement logiciel (SDK) .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Créer un objet client
L’extrait de code suivant permet de créer l’objet client filesystem Data Lake Storage Gen1 qui est utilisé pour adresser des requêtes au service.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Créer un fichier un répertoire
Ajoutez l’extrait de code suivant à votre application. Cet extrait de code permet d’ajouter un fichier, ainsi que tout répertoire parent qui n’existe pas.

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>Ajout à un fichier
L’extrait de code suivant permet d’ajouter des données à un fichier existant dans le compte Data Lake Storage Gen1.

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>Lire un fichier
L’extrait de code suivant permet de lire le contenu d’un fichier dans un compte Data Lake Storage Gen1.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Obtenir les propriétés de fichier
L’extrait de code suivant permet de renvoyer les propriétés associées à un fichier ou un répertoire.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

La définition de la méthode `PrintDirectoryEntry` n’est disponible que dans l’exemple [sur GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted). 

## <a name="rename-a-file"></a>Renommer un fichier
L’extrait de code suivant renomme un fichier existant dans un compte Data Lake Storage Gen1.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Énumérer un répertoire
L’extrait de code suivant permet d’énumérer des répertoires dans un compte Data Lake Storage Gen1

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

La définition de la méthode `PrintDirectoryEntry` n’est disponible que dans l’exemple [sur GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Supprimer des répertoires de manière récursive
L’extrait de code suivant permet de supprimer un répertoire et tous ses sous-répertoires de manière récursive.

    // Delete a directory and all its subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Exemples
Voici quelques exemples sur la façon d’utiliser le Kit de développement logiciel (SDK) Filesystem Data Lake Storage Gen1.
* [Exemple de base sur GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Exemple avancé sur GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Voir aussi
* [Opérations de gestion du compte sur Data Lake Storage Gen1 à l’aide du SDK .NET](data-lake-store-get-started-net-sdk.md)
* [Référence SDK .NET Data Lake Storage Gen1](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Étapes suivantes
* [Sécuriser les données dans Data Lake Storage Gen1](data-lake-store-secure-data.md)
