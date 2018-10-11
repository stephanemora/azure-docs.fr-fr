---
title: Utiliser des identités managées pour authentifier les travaux Azure Stream Analytics avec la sortie Azure Data Lake Storage Gen1 (préversion)
description: ''
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/27/2018
ms.openlocfilehash: 72bf467cc0f2ba195aa4f25228bc9e08605cd4ee
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018588"
---
# <a name="use-managed-identities-to-authenticate-azure-stream-analytics-jobs-to-azure-data-lake-storage-gen1-output-preview"></a>Utiliser des identités managées pour authentifier les travaux Azure Stream Analytics avec la sortie Azure Data Lake Storage Gen1 (préversion)

Azure Stream Analytics prend en charge l’authentification des identités managées avec la sortie Azure Data Lake Storage (ADLS) Gen1. L’identité est une application managée inscrite auprès d’Azure Active Directory. Elle représente un travail Stream Analytics spécifique et peut servir à l’authentification auprès d’une ressource cible. Les identités managées n’ont pas les limitations des méthodes d’authentification basée sur l’utilisateur, comme la réauthentification obligatoire après un changement de mot de passe ou l’expiration du jeton d’utilisateur tous les 90 jours. De plus, les identités managées facilitent l’automatisation des déploiements de travaux Stream Analytics dont la sortie est générée dans Azure Data Lake Storage Gen1.

