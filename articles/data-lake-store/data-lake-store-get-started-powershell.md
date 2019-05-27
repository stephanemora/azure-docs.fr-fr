---
title: Bien démarrer avec Azure Data Lake Storage Gen1 à l’aide de PowerShell | Microsoft Docs
description: Utiliser Azure PowerShell pour créer un compte Data Lake Storage Gen1 et effectuer des opérations de base
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 5bec627f114a20033ca4364c39c048763df36b67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161486"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Bien démarrer avec Azure Data Lake Storage Gen1 à l’aide de PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Interface de ligne de commande Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Découvrez comment utiliser Azure PowerShell pour créer un compte Azure Data Lake Storage Gen1 et effectuer des opérations de base comme créer des dossiers, charger et télécharger des fichiers de données, supprimer votre compte, etc. Pour plus d’informations sur Data Lake Store Gen1, consultez [Vue d’ensemble de Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou version ultérieure**. Consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Authentication
Pour l’authentification auprès de Data Lake Storage Gen1, cet article utilise une approche plus simple où vous êtes invité à entrer les informations d’identification de votre compte Azure. Le niveau d’accès au compte et au système de fichiers Data Lake Storage Gen1 est alors régi par le niveau d’accès de l’utilisateur connecté. Cependant, il existe d’autres approches pour l’authentification sur Data Lake Storage Gen1, à savoir **l’authentification de l’utilisateur final** ou **l’authentification de service à service**. Pour obtenir des instructions et plus d’informations sur l’authentification, consultez [l’authentification de l’utilisateur final](data-lake-store-end-user-authenticate-using-active-directory.md) ou [l’authentification de service à service](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-data-lake-storage-gen1-account"></a>Créer un compte Data Lake Storage Gen1
1. Sur votre Bureau, ouvrez une nouvelle fenêtre Windows PowerShell. Entrez l’extrait de code suivant pour vous connecter à votre compte Azure, définir l’abonnement et inscrire le fournisseur Data Lake Storage Gen1. À l’invite de connexion, vérifiez que vous vous connectez en tant qu’administrateur/propriétaire de l’abonnement :

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Un compte Data Lake Storage Gen1 est associé à un groupe de ressources Azure. Commencez par créer un groupe de ressources Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Créer un groupe de ressources Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Créer un groupe de ressources Azure")
3. Créer un compte Data Lake Storage Gen1. Le nom que vous spécifiez doit contenir uniquement des lettres minuscules et des chiffres.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    ![Créer un compte Data Lake Storage Gen1](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Créer un compte Data Lake Storage Gen1")
4. Vérifiez que le compte a bien été créé.

        Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

    Le résultat de la cmdlet doit être **True**.

## <a name="create-directory-structures-in-your-data-lake-storage-gen1-account"></a>Créer des structures de répertoires dans votre compte Data Lake Storage Gen1
Vous pouvez créer des répertoires sous votre compte Data Lake Storage Gen1 pour gérer et stocker des données.

1. Spécifiez un répertoire racine.

        $myrootdir = "/"
2. Créez un répertoire appelé **mynewdirectory** sous la racine spécifiée.

        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
3. Vérifiez que le répertoire a bien été créé.

        Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir

    Ceci devrait afficher un résultat, comme illustré sur la capture d’écran suivante :

    ![Vérifier le répertoire](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Vérifier le répertoire")

## <a name="upload-data-to-your-data-lake-storage-gen1-account"></a>Charger des données sur votre compte Data Lake Storage Gen1
Vous pouvez charger des données sur Data Lake Storage Gen1 directement à la racine ou dans un répertoire que vous avez créé dans le compte. Les extraits de code de cette section montrent comment télécharger des exemples de données dans le répertoire (**mynewdirectory**) que vous avez créé dans la section précédente.

Si vous recherchez des exemples de données à charger, vous pouvez récupérer le dossier **Données Ambulance** dans le [Référentiel Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Téléchargez le fichier et stockez-le dans un répertoire local sur votre ordinateur, comme C:\sampledata\.

    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-storage-gen1-account"></a>Renommer, télécharger et supprimer des données de votre compte Data Lake Storage Gen1
Utilisez la commande suivante pour renommer un fichier :

    Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Utilisez la commande suivante pour télécharger un fichier :

    Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Utilisez la commande suivante pour supprimer un fichier :

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quand vous y êtes invité, entrez **Y** pour supprimer l’élément. Si vous avez plusieurs fichiers à supprimer, vous pouvez fournir tous les chemins d'accès séparés par des virgules.

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-data-lake-storage-gen1-account"></a>Supprimer votre compte Data Lake Storage Gen1
Utilisez la commande suivante pour supprimer votre compte Data Lake Storage Gen1.

    Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

Quand vous y êtes invité, entrez **Y** pour supprimer le compte.

## <a name="next-steps"></a>Étapes suivantes
* [Recommandations en matière d’optimisation des performances pour l’utilisation de PowerShell avec Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Utiliser Azure Data Lake Storage Gen1 pour le Big Data](data-lake-store-data-scenarios.md) 
* [Sécuriser les données dans Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
