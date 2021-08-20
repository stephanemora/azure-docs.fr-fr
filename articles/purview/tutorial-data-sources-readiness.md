---
title: Évaluer la préparation nécessaire pour les sources de données à grande échelle (préversion)
description: Dans ce tutoriel, vous allez évaluer la préparation nécessaire pour vos sources de données Azure avant de les inscrire et de les analyser dans Azure Purview.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 05/28/2021
ms.openlocfilehash: d7dc8ab7987f149747df30834f426ce6d119eb5c
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105918"
---
# <a name="tutorial-check-data-source-readiness-at-scale-preview"></a>Tutoriel : Évaluer la préparation nécessaire pour les sources de données à grande échelle (préversion)

> [!IMPORTANT]
> Azure Purview est actuellement en préversion. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Pour analyser des sources de données, Azure Purview doit pouvoir y accéder. Il utilise des informations d’identification pour obtenir cet accès. Les *informations d’identification* permettent à Azure Purview de s’authentifier auprès des sources de données que vous avez inscrites. Il existe plusieurs façons de configurer les informations d’identification pour Azure Purview, notamment : 
- Une identité managée affectée au compte Azure Purview.
- Des secrets stockés dans Azure Key Vault. 
- Des principaux de service.

Dans cette série de tutoriels en deux parties, nous vous aiderons à vérifier et à configurer les attributions de rôles Azure ainsi que l’accès réseau pour les sources de données Azure de vos abonnements Azure à grande échelle. Vous pourrez ensuite inscrire et analyser vos sources de données Azure dans Azure Purview. 

Exécutez le script [Azure Purview data sources readiness checklist](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness) après avoir déployé votre compte Azure Purview et avant d’inscrire et d’analyser vos sources de données Azure. 

Dans la partie 1 de cette série de tutoriels, vous allez :

> [!div class="checklist"]
>
> * Localiser vos sources de données et préparer une liste d’abonnements de sources de données.
> * Exécuter le script de check-list de préparation pour rechercher d’éventuelles configurations RBAC ou réseau manquantes dans vos sources de données Azure.
> * Dans le rapport de sortie, vérifier les configurations réseau manquantes et les attributions de rôles exigées par Azure Purview Managed Identity (MSI). 
> * Partager le rapport avec les propriétaires des abonnements Azure pour qu’ils puissent effectuer les actions suggérées.

## <a name="prerequisites"></a>Prérequis

* Les abonnements Azure où se trouvent vos sources de données. Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
* Un [compte Azure Purview](create-catalog-portal.md).
* Une ressource Azure Key Vault dans chaque abonnement qui comprend des sources de données comme Azure SQL Database, Azure Synapse Analytics ou Azure SQL Managed Instance.
* Le script [Azure Purview data sources readiness checklist](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness).

> [!NOTE]
> La check-list de préparation des sources de données Azure Purview est disponible uniquement pour Windows.
> Ce script de check-list de préparation est pris en charge par Azure Purview Managed Identity (MSI).

## <a name="prepare-azure-subscriptions-list-for-data-sources"></a>Préparer la liste des abonnements Azure pour les sources de données

Avant d’exécuter le script, créez un fichier .csv (par exemple, C:\temp\Subscriptions.csv) comprenant quatre colonnes :

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

1. [Téléchargez le script Azure Purview data sources readiness checklist](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness) à l’emplacement de votre choix.

2. Sur votre ordinateur, entrez **PowerShell** dans la zone de recherche de la barre des tâches Windows. Dans la liste des résultats de recherche, cliquez avec le bouton droit sur **Windows PowerShell**, puis sélectionnez **Exécuter en tant qu’administrateur**.

3. Dans la fenêtre PowerShell, exécutez la commande suivante. (Remplacez `<path-to-script>` par le chemin du dossier du fichier de script).

   ```powershell
   dir -Path <path-to-script> | Unblock-File
   ```

4. Entrez la commande suivante pour installer les applets de commande Azure :

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```
6. Si vous voyez l’invite *Le fournisseur NuGet est requis pour continuer*, entrez **Y** puis sélectionnez **Entrée**.

7. Si vous voyez l’invite *Référentiel non approuvé*, entrez **A** puis sélectionnez **Entrée**.

5. Répétez les étapes précédentes pour installer les modules `Az.Synapse` et `AzureAD`.

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

Rôle ou autorisation | Étendue |
|-------|--------|
| **Lecteur général** | Client Azure AD |
| **Lecteur** | Abonnements Azure où se trouvent vos sources de données Azure |
| **Lecteur** | Abonnement dans lequel votre compte Azure Purview a été créé |
| **Administrateur SQL** (Azure AD Authentication) | Pools dédiés Azure Synapse, instances Azure SQL Database, instances managées Azure SQL |
| Accès à votre coffre de clés Azure | Accès pour récupérer ou lister les secrets du coffre de clés ou l’utilisateur des secrets Azure Key Vault |  


## <a name="run-the-client-side-readiness-script"></a>Exécuter le script de préparation côté client

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
   .\purview-data-sources-readiness-checklist.ps1 -AzureDataType <DataType> -PurviewAccount <PurviewName> -PurviewSub <SubscriptionID>
   ```

   Quand vous exécutez la commande, une fenêtre contextuelle peut s’afficher deux fois pour vous demander de vous connecter à Azure et à Azure AD à l’aide de vos informations d’identification Azure Active Directory.


La création du rapport peut prendre plusieurs minutes, en fonction du nombre d’abonnements et de ressources Azure présents dans l’environnement. 