Consultez le billet de blog [Eight new features in Azure Stream Analytics](https://azure.microsoft.com/en-us/blog/eight-new-features-in-azure-stream-analytics/) pour vous inscrire à cette préversion et en savoir plus sur les nouvelles fonctionnalités.

Cet article vous montre deux façons d’activer une identité managée pour un travail Azure Stream Analytics dont la sortie est générée dans Azure Data Lake Storage Gen1 : à l’aide du portail Azure et par le biais d’un déploiement de modèle Azure Resource Manager.

## <a name="enable-managed-identity-with-azure-portal"></a>Activer une identité managée à l’aide du portail Azure

1. Commencez par créer un travail Stream Analytics ou par ouvrir un travail existant dans le portail Azure. Dans la barre de menus située sur le côté gauche de l’écran, sélectionnez **Identité managée (préversion)** sous **Configurer**.

   ![Configurer une identité managée Stream Analytics (préversion)](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Sélectionnez **Utiliser une identité managée assignée par le système (préversion)** dans la fenêtre à droite. Cliquez sur **Enregistrer** afin de créer un principal de service pour l’identité associée au travail Stream Analytics dans Azure Active Directory. Le cycle de vie de la nouvelle identité est géré par Azure. Quand le travail Stream Analytics est supprimé, l’identité associée (autrement dit, le principal de service) est également automatiquement supprimée par Azure.

   Une fois que la configuration est enregistrée, l’ID objet (l’OID) du principal de service s’affiche en tant qu’ID de principal, comme ci-dessous :

   ![ID de principal Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Le principal de service a le même nom que le travail Stream Analytics. Par exemple, si le nom de votre travail est **MyASAJob**, le nom du principal de service créé est également **MyASAJob**.

3. Dans la fenêtre de propriétés de la sortie du récepteur de sortie ADLS Gen1, cliquez sur la liste déroulante Mode d’authentification et sélectionnez **Identité managée (préversion)**.

4. Renseignez le reste des propriétés. Pour en savoir plus sur la création d’une sortie ADLS, consultez [Créer une sortie Data Lake Storage avec Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md). Quand vous avez terminé, cliquez sur **Enregistrer**.

   ![Configurer Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Accédez à la page Vue d’ensemble de votre sortie ADLS Gen1 et cliquez sur **Explorateur de données**.

   ![Configurer Data Lake Storage - Vue d’ensemble](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. Dans le volet Explorateur de données, sélectionnez **Accès** et cliquez sur **Ajouter** dans le volet Accès.

   ![Configurer Data Lake Storage - Accès](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Dans la zone de texte du volet **Sélectionner un utilisateur ou un groupe**, tapez le nom du principal de service. N’oubliez pas que le nom du principal de service est également le nom du travail Stream Analytics correspondant. Quand vous commencez à taper le nom du principal, celui-ci s’affiche sous la zone de texte. Choisissez le nom du principal de service souhaité en cliquant sur **Sélectionner**.

   ![Sélectionner un nom de principal de service](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. Dans le volet **Autorisations**, sélectionnez les autorisations **Écriture** et **Exécution**, et assignez-les à **Ce dossier et tous les enfants**. Cliquez ensuite sur **OK**.

   ![Sélectionner une autorisation](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Le principal de service est répertorié sous **Autorisations assignées** dans le volet **Accès** comme illustré ci-dessous. Vous pouvez maintenant revenir en arrière et lancer votre travail Stream Analytics.

   ![Liste d’accès](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Pour en savoir plus sur les autorisations de système de fichiers pour Data Lake Storage Gen1, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="resource-manager-template-deployment"></a>Déploiement de modèle Resource Manager

1. Vous pouvez créer une ressource *Microsoft.StreamAnalytics/streamingjobs* avec une identité managée en incluant la propriété suivante dans la section ressources de votre modèle Resource Manager :

   ```json
   "Identity": {
   "Type": "SystemAssigned",
   },
   ```

   Cette propriété indique à Azure Resource Manager de créer et manager l’identité de votre travail Azure Stream Analytics.

   **Exemple de travail**

   ```json
   { 
   "Name": "AsaJobWithIdentity", 
   "Type": "Microsoft.StreamAnalytics/streamingjobs", 
   "Location": "West US",
   "Identity": {
     "Type": "SystemAssigned", 
     }, 
   "properties": {
      "sku": {
       "name": "standard"
       },
   "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {        
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": “myDataLakeAccountName",
                 "filePathPrefix": “cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
                 }
                 
   }
   ```
  
   **Exemple de réponse du travail**

   ```json
   { 
   "Name": "mySAJob", 
   "Type": "Microsoft.StreamAnalytics/streamingjobs", 
   "Location": "West US",
   "Identity": {
   "Type": "SystemAssigned",
    "principalId": "GUID", 
    "tenantId": "GUID", 
   }, 
   "properties": {
           "sku": {
             "name": "standard"
           },
   }
   ```

   Prenez note de l’ID du principal indiqué dans la réponse du travail pour accorder l’accès à la ressource ADLS requise.

   La valeur **ID du locataire** est l’ID du locataire Azure Active Directory où le principal de service est créé. Le principal de service est créé dans le locataire Azure approuvé par l’abonnement.

   Le **Type** indique le type de l’identité managée, comme expliqué dans les types d’identités managées. Seul le type Affecté par le système est pris en charge.

2. Fournissez l’accès au principal de service à l’aide de PowerShell. Pour donner accès au principal de service avec PowerShell, exécutez la commande suivante :

   ```powershell
   Set-AzureRmDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   La valeur **PrincipalId** correspond à l’ID objet du principal de service, qui s’affiche sur le portail une fois que le principal de service est créé. Si vous avez créé le travail à l’aide d’un déploiement de modèle Resource Manager, l’ID objet est indiqué dans la propriété Identity contenue dans la réponse du travail.

   **Exemple**

   ```powershell
   PS > Set-AzureRmDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Pour en savoir plus sur la commande PowerShell ci-dessus, consultez la documentation [Set-AzureRmDataLakeStoreItemAclEntry](https://docs.microsoft.com/powershell/module/azurerm.datalakestore/set-azurermdatalakestoreitemaclentry?view=azurermps-6.8.1&viewFallbackFrom=azurermps-4.2.0#optional-parameters).

## <a name="next-steps"></a>Étapes suivantes

* [Créer une sortie Data Lake Store avec Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)
