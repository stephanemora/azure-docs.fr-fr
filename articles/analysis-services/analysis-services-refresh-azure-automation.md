---
title: Actualiser les modèles Azure Analysis Services avec Azure Automation | Microsoft Docs
description: Découvrez comment coder des actualisations de modèle à l’aide d’Azure Automation.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: abbad97e29f60e5f135017cd43f9d30eba1805ca
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66142726"
---
# <a name="refresh-with-azure-automation"></a>Actualiser avec Azure Automation

Grâce à Azure Automation et les PowerShell Runbooks, vous pouvez effectuer les opérations d’actualisation automatique des données sur vos modèles tabulaires Azure Analysis.  

L’exemple de cet article utilise le [les modules PowerShell SqlServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Un exemple de PowerShell Runbook qui illustre l’actualisation d’un modèle est fourni plus loin dans cet article.  

## <a name="authentication"></a>Authentication

Tous les appels doivent être authentifiées avec un jeton Azure Active Directory (OAuth 2) valide.  L’exemple dans cet article utilisera un Principal de Service (SPN) pour s’authentifier auprès d’Azure Analysis Services.

Pour en savoir plus sur la création d’un Principal de Service, consultez]

## <a name="prerequisites"></a>Conditions préalables

> [!IMPORTANT]
> L’exemple suivant suppose que le pare-feu Azure Analysis Services est désactivé. Si le pare-feu est activé, puis l’adresse IP publique de l’initiateur de la demande sera doivent être dans la liste verte dans le pare-feu.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Installer les modules de SQL Server à partir de PowerShell gallery.

1. Dans votre compte Azure Automation, cliquez sur **Modules**, puis **parcourir la galerie**.

2. Dans la barre de recherche, recherchez **SqlServer**.

    ![Rechercher dans les modules](./media/analysis-services-refresh-azure-automation/1.png)

3. Sélectionnez SQL Server, puis cliquez sur **importation**.
 
    ![Importer un module](./media/analysis-services-refresh-azure-automation/2.png)

4. Cliquez sur **OK**.
 
### <a name="create-a-service-principal-spn"></a>Créez un Principal de Service (SPN)

