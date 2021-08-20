---
title: Configurer l’accès aux sources de données pour Azure Purview MSI à grande échelle (préversion)
description: Dans ce tutoriel, vous allez configurer les paramètres MSI Azure sur vos abonnements à une source de données Azure.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 05/28/2021
ms.openlocfilehash: 952f69d7d33ae695103ee42de4462bdaa30c2538
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113109194"
---
# <a name="tutorial-configure-access-to-data-sources-for-azure-purview-msi-at-scale-preview"></a>Tutoriel : Configurer l’accès aux sources de données pour Azure Purview MSI à grande échelle (préversion)

> [!IMPORTANT]
> Azure Purview est actuellement disponible en préversion. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Pour analyser des sources de données, Azure Purview doit pouvoir y accéder. Ce tutoriel s’adresse aux propriétaires d’abonnements Azure et aux administrateurs de sources de données Azure Purview. Il vous aide à identifier les accès requis et à configurer les règles d’authentification et de réseau requises pour Azure Purview dans les sources de données Azure.

Dans la partie 2 de cette série de tutoriels, vous allez :

> [!div class="checklist"]
>
> * Localiser vos sources de données et préparer une liste d’abonnements de sources de données.
> * Exécuter un script pour configurer le contrôle d’accès en fonction du rôle (RBAC) manquant ou les configurations réseau requises sur vos sources de données dans Azure.
> * Examinez l’affichage de sortie.

## <a name="prerequisites"></a>Prérequis

* Les abonnements Azure où se trouvent vos sources de données. Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
* Un [compte Azure Purview](create-catalog-portal.md).
* Une ressource Azure Key Vault dans chaque abonnement qui comprend des sources de données comme Azure SQL Database, Azure Synapse Analytics ou Azure SQL Managed Instance.
* Le script de [configuration MSI Azure Purview](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-MSI-Configuration).

> [!NOTE]
> Le script de configuration MSI Azure Purview est disponible uniquement pour Windows.
> Ce script est actuellement pris en charge pour Azure Purview Managed Identity (MSI).

> [!IMPORTANT]
> Nous vous recommandons vivement de tester et de vérifier toutes les modifications apportées par le script dans votre environnement Azure avant de le déployer dans votre environnement de production.

## <a name="prepare-azure-subscriptions-list-for-data-sources"></a>Préparer la liste des abonnements Azure pour les sources de données 