Une fois le processus terminé, examinez le rapport de sortie qui signale les configurations manquantes dans vos abonnements ou ressources Azure. Les résultats peuvent être associés à l’état _Passed_ (Réussite), _Not Passed_ (Échec) ou _Awareness_ (Avertissement). Vous pouvez partager les résultats avec les administrateurs d’abonnements correspondants de votre organisation pour qu’ils puissent configurer les paramètres nécessaires.


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

### <a name="what-checks-are-included-in-the-results"></a>Quelles vérifications sont incluses dans les résultats ?

#### <a name="azure-blob-storage-blobstorage"></a>Stockage Blob Azure (BlobStorage)

- RBAC. Vérifiez qu’Azure Purview MSI dispose bien du rôle **Lecteur des données blob du stockage** dans chacun des abonnements de l’étendue sélectionnée.
- RBAC. Vérifiez qu’Azure Purview MSI dispose bien du rôle **Lecteur** dans l’étendue sélectionnée.
- Point de terminaison de service. Vérifiez que le point de terminaison de service est activé et vérifiez que l’option **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** est activée.
- Réseau : Vérifiez que le point de terminaison privé a été créé pour le stockage et qu’il a été activé pour le stockage Blob.

#### <a name="azure-data-lake-storage-gen2-adlsgen2"></a>Azure Data Lake Storage Gen2 (ADLSGen2)

- RBAC. Vérifiez qu’Azure Purview MSI dispose bien du rôle **Lecteur des données blob du stockage** dans chacun des abonnements de l’étendue sélectionnée.
- RBAC. Vérifiez qu’Azure Purview MSI dispose bien du rôle **Lecteur** dans l’étendue sélectionnée.
- Point de terminaison de service. Vérifiez que le point de terminaison de service est activé et vérifiez que l’option **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** est activée.
- Réseau : Vérifiez que le point de terminaison privé a été créé pour le stockage et qu’il a été activé pour le stockage Blob.

#### <a name="azure-data-lake-storage-gen1-adlsgen1"></a>Azure Data Lake Storage Gen1 (ADLSGen1)

- Mise en réseau. Vérifiez que le point de terminaison de service est activé et que l’option **Autoriser tous les services Azure à accéder à ce compte Data Lake Storage Gen1** est activée.
- Autorisations. Vérifiez qu’Azure Purview MSI dispose des autorisations de lecture et d’exécution.

#### <a name="azure-sql-database-azuresqldb"></a>Azure SQL Database (AzureSQLDB)

- Instances SQL Server :
  - Réseau. Vérifiez si le point de terminaison public ou privé est activé.
  - Pare-feu. Vérifiez que l’option **Autoriser les services et les ressources Azure à accéder à ce serveur** est activée.
  - Administration Azure AD. Vérifiez qu’Azure SQL Server a une authentification Azure AD.
  - Administration Azure AD. Renseignez le groupe ou l’utilisateur administrateur Azure AD Azure SQL Server.

- Bases de données SQL :
  - Rôle SQL. Vérifiez qu’Azure Purview MSI dispose bien du rôle **db_datareader**.

#### <a name="azure-sql-managed-instance-azuresqlmi"></a>Azure SQL Managed Instance (AzureSQLMI)

- Serveurs SQL Managed Instance :
  - Réseau. Vérifiez si le point de terminaison public ou privé est activé.
  - ProxyOverride. Vérifiez qu’Azure SQL Managed Instance est configuré avec la valeur Proxy ou Redirection.
  - Mise en réseau. Vérifiez que le groupe de sécurité réseau comprend une règle de trafic entrant qui autorise AzureCloud sur les ports nécessaires :  
    - Redirection : 1433 et 11000-11999  
    ou
    - Proxy : 3342
  - Administration Azure AD. Vérifiez qu’Azure SQL Server a une authentification Azure AD.
  - Administration Azure AD. Renseignez le groupe ou l’utilisateur administrateur Azure AD Azure SQL Server.

- Bases de données SQL :
  - Rôle SQL. Vérifiez qu’Azure Purview MSI dispose bien du rôle **db_datareader**.

#### <a name="azure-synapse-synapse-dedicated-pool"></a>Pool dédié Azure Synapse (Synapse)

- RBAC. Vérifiez qu’Azure Purview MSI dispose bien du rôle **Lecteur des données blob du stockage** dans chacun des abonnements de l’étendue sélectionnée.
- RBAC. Vérifiez qu’Azure Purview MSI dispose bien du rôle **Lecteur** dans l’étendue sélectionnée.
- Instances SQL Server (pools dédiés) :
  - Réseau : Vérifiez si le point de terminaison public ou privé est activé.
  - Pare-feu : Vérifiez que l’option **Autoriser les services et les ressources Azure à accéder à ce serveur** est activée.
  - Administration Azure AD : Vérifiez si Azure SQL Server a l’authentification Azure AD.
  - Administration Azure AD : Renseignez le groupe ou l’utilisateur administrateur Azure AD Azure SQL Server.

- Bases de données SQL :
  - Rôle SQL. Vérifiez qu’Azure Purview MSI dispose bien du rôle **db_datareader**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :
> [!div class="checklist"]
>
> * Exécutez le script de check-list de préparation Azure Purview pour vérifier à grande échelle si des abonnements Azure ne sont pas configurés, avant de les inscrire et de les analyser dans Azure Purview.

Passez au tutoriel suivant pour découvrir comment identifier l’accès nécessaire et configurer les règles d’authentification et de réseau exigées par Azure Purview dans vos sources de données Azure :

> [!div class="nextstepaction"]
> [Configurer l’accès aux sources de données pour Azure Purview MSI à grande échelle](tutorial-msi-configuration.md)