Pour en savoir plus sur la création d’un Principal de Service, consultez [créer un principal de service à l’aide du portail Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Configurer des autorisations dans Azure Analysis Services
 
Le principal du Service que vous créez doit avoir des autorisations d’administrateur de serveur sur le serveur. Pour plus d’informations, consultez [Ajouter un principal de service au rôle d’administrateur du serveur](analysis-services-addservprinc-admins.md)

## <a name="design-the-azure-automation-runbook"></a>Conception du Runbook Azure Automation

1. Dans le compte Automation, créez un **informations d’identification** ressource qui sera utilisé pour stocker en toute sécurité le principal du Service.

    ![créer des informations d’identification](./media/analysis-services-refresh-azure-automation/6.png)

2. Entrez les informations d’identification.  Pour le **nom d’utilisateur**, entrez la **SPN ClientId**, pour le **mot de passe**, entrez la **SPN Secret**.

    ![créer des informations d’identification](./media/analysis-services-refresh-azure-automation/7.png)

3. Importer le Runbook Automation

    ![Importer le Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Recherchez le **actualisation-Model.ps1** de fichiers, fournissez un **nom** et **description**, puis cliquez sur **créer**.

    ![Importer le Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Lorsque le Runbook a été créé, il passe automatiquement en mode édition.  Sélectionnez **Publier**.

    ![Publier un runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > La ressource d’informations d’identification qui a été créée précédemment est récupérée par le runbook à l’aide de la **Get-AutomationPSCredential** commande.  Cette commande est ensuite transmise à la **Invoke-ProcessASADatabase** commande PowerShell pour effectuer l’authentification à Azure Analysis Services.

6. Tester le runbook en cliquant sur **Démarrer**.

    ![Démarrer le Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Remplissez le **DATABASENAME**, **ANALYSISSERVER**, et **REFRESHTYPE** paramètres, puis cliquez sur **OK**. Le **WEBHOOKDATA** paramètre n’est pas obligatoire lorsque le Runbook est exécuté manuellement.

    ![Démarrer le Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Si le Runbook a été exécutée avec succès, vous recevrez une sortie semblable à ce qui suit :

![Exécution réussie](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Utiliser un Runbook Azure Automation autonome

Le Runbook peut être configuré pour déclencher le modèle Azure Analysis Services selon une planification d’actualisation.

Cela peut être configuré comme suit :

1. Dans le Runbook Automation, cliquez sur **planifications**, puis **ajouter une planification**.
 
    ![Créer une planification](./media/analysis-services-refresh-azure-automation/14.png)

2. Cliquez sur **planification** > **créer une planification**, puis renseignez les détails.

    ![Configurer la planification](./media/analysis-services-refresh-azure-automation/15.png)

3. Cliquez sur **Créer**.

4. Renseignez les paramètres pour la planification. Elles seront utilisées chaque fois déclenche le Runbook. Le **WEBHOOKDATA** paramètre doit être laissé vide lors de l’exécution via une planification.

    ![Configurer les paramètres](./media/analysis-services-refresh-azure-automation/16.png)

5. Cliquez sur **OK**.

## <a name="consume-with-data-factory"></a>Utiliser avec Data Factory

Pour utiliser le runbook à l’aide d’Azure Data Factory, créez d’abord un **Webhook** pour le runbook. Le **Webhook** fournira une URL qui peut être appelée via une activité web de Azure Data Factory.

> [!IMPORTANT]
> Pour créer un **Webhook**, l’état du Runbook doit être **publié**.

1. Dans votre Runbook Automation, cliquez sur **Webhooks**, puis cliquez sur **ajouter un Webhook**.

   ![Add Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Donnez à la Webhook un nom et expiration.  Le nom identifie uniquement le Webhook à l’intérieur du Runbook Automation, il ne font pas partie de l’URL.

   >[!CAUTION]
   >Vérifiez que vous copiez l’URL avant de fermer l’Assistant comme vous ne pouvez pas le récupérer une fois fermé.
    
   ![Configurer un Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Les paramètres pour le webhook peuvent rester vides.  Lors de la configuration de l’activité web de Azure Data Factory, ceux-ci peuvent être passés dans le corps de l’appel web.

3. Dans Data Factory, vous devez configurer un **de l’activité web**

### <a name="example"></a>Exemples

   ![Exemple d’activité Web](./media/analysis-services-refresh-azure-automation/19.png)

Le **URL** est l’URL créée à partir du Webhook.

Le **corps** est un document JSON qui doit contenir les propriétés suivantes :


|Propriété  |Valeur  |
|---------|---------|
|**AnalysisServicesDatabase**     |Le nom de la base de données Azure Analysis Services <br/> Exemple : AdventureWorksDB         |
|**AnalysisServicesServer**     |Le nom du serveur Azure Analysis Services. <br/> Exemple : https :\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Le type d’actualisation à effectuer. <br/> Exemple : Complet         |

Exemple de corps JSON :

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Ces paramètres sont définis dans le script PowerShell de runbook.  Lorsque l’activité web est exécutée, la charge utile JSON passée est WEBHOOKDATA.

Il est désérialisé et stockée en tant que paramètres de PowerShell, qui sont ensuite utilisés par la commande Invoke-ProcesASDatabase PowerShell.

![Webhook désérialisée](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Utiliser un Worker hybride avec Azure Analysis Services

Une Machine virtuelle de Azure avec une adresse IP publique statique peut être utilisé comme un Worker hybride Azure Automation.  Cette adresse IP publique peut ensuite être ajoutée au pare-feu Azure Analysis Services.

> [!IMPORTANT]
> Vérifiez que l’adresse IP publique de Machine virtuelle est configurée comme statique.
>
>Pour en savoir plus sur la configuration de Worker hybride Azure Automation, consultez [automatiser des ressources dans votre centre de données ou le cloud à l’aide de Runbook Worker hybride](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Une fois un Worker hybride est configuré, créer un Webhook, comme décrit dans la section [consommer avec Data Factory](#consume-with-data-factory).  La seule différence ici consiste à sélectionner le **s’exécutent sur** > **Worker hybride** option lors de la configuration du Webhook.

Exemple de webhook à l’aide de Worker hybride :

![Exemple de Webhook de Worker hybride](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Exemple de PowerShell Runbook

L’extrait de code suivant est un exemple montrant comment procéder à l’actualisation de modèle Azure Analysis Services à l’aide d’un PowerShell Runbook.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Étapes suivantes

[Exemples](analysis-services-samples.md)  
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