Avant d’exécuter le script, créez un fichier .csv (par exemple, "C:\temp\Subscriptions.csv) comprenant quatre colonnes :
   
|Nom de la colonne|Description|Exemple|
|----|----|----|
|`SubscriptionId`|ID d’abonnement Azure pour vos sources de données.|12345678-aaaa-bbbb-cccc-1234567890ab|
|`KeyVaultName`|Nom du coffre de clés existant déployé dans l’abonnement de source de données.|ContosoDevKeyVault|
|`SecretNameSQLUserName`|Nom d’un secret Azure Key Vault existant qui contient un nom d’utilisateur Azure Active Directory (Azure AD) pouvant se connecter à Azure Synapse, Azure SQL Database ou Azure SQL Managed Instance à l’aide de l’authentification Azure AD.|ContosoDevSQLAdmin|
|`SecretNameSQLPassword`|Nom d’un secret Azure Key Vault existant qui contient un mot de passe utilisateur Azure AD pouvant se connecter à Azure Synapse, Azure SQL Database ou Azure SQL Managed Instance à l’aide de l’authentification Azure AD.|ContosoDevSQLPassword|
   


**Exemple de fichier .csv :**
    
:::image type="content" source="./media/tutorial-data-sources-readiness/subscriptions-input.png" alt-text="Capture d’écran montrant un exemple de liste d’abonnements." lightbox="./media/tutorial-data-sources-readiness/subscriptions-input.png":::

> [!NOTE] 
> Vous pouvez mettre à jour le nom et le chemin du fichier dans le code, si nécessaire.


## <a name="run-the-script-and-install-the-required-powershell-modules"></a>Exécuter le script et installer les modules PowerShell nécessaires 

Pour exécuter le script à partir de votre ordinateur Windows, effectuez les étapes suivantes :

1. Téléchargez le script de [configuration Azure Purview MSI](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-MSI-Configuration) à l’emplacement de votre choix.

2. Sur votre ordinateur, entrez **PowerShell** dans la zone de recherche de la barre des tâches Windows. Dans la liste des résultats de recherche, cliquez avec le bouton droit sur **Windows PowerShell**, puis sélectionnez **Exécuter en tant qu’administrateur**.

3. Dans la fenêtre PowerShell, exécutez la commande suivante. (Remplacez `<path-to-script>` par le chemin du dossier du fichier de script).

   ```powershell
   dir -Path <path-to-script> | Unblock-File
   ```

4. Entrez la commande suivante pour installer les applets de commande Azure :

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```
5. Si vous voyez l’invite *, le fournisseur NuGet est requis pour continuer*, entrez **Y** puis sélectionnez **Entrée**.

6. Si vous voyez l’invite *Référentiel non approuvé*, entrez **A** puis sélectionnez **Entrée**.

7. Répétez les étapes précédentes pour installer les modules `Az.Synapse` et `AzureAD`.

L’installation par PowerShell des modules requis peut prendre jusqu’à une minute.


## <a name="collect-other-data-needed-to-run-the-script"></a>Collecter les autres données nécessaires pour exécuter le script

Avant d’exécuter le script PowerShell dans le but d’évaluer la préparation nécessaire des abonnements de sources de données, récupérez les valeurs des arguments suivants pour les utiliser dans les scripts :

- `AzureDataType` : Choisissez l’une des options suivantes comme type de source de données afin d’évaluer la préparation nécessaire pour ce type de données dans l’ensemble de vos abonnements : 
    
    - `BlobStorage`

    - `AzureSQLMI`

    - `AzureSQLDB`
    
    - `ADLSGen2`
    
    - `ADLSGen1`
    
    - `Synapse`
    
    - `All`

- `PurviewAccount` : Nom de ressource de votre compte Azure Purview existant.

- `PurviewSub` : ID de l’abonnement où le compte Azure Purview est déployé.

## <a name="verify-your-permissions"></a>Vérifier vos autorisations

Vérifiez que votre utilisateur dispose bien des autorisations et des rôles suivants :

Au minimum, vous devez disposer des autorisations suivantes pour exécuter le script dans votre environnement Azure :

Role | Étendue | Pourquoi est-il nécessaire ? |
|-------|--------|--------|
| **Lecteur général** | Client Azure AD | Pour lire l'appartenance au groupe d'utilisateurs administrateurs Azure SQL et Azure Purview MSI |
| **Administrateur général** | Client Azure AD | Pour affecter le rôle **lecteur de répertoire** aux instances gérées d’Azure SQL |
| **Contributeur** | Abonnement ou groupe de ressources dans lequel votre compte Azure portée est créé | Pour lire la ressource de compte Azure Purview et créer une ressource Key Vault et une clé secrète |
| **Propriétaire ou Administrateur de l’accès utilisateur** | Groupe d’administration ou abonnement dans lequel se trouvent vos sources de données Azure | Pour attribuer RBAC |
| **Contributeur** | Groupe d’administration ou abonnement dans lequel se trouvent vos sources de données Azure | Pour configurer le réseau |
| **Administrateur SQL** (Azure AD Authentication) | Instances Azure SQL Server ou instances gérées Azure SQL | Pour attribuer le rôle **db_datareader** à Azure Purview |
| Accédez à votre coffre de clés Azure | Accédez-y pour obtenir/lister le secret de Key Vault pour l'authentification à la base de données ou aux instances managées d’Azure SQL ou à Azure Synapse |  


## <a name="run-the-client-side-readiness-script"></a>Exécutez le script de préparation côté client

Exécutez le script en effectuant les étapes suivantes :

1. Utilisez la commande suivante pour accéder au dossier du script. Remplacez `<path-to-script>` par le chemin d’accès au dossier d’extraction du fichier.

   ```powershell
   cd <path-to-script>
   ```

2. Exécutez la commande suivante pour définir la stratégie d’exécution de l’ordinateur local. Entrez **A** pour *Oui pour tout* lorsque vous êtes invité à modifier la stratégie d’exécution.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

3. Exécutez le script avec les paramètres suivants : Remplacez les valeurs des espaces réservés `DataType` , `PurviewName` et `SubscriptionID` .

   ```powershell
   .\purview-msi-configuration.ps1 -AzureDataType <DataType> -PurviewAccount <PurviewName> -PurviewSub <SubscriptionID>
   ```

   Quand vous exécutez la commande, une fenêtre contextuelle peut s’afficher deux fois pour vous demander de vous connecter à Azure et à Azure AD à l’aide de vos informations d’identification Azure Active Directory.

La création du rapport peut prendre plusieurs minutes, en fonction du nombre d’abonnements et de ressources Azure présents dans l’environnement. 

Vous pouvez être invité à vous connecter à vos instances Azure SQL Server si les informations d’identification du coffre de clés ne correspondent pas. Vous pouvez fournir les informations d’identification ou sélectionner **Entrée** pour ignorer le serveur spécifique. 

Une fois le processus terminé, affichez le rapport de sortie pour passer en revue les modifications. 


## <a name="more-information"></a>Autres informations

### <a name="what-data-sources-are-supported-by-the-script"></a>Quelles sources de données sont prises en charge par le script ?

Les sources de données suivantes sont prises en charge par le script :

- Stockage Blob Azure (BlobStorage)
- Azure Data Lake Storage Gen2 (ADLSGen2)
- Azure Data Lake Storage Gen1 (ADLSGen1)
- Azure SQL Database (AzureSQLDB)
- Azure SQL Managed Instance (AzureSQLMI)
- Pool dédié Azure Synapse (Synapse)

Vous pouvez choisir toutes ces sources de données ou certaines d’entre elles comme paramètre d’entrée lorsque vous exécutez le script.

### <a name="what-configurations-are-included-in-the-script"></a>Quelles sont les configurations incluses dans le script ?

Ce script peut vous aider à effectuer automatiquement les tâches suivantes :

#### <a name="azure-blob-storage-blobstorage"></a>Stockage Blob Azure (BlobStorage)

- RBAC. Attribuez le rôle **lecteur** RBAC Azure à Azure Purview MSI sur l’étendue sélectionnée. Vérifiez l’attribution. 
- RBAC. Attribuez le rôle **Lecteur de données de stockage blob** d’Azure RBAC à Azure Purview MSI dans chacun des abonnements situés sous la portée sélectionnée. Vérifiez les affectations.
- Mise en réseau. Vérifiez que le point de terminaison privé a été créé pour le stockage et qu’il a été activé pour le stockage Blob.
- Point de terminaison de service. Si le point de terminaison privé est désactivé, vérifiez si le point de terminaison de service est activé, et activez l'option **Autoriser les services Microsoft de confiance à accéder à ce compte de stockage**.

#### <a name="azure-data-lake-storage-gen2-adlsgen2"></a>Azure Data Lake Storage Gen2 (ADLSGen2)

- RBAC. Attribuez le rôle **lecteur** RBAC Azure à Azure Purview MSI sur l’étendue sélectionnée. Vérifiez l’attribution. 
- RBAC. Attribuez le rôle **Lecteur de données de stockage blob** d’Azure RBAC à Azure Purview MSI dans chacun des abonnements situés sous la portée sélectionnée. Vérifiez les affectations.
- Mise en réseau. Vérifiez que le point de terminaison privé a été créé pour le stockage et qu’il a été activé pour le stockage Blob.
- Point de terminaison de service. Si le point de terminaison privé est désactivé, vérifiez si le point de terminaison de service est activé, et activez l'option **Autoriser les services Microsoft de confiance à accéder à ce compte de stockage**.

#### <a name="azure-data-lake-storage-gen1-adlsgen1"></a>Azure Data Lake Storage Gen1 (ADLSGen1)

- Mise en réseau. Vérifiez que le point de terminaison de service est activé et que l’option **Autoriser tous les services Azure à accéder à ce compte Data Lake Storage Gen1** est activée sur Data Lake Storage.
- Autorisations. Attribuez un accès en lecture/exécution à Azure Purview MSI. Vérifiez l’accès. 

#### <a name="azure-sql-database-azuresqldb"></a>Azure SQL Database (AzureSQLDB)

- Instances SQL Server :
  - Réseau. Vérifiez si le point de terminaison public ou privé est activé.
  - Pare-feu. Si le point de terminaison privé est désactivé, vérifiez les règles de pare-feu et activez **Autoriser les services et ressources Azure à accéder à ce serveur**.
  - Administration Azure AD. Activez l’authentification Azure AD pour Azure SQL Database.

- Bases de données SQL :
  - Rôle SQL. Attribuez le rôle **db_datareader** à Azure Purview MSI.

#### <a name="azure-sql-managed-instance-azuresqlmi"></a>Azure SQL Managed Instance (AzureSQLMI)

- Serveurs SQL Managed Instance :
  - Réseau. Vérifiez que le point de terminaison public ou privé est activé. Vérifiez si le point de terminaison public est désactivé.
  - ProxyOverride. Vérifiez qu’Azure SQL Managed Instance est configuré avec la valeur Proxy ou Redirection.
  - Mise en réseau. Mettez à jour les règles NSG pour autoriser l'accès entrant d'AzureCloud aux instances de SQL Server sur les ports requis :  
    - Redirection : 1433 et 11000-11999
    
    ou 
    - Proxy : 3342
    
    Vérifiez cet accès. 
  - Administration Azure AD. Activez l’authentification Azure AD pour Azure SQL Managed Instance.
  
- Bases de données SQL :
  - Rôle SQL. Attribuez le rôle **db_datareader** à Azure Purview MSI.

#### <a name="azure-synapse-synapse-dedicated-pool"></a>Pool dédié Azure Synapse (Synapse)

- RBAC. Attribuez le rôle **lecteur** RBAC Azure à Azure Purview MSI sur l’étendue sélectionnée. Vérifiez l’attribution. 
- RBAC. Attribuez le rôle **Lecteur de données de stockage blob** d’Azure RBAC à Azure Purview MSI dans chacun des abonnements situés sous la portée sélectionnée. Vérifiez les affectations.
- Instances SQL Server (pools dédiés) :
  - Réseau. Vérifiez si le point de terminaison public ou privé est activé.
  - Pare-feu. Si le point de terminaison privé est désactivé, vérifiez les règles de pare-feu et activez **Autoriser les services et ressources Azure à accéder à ce serveur**.
  - Administration Azure AD. Activez l’authentification Azure AD pour Azure SQL Database.

- Bases de données SQL :
  - Rôle SQL. Attribuez le rôle **db_datareader** à Azure Purview MSI.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :
> [!div class="checklist"]
>
> * Identifiez les accès requis et configurez les règles d’authentification et de réseau requises pour Azure Purview dans les sources de données Azure.

Passez au tutoriel suivant pour découvrir comment [Inscrire et analyser plusieurs sources dans Azure portée](register-scan-azure-multiple-sources.md).
