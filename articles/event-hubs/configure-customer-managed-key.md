---
title: Configurer votre propre clé pour chiffrer les données Azure Event Hubs au repos
description: Cet article vous explique comment configurer votre propre clé pour chiffrer les données Azure Event Hubs au repos.
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: ed230cc1e0a377a580e0f23feeac74f6e0b5489d
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122566166"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest"></a>Configurer les clés managées par le client pour le chiffrement des données au repos Azure Event Hubs
Azure Event Hubs fournit une fonctionnalité de chiffrement des données au repos avec Azure Storage Service Encryption (Azure SSE). Le service Azure Event Hubs utilise Stockage Azure pour stocker les données. Toutes les données stockées avec Stockage Azure sont chiffrées à l'aide de clés gérées par Microsoft. Si vous utilisez votre propre clé (méthode également appelée Bring Your Own Key (BYOK) ou clé gérée par le client), les données sont toujours chiffrées à l'aide de la clé gérée par Microsoft, mais, en outre, la clé gérée par Microsoft est chiffrée à l'aide de la clé gérée par le client. Cette fonctionnalité vous permet de créer, de faire tourner, de désactiver et de révoquer l'accès aux clés gérées par le client et utilisées pour chiffrer les clés gérées par Microsoft. L'activation de la fonctionnalité BYOK sur votre espace de noms ne s'effectue qu'une seule fois.

> [!IMPORTANT]
> - La fonctionnalité BYOK est prise en charge par les niveaux **Premium** et **dédié** d’Event Hubs.
> - Le chiffrement ne peut être activé que pour les espaces de noms nouveaux ou vides. Si l'espace de noms contient des concentrateurs d'événements, l'opération de chiffrement échoue.

Vous pouvez utiliser Azure Key Vault pour gérer vos clés et effectuer un audit sur leur utilisation. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](../key-vault/general/overview.md)

Cet article explique comment configurer un coffre de clés à l'aide de clés gérées par le client via le portail Azure. Pour savoir comment créer un coffre de clés à l’aide du portail Azure, consultez [Démarrage rapide : Créer un coffre de clés Azure Key Vault à l'aide du portail Azure](../key-vault/general/quick-create-portal.md).

## <a name="enable-customer-managed-keys-azure-portal"></a>Activer des clés gérées par le client (portail Azure)
Pour activer des clés gérées par le client dans le portail Azure, procédez comme suit. Si vous utilisez le niveau dédié, commencez par accéder à votre cluster Event Hubs Dedicated.

