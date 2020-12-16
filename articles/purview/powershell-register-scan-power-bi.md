---
title: Utiliser PowerShell pour inscrire et analyser Power BI (préversion)
description: Découvrez comment utiliser PowerShell pour inscrire et analyser un locataire Power BI dans Azure Purview.
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550084"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>Utiliser PowerShell pour inscrire et analyser Power BI dans Azure Purview (préversion) 

Cet article explique comment utiliser PowerShell pour configurer l’analyse d’un locataire Power BI dans un catalogue Azure Purview.

## <a name="power-bi-authentication-background"></a>Arrière-plan d’authentification Power BI

Le catalogue Purview doit se connecter à l’API d’administration Power BI pour analyser les artefacts dans un locataire Power BI. Actuellement, l’API d’administration Power BI prend en charge deux types d’authentification :

- Identité managée (MSI)
- Authentification utilisateur déléguée

> [!Note]
> MSI est recommandé, sauf si l’authentification utilisateur déléguée est requise.

## <a name="create-a-security-group"></a>Créer un groupe de sécurité

Chaque catalogue Purview possède sa propre identité managée attribuée par le système qui doit se voir accorder un accès au locataire Power BI pour activer l’analyse. Le nom du catalogue permet de trouver son identité sur le portail Azure.

1. Dans le [portail Azure](https://portal.azure.com), recherchez Azure Active Directory.
1. Créez un groupe de sécurité dans votre annuaire Azure Active Directory, en suivant les instructions fournies dans [Créer un groupe de base et ajouter des membres avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > Vous pouvez ignorer cette étape si vous disposez déjà d’un groupe de sécurité à utiliser.

1. Veillez à sélectionner Sécurité en tant que **Type de groupe**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Type de groupe de sécurité":::

1. Ajoutez l’identité managée de votre catalogue à ce groupe de sécurité en sélectionnant Membres, puis **+ Ajouter des membres**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Ajouter l’instance managée du catalogue au groupe":::

1. Recherchez votre catalogue et sélectionnez-le.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Ajouter un catalogue en le recherchant":::

1. Vous devez voir une notification de réussite indiquant qu’il a été ajouté.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Réussite de l’ajout de l’identité MSI du catalogue":::

## <a name="associate-the-security-group-with-power-bi"></a>Associer le groupe de sécurité à Power BI

1. Connectez-vous au [portail d’administration Power BI](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1). Ajouter cet indicateur de fonctionnalité :  `allowServicePrincipalsUseReadAdminAPIsUI=1`. Cet indicateur active la fonctionnalité qui vous permet d’associer votre groupe de sécurité. Par exemple :

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > Vous devez être Administrateur Power BI pour voir la page des paramètres du locataire.

1. Sélectionnez **Paramètres de développeur** > **Autoriser les principaux de service à utiliser les API Power BI en lecture seule (préversion)** .
1. Sélectionnez **Groupes de sécurité spécifiques**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Image illustrant comment permettre aux principaux de service d’obtenir des autorisations d’API d’administration Power BI en lecture seule":::

    > [!Caution]
    > Lorsque vous autorisez le groupe de sécurité que vous avez créé (dont l’identité managée de votre catalogue de données est membre) à utiliser des API d’administration Power BI en lecture seule, vous l’autorisez également à accéder aux métadonnées (telles que le tableau de bord et les noms de rapports, les propriétaires, les descriptions) pour tous vos artefacts Power BI dans ce locataire. Une fois que les métadonnées ont été extraites dans Azure Purview, les autorisations de Purview, et non celles de Power BI, déterminent qui peut voir ces métadonnées.

    > [!Note]
    > Vous pouvez supprimer le groupe de sécurité de vos paramètres de développeur, mais les métadonnées précédemment extraites ne seront pas supprimées du compte Purview. Vous pouvez les supprimer séparément.

## <a name="register-power-bi-and-set-up-a-scan"></a>Inscrire Power BI et configurer une analyse

Maintenant que vous avez accordé au catalogue les autorisations nécessaires pour se connecter à l’API d’administration de votre locataire Power BI, vous devez configurer votre analyse dans le catalogue. Pour ce faire, vous configurez et exécutez un script PowerShell.

1. Téléchargez et extrayez les cmdlets PowerShell ADC.
1. Configurez votre script en fournissant des valeurs pour les attributions en haut du script.

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > Vous devez être contributeur ou propriétaire de l’abonnement sous lequel vous exécutez les commandes.

1. Pour démarrer votre analyse, exécutez le script suivant :

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>Inscrire et analyser Power BI

La méthode d’authentification recommandée est MSI. Toutefois, pour analyser un locataire Power BI situé dans un autre locataire Azure que votre catalogue, vous utilisez l’authentification déléguée.

Dans le cadre de l’authentification déléguée, vous devez disposer des informations d’identification d’utilisateur administrateur, ainsi que des informations d’identification d’administrateur Power BI. Vous devez également créer une application Azure et lui octroyer les autorisations Power BI Tenant.ReadAll.

1. Accédez au [portail Azure](https://portal.azure.com) et recherchez **Inscriptions d'applications**.

1. Depuis **Inscriptions d’applications**, sélectionnez **+ Nouvelle inscription**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="Image illustrant comment créer une inscription d’application Azure":::

1. Entrez un nom pour votre application.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="Inscrire une nouvelle application":::

1. Une fois l’application créée, sélectionnez **Autorisations d’API**, puis **+ Ajouter une autorisation**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="Image illustrant comment ajouter une autorisation à l’application":::

1. Sélectionnez **Service Power BI** dans **Demander des autorisations d’API**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="Image illustrant comment sélectionner le service PBI":::

1. Sélectionnez **Autorisations déléguées** et **Tenant.Read.All**. Sélectionnez ensuite **Ajouter des autorisations**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="Image illustrant comment demander des autorisations d’API":::

1. Sélectionner **Accorder le consentement administrateur**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="Image illustrant comment accorder un consentement administrateur":::

1. Copiez les valeurs **ID d’application (client)** et **ID de l’annuaire (locataire)** .  Vous utiliserez ces valeurs lors de la configuration de votre analyse.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="Image illustrant la copie des ID de client et de locataire":::

1. Configurez votre analyse dans PowerShell. Le script vous invite à indiquer des informations d’identification. Vous devez avoir au moins le rôle Contributeur et le rôle Administrateur de source de données Purview sur l’abonnement Azure que vous envisagez d’utiliser.

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. Exécutez votre analyse.

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>Étapes suivantes

Pour prendre en main Azure Purview, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).
