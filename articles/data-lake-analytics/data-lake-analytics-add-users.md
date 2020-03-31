---
title: Ajouter des utilisateurs à un compte Azure Data Lake Analytics
description: Découvrez comment ajouter correctement des utilisateurs à votre compte Data Lake Analytics à l’aide de l’Assistant Ajout d’un utilisateur et d’Azure PowerShell.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 16c503fe2d584d5f8256c65bfc49825b300f6a36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672730"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Ajout d’un utilisateur dans le portail Azure

## <a name="start-the-add-user-wizard"></a>Démarrer l’Assistant Ajouter un d’utilisateur
1. Ouvrez votre Azure Data Lake Analytics via https://portal.azure.com.
2. Cliquez sur **Assistant Ajouter un utilisateur**.
3. Dans l’étape **Sélectionner un utilisateur**, recherchez l’utilisateur que vous voulez ajouter. Cliquez sur **Sélectionner**.
4. Dans l’étape **Sélectionner un rôle**, choisissez **Développeur Data Lake Analytics**. Ce rôle dispose de l’ensemble minimal d’autorisations requises pour envoyer/contrôler/gérer les travaux U-SQL. Assignez ce rôle si le groupe n’est pas conçu pour gérer les services Azure.
5. Dans l’étape **Sélectionner des autorisations de catalogue**, sélectionnez les bases de données supplémentaires auxquelles l’utilisateur doit avoir accès. Un accès Lecture et écriture à la base de données MASTER est requis pour envoyer des travaux. Lorsque vous avez terminé, cliquez sur **OK**.
6. Dans la dernière étape appelée **Affecter les autorisations sélectionnées**, passez en revue les modifications que l’Assistant apportera. Cliquez sur **OK**.


## <a name="configure-acls-for-data-folders"></a>Configurer des ACL de dossiers de données
Accordez « R-X » ou « RWX », si nécessaire, sur les dossiers contenant les données d’entrée et les données de sortie.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Ajoutez, si vous le souhaitez, l’utilisateur au rôle **Lecteur** d’Azure Data Lake Storage Gen1.
1.  Cherchez votre compte Azure Data Lake Storage Gen1.
2.  Cliquez sur **Utilisateurs**.
3. Cliquez sur **Ajouter**.
4.  Sélectionnez un rôle RBAC Azure à assigner à ce groupe.
5.  Assignez le rôle Lecteur. Ce rôle dispose de l’ensemble minimal d’autorisations requises pour parcourir/gérer les données stockées dans des ADLSGen1. Assignez ce rôle si le groupe n’est pas conçu pour gérer les services Azure.
6.  Saisissez le nom du groupe.
7.  Cliquez sur **OK**.

## <a name="adding-a-user-using-powershell"></a>Ajout d’un utilisateur à l’aide de PowerShell

1. Suivez les instructions de ce guide : [Installation et configuration d’Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Téléchargez le script PowerShell [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1).
3. Exécutez le script PowerShell. 

L’exemple de commande pour autoriser l’utilisateur à envoyer des travaux, afficher les nouvelles métadonnées du travail et afficher les anciennes métadonnées est le suivant :

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Gestion d’Azure Data Lake Analytics à l’aide d’Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