1. Sélectionnez l'espace de noms sur lequel vous souhaitez activer la fonctionnalité BYOK.
1. Sur la page **Paramètres** de votre espace de noms Event Hubs, sélectionnez **Chiffrement**. 
1. Sélectionnez **Clé gérée par le client**, comme indiqué sur l'illustration suivante. 

    ![Activer une clé gérée par le client](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Configurer un coffre de clés avec des clés
Après avoir activé une clé gérée par le client, vous devez l'associer à votre espace de noms Azure Event Hubs. Event Hubs prend uniquement en charge Azure Key Vault. Si vous activez l'option **Chiffrement à l'aide de la clé gérée par le client** dans la section précédente, vous devez importer la clé dans Azure Key Vault. En outre, les fonctionnalités **Suppression réversible** et **Ne pas vider** doivent être configurées pour la clé. Ces paramètres peuvent être configurés à l'aide de [PowerShell](../key-vault/general/key-vault-recovery.md) ou de l'[interface CLI](../key-vault/general/key-vault-recovery.md).

1. Pour créer un coffre de clés, suivez le [guide de démarrage rapide](../key-vault/general/overview.md) d'Azure Key Vault. Pour plus d'informations sur l'importation de clés existantes, consultez [Présentation des clés, des secrets et des certificats](../key-vault/general/about-keys-secrets-certificates.md).

    > [!IMPORTANT]
    > Pour utiliser des clés gérées par le client avec Azure Event Hubs, le coffre de clés doit contenir deux propriétés requises configurées. Il s'agit de :  **Suppression réversible** et **Ne pas vider**. Ces propriétés sont activées par défaut lorsque vous créez un coffre de clés dans le portail Azure. Toutefois, si vous devez activer ces propriétés sur un coffre de clés existant, vous devez utiliser PowerShell ou Azure CLI.
1. Pour activer à la fois la suppression réversible et la protection contre le vidage lors de la création d'un coffre, utilisez la commande [az keyvault create](/cli/azure/keyvault#az_keyvault_create).

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Pour ajouter la protection contre le vidage à un coffre existant (où la suppression réversible est déjà activée), utilisez la commande [az keyvault update](/cli/azure/keyvault#az_keyvault_update).

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Pour créer des clés, procédez comme suit :
    1. Pour créer une clé, sélectionnez **Générer/Importer** dans le menu **Clés** sous **Paramètres**.
        
        ![Sélectionner le bouton Générer/Importer](./media/configure-customer-managed-key/select-generate-import.png)
    1. Définissez **Options** sur **Générer** et donnez un nom à la clé.

        ![Créer une clé](./media/configure-customer-managed-key/create-key.png) 
    1. Vous pouvez maintenant sélectionner cette clé dans la liste déroulante pour l'associer à l'espace de noms Event Hubs à des fins de chiffrement. 

        ![Sélectionner une clé dans le coffre de clés](./media/configure-customer-managed-key/select-key-from-key-vault.png)

        > [!NOTE]
        > À des fins de redondance, vous pouvez ajouter jusqu’à 3 clés. Dans le cas où l’une des clés a expiré ou n’est pas accessible, les autres clés seront utilisées pour le chiffrement.
    1. Renseignez les détails de la clé, puis cliquez sur **Sélectionner**. Cela permet le chiffrement de la clé gérée par Microsoft avec votre clé (clé gérée par le client). 

## <a name="managed-identities"></a>Identités managées
Il existe deux types d’identités managées que vous pouvez assigner à un espace de noms Event Hubs.

- **Affecté par le système** : vous pouvez activer une identité managée directement sur un espace de noms Event Hubs. Lorsque vous activez une identité managée affectée par le système, une identité est créée dans Azure AD qui est liée au cycle de vie de cet espace de noms Event Hubs. Ainsi, quand l’espace de noms est supprimée, Azure supprime automatiquement l’identité. Par défaut, seule cette ressource Azure (espace de noms) peut utiliser cette identité pour demander des jetons à Azure AD.
- **Affecté par l’utilisateur** : vous pouvez également créer une identité managée en tant que ressource Azure autonome, laquelle est appelée identité affectée par l’utilisateur. Vous pouvez créer une identité managée affectée par l’utilisateur et l’attribuer à un ou plusieurs espace(s) de noms Event Hubs. Une identité managée affectée par l’utilisateur est gérée séparément des ressources qui l’utilisent. Elles ne sont pas liées au cycle de vie de l’espace de noms. Vous pouvez explicitement supprimer une identité affectée par l’utilisateur lorsque vous n’en n’avez plus besoin.    

    Pour plus d’informations, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md).


## <a name="encrypt-using-system-assigned-identities-template"></a>Chiffrer à l’aide des identités affectées par le système (modèle)
Cette section montre comment effectuer les tâches suivantes à l’aide de **modèles Azure Resource Manager**. 

1. Créez un **espace de noms Event Hubs** avec une identité du service managé.
2. Créez un **coffre de clés** et autorisez l'identité du service à accéder au coffre de clés. 
3. Mettez à jour l’espace de noms Event Hubs avec les informations du coffre de clés (clé/valeur). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Créer un cluster et un espace de noms Event Hubs avec une identité du service managé
Cette section vous montre comment créer un espace de noms Azure Event Hubs avec identité managée du service à l’aide d’un modèle Azure Resource Manager et de PowerShell. 

1. Créez un modèle Resource Manager pour créer un espace de noms Event Hubs avec une identité du service managé. Nommez le fichier : **CreateEventHubClusterAndNamespace.json** : 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Créez un fichier de paramètres de modèle nommé : **CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Remplacez les valeurs suivantes : 
    > - `<EventHubsClusterName>` - Nom de votre cluster Event Hubs    
    > - `<EventHubsNamespaceName>` - Nom de votre espace de noms Event Hubs
    > - `<Location>` - Emplacement de votre espace de noms Event Hubs

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Exécutez la commande PowerShell suivante pour déployer le modèle afin de créer un espace de noms Event Hubs. Ensuite, récupérez l’ID de l’espace de noms Event Hubs pour l’utiliser ultérieurement. Remplacez `{MyRG}` par le nom du groupe de ressources avant d'exécuter la commande.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Autorisez l'identité d'espace de noms Event Hubs à accéder au coffre de clés.

1. Exécutez la commande suivante pour créer un coffre de clés avec la **protection contre le vidage** et la **suppression réversible** activées. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (OU)    
    
    Exécutez la commande suivante pour mettre à jour un **coffre de clés existant**. Spécifiez des valeurs pour les noms de groupe de ressources et de coffre de clés avant d’exécuter la commande. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Définissez la stratégie d’accès au coffre de clés afin que l’identité managée de l’espace de noms Event Hubss puisse accéder à la valeur de clé dans le coffre de clés. Utilisez l’ID de l’espace de noms Event Hubs de la section précédente. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Chiffrer les données dans l’espace de noms Event Hubs avec une clé gérée par le client à partir du coffre de clés
Jusqu’à présent, vous avez effectué les étapes suivantes : 

1. Création d’un espace de noms Premium avec une identité managée.
2. Création d’un coffre de clés et octroi de l’accès au coffre de clés à l’identité managée. 

Dans cette étape, vous allez mettre à jour l’espace de noms Event Hubs avec des informations du coffre de clés. 

1. Créez un fichier JSON nommé **CreateEventHubClusterAndNamespace.json** avec le contenu suivant : 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Créez un fichier de paramètres de modèle : **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Remplacez les valeurs suivantes : 
    > - `<EventHubsClusterName>` - Nom de votre cluster Event Hubs        
    > - `<EventHubsNamespaceName>` - Nom de votre espace de noms Event Hubs
    > - `<Location>` - Emplacement de votre espace de noms Event Hubs
    > - `<KeyVaultName>` - Nom de votre coffre de clés
    > - `<KeyName>` : Nom de la clé dans le coffre de clés

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Exécutez la commande PowerShell suivante pour déployer le modèle Resource Manager. Remplacez `{MyRG}` par le nom de votre groupe de ressources avant d’exécuter la commande. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="encrypt-using-user-assigned-identities-template"></a>Chiffrer à l’aide des identités affectées par l’utilisateur (modèle)

1. Créer une **identité affectée par l’utilisateur**.
1. Créer un **coffre de clés** et autoriser l’accès à l’identité affectée par l’utilisateur via les stratégies d’accès.
1. Créer un **espace de noms Event Hubs** à l’aide des informations de l’identité affectée par l’utilisateur et du coffre de clés.

### <a name="create-a-user-assigned-identity"></a>Créer une identité attribuée par l’utilisateur
Créez une identité affectée par l’utilisateur à l’aide des instructions fournies dans l’article [Créer une identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Vous pouvez également créer une identité affectée par l’utilisateur à l’aide de [CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md), de [PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), du [modèle Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md), et de [REST](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md). 

> [!NOTE]
> Vous pouvez affecter jusqu’à **4** identités utilisateur à un espace de noms. Ces associations sont supprimées lorsque l’espace de noms est supprimé ou lorsque vous transmettez le `identity -> type` dans le modèle à `None`. 

### <a name="create-a-key-vault-and-grant-access-to-user-assigned-identity"></a>Créer un coffre de clés et autoriser l’accès à l’identité affectée par l’utilisateur 

1. Exécutez la commande suivante pour créer un coffre de clés avec la protection contre le vidage et la suppression réversible activées.

    ```azurepowershell-interactive
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName} -Location "{location}" -EnableSoftDelete -EnablePurgeProtection           
    ```
    
    (OU)

    Exécutez la commande suivante pour mettre à jour un coffre de clés existant. Spécifiez des valeurs pour les noms de groupe de ressources et de coffre de clés avant d’exécuter la commande.

    ```azurepowershell-interactive
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force            
    ```
2. Obtenir l’**ID du Principal de service** pour l’identité de l’utilisateur à l’aide de la commande PowerShell suivante. Dans l’exemple, `ud1` est l’identité affectée par l’utilisateur à utiliser pour le chiffrement.

    ```azurepowershell-interactive
    $servicePrincipal=Get-AzADServicePrincipal -SearchString "ud1"    
    ```
3. Accordez à l’identité affectée à l’utilisateur l’accès au coffre de clés en affectant une stratégie d’accès.     

    ```azurepowershell-interactive
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $servicePrincipal.Id -PermissionsToKeys get,wrapKey,unwrapKey,list    
    ```

    > [!NOTE]
    > Vous pouvez ajouter jusqu’à **3** clés, mais l’identité de l’utilisateur utilisée pour le chiffrement doit être la même pour toutes les clés. Actuellement, seule l’identité de chiffrement unique est prise en charge. 

### <a name="create-an-event-hubs-namespace-with-user-identity-and-key-vault-information"></a>Créer un espace de noms Event Hubs à l’aide des informations de l’identité affectée par l’utilisateur et du coffre de clés
Cette section vous donne un exemple qui illustre comment effectuer les tâches suivantes à l’aide d’un modèle Azure Resource Manager. 

- Assignez une identité managée par l’utilisateur à un espace de noms Event Hubs.

    ```json
                "identity": {
                    "type": "UserAssigned",
                    "userAssignedIdentities": {
                        "[parameters('identity').userAssignedIdentity]": {}
                    }
                },
    ```    
- Activez le chiffrement sur l’espace de noms en spécifiant une clé de votre coffre de clés et l’identité managée par l’utilisateur pour accéder à la clé. 

    ```json
                    "encryption":{
                       "keySource":"Microsoft.KeyVault",
                       "keyVaultProperties":[
                            {
                                "keyName": "[parameters('keyName')]",
                                "keyVaultUri": "[parameters('keyVaultUri')]",
                                "identity": {
                                    "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
                                }
                            }
                       ]
                    }
    ```
   

1. Créez un fichier JSON nommé **UpdateServiceBusNamespaceWithEncryption.json** avec le contenu suivant :

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
        "clusterName":{
            "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             },
         "identity": {
            "type": "Object",
            "defaultValue": {
                "userAssignedIdentity": ""
            },
            "metadata": {
                "description": "user-assigned identity."
            }
         }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2021-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[parameters('identity').userAssignedIdentity]": {}
                }
            },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                        {
                            "keyName": "[parameters('keyName')]",
                            "keyVaultUri": "[parameters('keyVaultUri')]",
                            "identity": {
                                "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
                            }
                        }
                   ]
                }
             }
          }
       ]
    }        
    ```  
1. Créez un fichier de paramètres de modèle :  **CreateEventHubsNamespaceWithUserIdentityAndEncryptionParams.json**.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "identity": {
            "value": {
                "userAssignedIdentity": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER MANAGED IDENTITY NAME>"
            }
         }
       }
    }
    ```

    Dans le fichier de paramètres, remplacez les espaces réservés par les valeurs appropriées.
    
    | Espace réservé | value | 
    | ----------- | ----- | 
    | `<EventHubsNamespaceName>` | Nom de l’espace de noms Event Hubs. | 
    | `<Location>` | Emplacement où vous souhaitez que soit créé l’espace de noms. | 
    | `<KeyVaultName>` | Nom du coffre de clés. | 
    | `<KeyName>` | Nom de la clé dans le coffre de clés. | 
    | `<AZURE SUBSCRIPTION ID>` | Votre ID d’abonnement Azure. |
    | `<RESOURCE GROUP NAME>` | Groupe de ressources de l’identité managée par l’utilisateur. | 
    | `<USER MANAGED IDENTITY NAME>` | Nom de l’identité managée par l’utilisateur. | 

3. Exécutez la commande PowerShell suivante pour déployer le modèle Resource Manager. Remplacez `{MyRG}` par le nom de votre groupe de ressources avant d’exécuter la commande.

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name CreateEventHubsNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./ CreateEventHubsNamespaceWithUserIdentityAndEncryption.json -TemplateParameterFile ./ CreateEventHubsNamespaceWithUserIdentityAndEncryptionParams.json        
    ```


## <a name="use-both-user-assigned-and-system-assigned-identities"></a>Utiliser les identités affectées par l’utilisateur et affectées par le système
Un espace de noms peut avoir en même temps une identité affectée par le système et une identité affectée par l’utilisateur. Dans ce cas, la `type` propriété devrait être `SystemAssigned`, `UserAssigned` comme indiqué dans l’exemple suivant. 

```json
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userIdentity1>" : {}
    }
}
```

Dans ce scénario, vous pouvez choisir l’identité affectée par le système ou l’identité affectée par l’utilisateur pour chiffrer les données au repos.  

Dans le modèle Ressource Manager, si vous ne spécifiez pas d'`identity`attribut, l’identité managée par le système est utilisée. Voici un extrait d’exemple. 

```json
"properties":{
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]"
         }
      ]
   }
}
```

Pour plus d’informations sur l’utilisation de l’identité managée par l’utilisateur pour le chiffrement, consultez l’exemple suivant. Notez que l’`identity` attribut est défini sur l’identité managée par l’utilisateur. 

```json
"properties":{
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]",
            "identity": {
                "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
            }
         }
      ]
   }
}
```

## <a name="enable-infrastructure-or-double-encryption-of-data"></a>Activer le (double) chiffrement de données de l’infrastructure
Si vous avez besoin d’un niveau plus élevé de garantie quant à la sécurisation de vos données, vous pouvez activer le chiffrement au niveau de l’infrastructure, également appelé Double chiffrement. 

Lorsque le chiffrement d’infrastructure est activé, les données d’un espace de noms Event Hubs sont chiffrées deux fois, une fois au niveau du service et une fois au niveau de l’infrastructure, à l’aide de deux algorithmes de chiffrement différents et deux clés différentes. Ainsi, le chiffrement de l’infrastructure des données Event Hubs permet d’éviter un scénario impliquant une possible compromission d’un algorithme ou d’une clé de chiffrement.

Vous pouvez activer le chiffrement de l’infrastructure en actualisant le modèle Azure Ressource Manager avec la propriété `requireInfrastructureEncryption` dans **CreateEventHubClusterAndNamespace.json** ci-dessus, comme indiqué ci-après. 

```json
"properties":{
   "isAutoInflateEnabled":false,
   "maximumThroughputUnits":0,
   "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "requireInfrastructureEncryption":true,
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]"
         }
      ]
   }
}
```

## <a name="rotate-revoke-and-cache-encryption-keys"></a>Rotation, révocation et mise en cache des clés de chiffrement

### <a name="rotate-your-encryption-keys"></a>Faire pivoter vos clés de chiffrement
Vous pouvez faire pivoter votre clé dans le coffre de clés à l'aide du mécanisme de rotation d'Azure Key Vault. Des dates d'activation et d'expiration peuvent également être définies pour automatiser la rotation des clés. Le service Event Hubs détectera les nouvelles versions des clés et commencera à les utiliser automatiquement.

### <a name="revoke-access-to-keys"></a>Révoquer l'accès aux clés
La révocation de l'accès aux clés de chiffrement ne videra pas les données d'Event Hubs. Mais il sera impossible d'accéder aux données à partir de l'espace de noms Event Hubs. Vous pouvez révoquer la clé de chiffrement en appliquant une stratégie d'accès ou en supprimant la clé. Pour en savoir plus sur les stratégies d'accès et sur la sécurisation de votre coffre de clés, consultez [Sécuriser l'accès à un coffre de clés](../key-vault/general/security-features.md).

Une fois la clé de chiffrement révoquée, le service Event Hubs devient inutilisable sur l'espace de noms chiffré. Si l'accès à la clé est activé ou si la clé de suppression est restaurée, le service Event Hubs choisira la clé afin de vous permettre d'accéder aux données à partir de l'espace de noms Event Hubs chiffré.

### <a name="caching-of-keys"></a>Mise en cache des clés
L’instance Event Hubs (event hub) interroge ses clés de chiffrement répertoriées toutes les 5 minutes. Elle les met en cache et les utilise jusqu’à la prochaine interrogation, soit après 5 minutes. Tant qu’au moins une clé est disponible, Event Hub est accessible. Si toutes les clés répertoriées sont inaccessibles lors de l’interrogation, l’ensemble des event hubs deviendront indisponibles. 

Voici des informations détaillées concernant ces options : 

- Toutes les 5 minutes, le service Event Hubs interroge toutes les clés managées par le client et répertoriées dans l’enregistrement de l’espace de noms :
    - Si une clé a pivoté, l’enregistrement est mis à jour avec la nouvelle clé.
    - Si une clé a été révoquée, la clé est supprimée de l’enregistrement.
    - Si toutes les clés ont été révoquées, l’état de chiffrement de l’espace de noms est défini sur **Révoqué**. Il sera impossible d'accéder aux données à partir de l'espace de noms Event Hubs.

## <a name="considerations-when-using-geo-disaster-recovery"></a>Considérations relatives à l’utilisation de la géorécupération d’urgence

> [!IMPORTANT]
> Pour activer la géorécupération d'urgence sur un espace de noms utilisant le chiffrement BYOK, une identité managée affectée par le système ou par l’utilisateur doit être activée sur l'espace de noms secondaire destiné à l'appariement. 

### <a name="geo-disaster-recovery---encryption-with-system-assigned-identities"></a>Géo-récupération d’urgence - chiffrement avec des identités affectées par le système
Pour activer le chiffrement d’une clé managée par Microsoft à l’aide d’une clé managée par le client, une [stratégie d’accès](../key-vault/general/secure-your-key-vault.md) est configurée pour une identité managée affectée par le système sur le coffre de clés Azure spécifié. Cela garantit un accès contrôlé au Coffre de clés Azure à partir de l’espace de noms Event Hubs Azure.

Pour cette raison :

- Si [la géorécupération d’urgence](event-hubs-geo-dr.md) est déjà activée pour l’espace de noms Event Hubs et que vous cherchez à activer la clé managée par le client, alors :
    - Arrêtez l’appairage.
    - [Configurez la stratégie d’accès](../key-vault/general/assign-access-policy-portal.md) pour l’identité managée affectée par le système pour les espaces de noms principal et secondaire sur le coffre de clés.
    - Configurez le chiffrement sur l’espace de noms principal.
    - Appairez à nouveau les espaces de noms principal et secondaire.
- Si vous envisagez d’activer la géorécupération d’urgence sur un espace de noms Event Hubs où la clé managée par le client est déjà configurée, alors procédez aux étapes suivantes : 
    - [Configurez la stratégie d’accès](../key-vault/general/assign-access-policy-portal.md) de l’identité managée pour l’espace de noms secondaire sur le coffre de clés.
    - Appairez les espaces de noms principal et secondaire.

### <a name="geo-disaster-recovery---encryption-with-user-assigned-identities"></a>Géo-récupération d’urgence - chiffrement avec des identités affectées par l’utilisateur
Voici quelques recommandations : 

1.  Créez une identité managée et affectez des autorisations de coffre de clé à votre identité managée. 
2.  Ajoutez l’identité en tant qu’identité affectée par l’utilisateur et activez le chiffrement avec l’identité sur les deux espaces de noms. 
3.  Associer des espaces de noms 

Conditions d’activation de la géorécupération d’urgence et du chiffrement avec des identités affectées par l’utilisateur :

1.  Le chiffrement doit déjà être activé pour l’espace de noms secondaire avec une identité affectée par l’utilisateur s’il doit être associé à un espace de noms principal pour lequel le Chiffrement est activé. 
2.  Il n’est pas possible d’activer le Chiffrement sur une base de données primaire déjà couplée, même si la base de données secondaire a une identité affectée par l’utilisateur associée à l’espace de noms.

## <a name="set-up-diagnostic-logs"></a>Configurer les journaux de diagnostic 
En configurant les journaux de diagnostic pour les espaces de noms BYOK, vous disposez des informations requises sur les opérations. Ces journaux peuvent être activés et ultérieurement transmis en continu à un hub d'événements, analysés via l'analytique des journaux d'activité ou transmis en continu au stockage pour effectuer des analyses personnalisées. Pour en savoir plus sur les journaux de diagnostic, consultez [Présentation des journaux de diagnostic Azure](../azure-monitor/essentials/platform-logs-overview.md). Pour visualiser le schéma, consultez les [Informations de référence sur les données de supervision](monitor-event-hubs-reference.md#customer-managed-key-user-logs-schema).

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :
- [Vue d’ensemble d’Event Hubs](event-hubs-about.md)
- [Vue d'ensemble de Key Vault](../key-vault/general/overview.md)