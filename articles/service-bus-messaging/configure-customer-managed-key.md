---
title: Configurer votre propre clé pour chiffrer les données Azure Service Bus au repos
description: Cet article vous explique comment configurer votre propre clé pour chiffrer les données Azure Service Bus au repos.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 58ce3559a9524abdd3391f9d663651c58a20144d
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122535384"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest"></a>Configurer les clés gérées par le client pour le chiffrement des données Azure Service Bus au repos
Azure Service Bus Premium fournit une fonctionnalité de chiffrement des données au repos à l’aide d’Azure Storage Service Encryption (Azure SSE). Service Bus Premium utilise Stockage Azure pour stocker les données. Toutes les données stockées avec Stockage Azure sont chiffrées à l'aide de clés gérées par Microsoft. Si vous utilisez votre propre clé (méthode également appelée Bring Your Own Key (BYOK) ou clé gérée par le client), les données sont toujours chiffrées à l'aide de la clé gérée par Microsoft, mais, en outre, la clé gérée par Microsoft est chiffrée à l'aide de la clé gérée par le client. Cette fonctionnalité vous permet de créer, de faire tourner, de désactiver et de révoquer l'accès aux clés gérées par le client et utilisées pour chiffrer les clés gérées par Microsoft. L'activation de la fonctionnalité BYOK sur votre espace de noms ne s'effectue qu'une seule fois.

Il existe quelques inconvénients à la clé gérée par le client pour le chiffrement côté service. 
- Cette fonctionnalité est prise en charge par le niveau de service [Azure Service Bus Premium](service-bus-premium-messaging.md). Elle ne peut pas être activée pour les espaces de noms Service Bus de niveau standard.
- Le chiffrement ne peut être activé que pour les espaces de noms nouveaux ou vides. Si l’espace de noms contient des files d’attente ou des rubriques, l’opération de chiffrement échoue.

Vous pouvez utiliser Azure Key Vault pour gérer vos clés et effectuer un audit sur leur utilisation. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-azure-portal"></a>Activer des clés gérées par le client (portail Azure)
Pour activer des clés gérées par le client dans le portail Azure, procédez comme suit :

1. Accédez à votre espace de noms Service Bus Premium.
2. Dans la page **Paramètres** de votre espace de noms Service Bus, sélectionnez **Chiffrement**.
3. Sélectionnez **Clé gérée par le client**, comme indiqué sur l'illustration suivante.

    ![Activer une clé gérée par le client](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Configurer un coffre de clés avec des clés

Après avoir activé une clé gérée par le client, vous devez l’associer à votre espace de noms Azure Service Bus. Service Bus prend uniquement en charge Azure Key Vault. Si vous activez l'option **Chiffrement à l'aide de la clé gérée par le client** dans la section précédente, vous devez importer la clé dans Azure Key Vault. En outre, les fonctionnalités **Suppression réversible** et **Ne pas vider** doivent être configurées pour la clé. Ces paramètres peuvent être configurés à l'aide de [PowerShell](../key-vault/general/key-vault-recovery.md) ou de l'[interface CLI](../key-vault/general/key-vault-recovery.md).

1. Pour créer un coffre de clés, suivez le [guide de démarrage rapide](../key-vault/general/overview.md) d'Azure Key Vault. Pour plus d'informations sur l'importation de clés existantes, consultez [Présentation des clés, des secrets et des certificats](../key-vault/general/about-keys-secrets-certificates.md).

    > [!IMPORTANT]
    > Pour utiliser des clés gérées par le client avec Azure Service Bus, le coffre de clés doit contenir deux propriétés requises configurées. Il s'agit de :  **Suppression réversible** et **Ne pas vider**. Ces propriétés sont activées par défaut lorsque vous créez un coffre de clés dans le portail Azure. Toutefois, si vous devez activer ces propriétés sur un coffre de clés existant, vous devez utiliser PowerShell ou Azure CLI.
1. Pour activer à la fois la suppression réversible et la protection contre le vidage lors de la création d'un coffre, utilisez la commande [az keyvault create](/cli/azure/keyvault#az_keyvault_create).

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Pour ajouter la protection contre le vidage à un coffre existant (où la suppression réversible est déjà activée), utilisez la commande [az keyvault update](/cli/azure/keyvault#az_keyvault_update).

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Pour créer des clés, procédez comme suit :
    1. Pour créer une clé, sélectionnez **Générer/Importer** dans le menu **Clés** sous **Paramètres**.
        
        ![Sélectionner le bouton Générer/Importer](./media/configure-customer-managed-key/select-generate-import.png)

    1. Définissez **Options** sur **Générer** et donnez un nom à la clé.

        ![Créer une clé](./media/configure-customer-managed-key/create-key.png) 

    1. Vous pouvez maintenant sélectionner cette clé dans la liste déroulante pour l’associer à l’espace de noms Service Bus à des fins de chiffrement. 

        ![Sélectionner une clé dans le coffre de clés](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > À des fins de redondance, vous pouvez ajouter jusqu’à 3 clés. Dans le cas où l’une des clés a expiré ou n’est pas accessible, les autres clés seront utilisées pour le chiffrement.
        
    1. Renseignez les détails de la clé, puis cliquez sur **Sélectionner**. Cela permet le chiffrement de la clé gérée par Microsoft avec votre clé (clé gérée par le client). 


    > [!IMPORTANT]
    > Si vous envisagez d’utiliser une clé gérée par le client avec la géorécupération d’urgence, veuillez consulter cette section. 
    >
    > Pour activer le chiffrement d’une clé gérée par Microsoft à l’aide d’une clé gérée par le client, une [stratégie d’accès](../key-vault/general/security-features.md) est configurée pour l’identité managée de Service Bus sur le coffre Azure Key Vault spécifié. Cela garantit un accès contrôlé à Azure Key Vault à partir de l’espace de noms Azure Service Bus.
    >
    > Pour cette raison :
    > 
    >   * Si [la géorécupération d’urgence](service-bus-geo-dr.md) est déjà activée pour l’espace de noms Service Bus et que vous cherchez à activer la clé gérée par le client, alors : 
    >     * Brisez l’appairage.
    >     * [Configurez la stratégie d’accès](../key-vault/general/assign-access-policy-portal.md) de l’identité managée pour les espaces de noms principal et secondaire sur le coffre de clés.
    >     * Configurez le chiffrement sur l’espace de noms principal.
    >     * Appairez à nouveau les espaces de noms principal et secondaire.
    > 
    >   * Si vous envisagez d’activer la géorécupération d’urgence sur un espace de noms Service Bus où la clé gérée par le client est déjà configurée, alors :
    >     * [Configurez la stratégie d’accès](../key-vault/general/assign-access-policy-portal.md) de l’identité managée pour l’espace de noms secondaire sur le coffre de clés.
    >     * Appairez les espaces de noms principal et secondaire.

## <a name="managed-identities"></a>Identités managées
Il existe deux types d’identités managées que vous pouvez assigner à un espace de noms Service Bus.

- **Affecté par le système** : vous pouvez activer une identité managée directement sur un espace de noms Service Bus. Lorsque vous activez une identité managée affectée par le système, une identité est créée dans Azure AD qui est liée au cycle de vie de cet espace de noms Service Bus. Ainsi, quand l’espace de noms est supprimé, Azure supprime automatiquement l’identité. Par défaut, seule cette ressource Azure (espace de noms) peut utiliser cette identité pour demander des jetons à Azure AD.
- **Affecté par l’utilisateur** : vous pouvez également créer une identité managée en tant que ressource Azure autonome, laquelle est appelée identité affectée par l’utilisateur. Vous pouvez créer une identité managée affectée par l’utilisateur et l’attribuer à un ou plusieurs espace(s) de noms Service Bus. Une identité managée affectée par l’utilisateur est gérée séparément des ressources qui l’utilisent. Ils ne sont pas liés au cycle de vie de l’espace de noms. Vous pouvez explicitement supprimer une identité affectée par l’utilisateur lorsque vous n’en n’avez plus besoin.     

    Pour plus d’informations, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="encrypt-using-system-assigned-identities-template"></a>Chiffrer à l’aide des identités affectées par le système (modèle)
Cette section montre comment effectuer les tâches suivantes : 

1. Créez un espace de noms Service Bus **Premium** avec une **identité managée du service**.
2. Créez un **coffre de clés** et accordez l’accès au coffre de clés à l’identité du service. 
3. Mettez à jour l’espace de noms Service Bus avec les informations du coffre de clés (clé/valeur). 

### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Créer un espace de noms Service Bus Premium avec une identité managée du service
Cette section vous montre comment créer un espace de noms Azure Service Bus avec une identité managée du service à l’aide d’un modèle Azure Resource Manager et de PowerShell. 

1. Créez un modèle Azure Resource Manager pour créer un espace de noms Service Bus de niveau de service Premium avec une identité managée du service. Nommez le fichier : **CreateServiceBusPremiumNamespace.json** : 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Créez un fichier de paramètres de modèle nommé : **CreateServiceBusPremiumNamespaceParams.json**. 

    > [!NOTE]
    > Remplacez les valeurs suivantes : 
    > - `<ServiceBusNamespaceName>` : Nom de votre espace de noms Service Bus
    > - `<Location>` : Emplacement de votre espace de noms Service Bus

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Exécutez la commande PowerShell suivante pour déployer le modèle afin de créer un espace de noms Service Bus Premium. Ensuite, récupérez l’ID de l’espace de noms Service Bus pour l’utiliser ultérieurement. Remplacez `{MyRG}` par le nom du groupe de ressources avant d’exécuter la commande.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Autorisez l’identité d’espace de noms Service Bus à accéder au coffre de clés.

1. Exécutez la commande suivante pour créer un coffre de clés avec la **protection contre le vidage** et la **suppression réversible** activées. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (OU)
    
    Exécutez la commande suivante pour mettre à jour un **coffre de clés existant**. Spécifiez des valeurs pour les noms de groupe de ressources et de coffre de clés avant d’exécuter la commande. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Définissez la stratégie d’accès au coffre de clés afin que l’identité managée de l’espace de noms Service Bus puisse accéder à la valeur de clé dans le coffre de clés. Utilisez l’ID de l’espace de noms Service Bus de la section précédente. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Chiffrer les données dans l’espace de noms Service Bus avec une clé gérée par le client à partir du coffre de clés 
Jusqu’à présent, vous avez effectué les étapes suivantes : 

1. Création d’un espace de noms Premium avec une identité managée.
2. Création d’un coffre de clés et octroi de l’accès au coffre de clés à l’identité managée. 

Dans cette étape, vous allez mettre à jour l’espace de noms Service Bus avec des informations du coffre de clés. 

1. Créez un fichier JSON nommé **UpdateServiceBusNamespaceWithEncryption.json** avec le contenu suivant : 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
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
          }
       ]
    }
    ``` 

2. Créez un fichier de paramètres de modèle : **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Remplacez les valeurs suivantes : 
    > - `<ServiceBusNamespaceName>` : Nom de votre espace de noms Service Bus
    > - `<Location>` : Emplacement de votre espace de noms Service Bus
    > - `<KeyVaultName>` : Nom de votre coffre de clés
    > - `<KeyName>` : Nom de la clé dans le coffre de clés  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```    

## <a name="encrypt-using-user-assigned-identities-template"></a>Chiffrer à l’aide des identités affectées par l’utilisateur (modèle)

1. Créer une **identité affectée par l’utilisateur**.
1. Créer un **coffre de clés** et autoriser l’accès à l’identité affectée par l’utilisateur via des stratégies d’accès.
1. Créer un espace de noms Service Bus **Premium** à l’aide des informations de l’identité affectée par l’utilisateur et du coffre de clés.

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
2. Obtenir l’**ID du Principal de service** pour l’identité de l’utilisateur à l’aide de la commande PowerShell suivante : Dans l’exemple, `ud1` est l’identité affectée par l’utilisateur à utiliser pour le chiffrement.

    ```azurepowershell-interactive
    $servicePrincipal=Get-AzADServicePrincipal -SearchString "ud1"    
    ```
3. Accordez à l’identité affectée à l’utilisateur l’accès au coffre de clés en affectant une stratégie d’accès.     

    ```azurepowershell-interactive
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $servicePrincipal.Id -PermissionsToKeys get,wrapKey,unwrapKey,list    
    ```

    > [!NOTE]
    > Vous pouvez ajouter jusqu’à **3** clés, mais l’identité de l’utilisateur utilisée pour le chiffrement doit être la même pour toutes les clés. Actuellement, seule l’identité de chiffrement unique est prise en charge.

### <a name="create-a-premium-service-bus-namespace-with-user-identity-and-key-vault-information"></a>Créer un espace de noms Service Bus Premium à l’aide des informations de l’identité de l’utilisateur et du coffre de clés
Cette section vous donne un exemple qui illustre comment effectuer les tâches suivantes à l’aide d’un modèle Azure Resource Manager. 

- Attribuez une identité managée par l’utilisateur à un espace de noms Service Bus.

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
   

1. Créez un fichier JSON nommé **CreateServiceBusNamespaceWithUserIdentityAndEncryption.json** avec le contenu suivant :

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2021-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Premium",
                "tier":"Premium",
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
1. Créez un fichier de paramètres de modèle :  **CreateServiceBusNamespaceWithUserIdentityAndEncryptionParams.json**.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
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
    | `<ServiceBusNamespaceName>` | Nom de l’espace de noms Service Bus. | 
    | `<Location>` | Emplacement où vous souhaitez que soit créé l’espace de noms. | 
    | `<KeyVaultName>` | Nom du coffre de clés. | 
    | `<KeyName>` | Nom de la clé dans le coffre de clés. | 
    | `<AZURE SUBSCRIPTION ID>` | Votre ID d’abonnement Azure. |
    | `<RESOURCE GROUP NAME>` | Groupe de ressources de l’identité managée par l’utilisateur. | 
    | `<USER MANAGED IDENTITY NAME>` | Nom de l’identité managée par l’utilisateur. | 

3. Exécutez la commande PowerShell suivante pour déployer le modèle Resource Manager. Remplacez `{MyRG}` par le nom de votre groupe de ressources avant d’exécuter la commande.

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name CreateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./ CreateServiceBusNamespaceWithUserIdentityAndEncryption.json -TemplateParameterFile ./ CreateServiceBusNamespaceWithUserIdentityAndEncryptionParams.json        
    ```


## <a name="use-both-user-assigned-and-system-assigned-identities"></a>Utiliser les identités affectées par l’utilisateur et affectées par le système
Un espace de noms peut avoir en même temps une identité affectée par le système et une identité affectée par l’utilisateur. Dans ce cas, la propriété `type` devrait être `SystemAssigned`, `UserAssigned` comme indiqué dans l’exemple suivant : 

```json
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userIdentity1>" : {}
    }
}
```

Dans ce scénario, vous pouvez choisir l’identité affectée par le système ou l’identité affectée par l’utilisateur pour chiffrer les données au repos.  

Dans le modèle Resource Manager, si vous ne spécifiez pas un attribut `identity`, l’identité managée par le système est utilisée. Voici un extrait d’exemple. 

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

Pour plus d’informations sur l’utilisation de l’identité managée par l’utilisateur pour le chiffrement, consultez l’exemple suivant. Notez que l’attribut `identity` est défini sur l’identité managée par l’utilisateur. 

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

## <a name="enable-infrastructure-double-encryption-of-data"></a>Activer le chiffrement (double) d’infrastructure des données
Si vous avez besoin d’un niveau de garantie plus élevé quant à la sécurisation de vos données, vous pouvez activer le chiffrement au niveau de l’infrastructure, également appelé Chiffrement double. 

Lorsque le chiffrement d’infrastructure est activé, les données de l’instance Azure Service Bus sont chiffrées deux fois, une fois au niveau du service et une fois au niveau de l’infrastructure, avec deux algorithmes de chiffrement différents et deux clés différentes. Ainsi, le chiffrement d’infrastructure des données Azure Service Bus permet d’éviter un scénario impliquant une possible compromission d’un algorithme ou d’une clé de chiffrement.

Vous pouvez activer le chiffrement d’infrastructure en actualisant le modèle Azure Resource Manager avec la propriété `requireInfrastructureEncryption` dans **UpdateServiceBusNamespaceWithEncryption.json** ci-dessus, comme indiqué ci-après. 

```json
"properties":{
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

## <a name="rotate-revoke-and-cache-keys"></a>Faire pivoter, révoquer et mettre en cache les clés

### <a name="rotate-your-encryption-keys"></a>Faire pivoter vos clés de chiffrement

Vous pouvez faire pivoter votre clé dans le coffre de clés à l'aide du mécanisme de rotation d'Azure Key Vault. Des dates d'activation et d'expiration peuvent également être définies pour automatiser la rotation des clés. Le service Service Bus détectera les nouvelles versions des clés et commencera à les utiliser automatiquement.

### <a name="revoke-access-to-keys"></a>Révoquer l'accès aux clés

La révocation de l’accès aux clés de chiffrement ne videra pas les données de Service Bus. Cependant, il sera impossible d’accéder aux données à partir de l’espace de noms Service Bus. Vous pouvez révoquer la clé de chiffrement en appliquant une stratégie d'accès ou en supprimant la clé. Pour en savoir plus sur les stratégies d'accès et sur la sécurisation de votre coffre de clés, consultez [Sécuriser l'accès à un coffre de clés](../key-vault/general/secure-your-key-vault.md).

Une fois la clé de chiffrement révoquée, le service Service Bus devient inutilisable sur l’espace de noms chiffré. Si l’accès à la clé est activé ou si la clé supprimée est restaurée, le service Service Bus choisira la clé afin de vous permettre d’accéder aux données à partir de l’espace de noms Service Bus chiffré.

### <a name="caching-of-keys"></a>Mise en cache des clés
L’instance de Service Bus interroge ses clés de chiffrement répertoriées toutes les 5 minutes. Elle les met en cache et les utilise jusqu’à la prochaine interrogation, soit après 5 minutes. Tant qu’au moins une clé est disponible, les files d’attente et les rubriques restent accessibles. Si toutes les clés répertoriées ne sont pas accessibles lors de l’interrogation, toutes les files d’attente et rubriques ne seront plus disponibles. 

Voici des informations détaillées concernant ces options : 

- Toutes les 5 minutes, le service Service Bus interroge toutes les clés gérées par le client et répertoriées dans l’enregistrement de l’espace de noms :
    - Si une clé a pivoté, l’enregistrement est mis à jour avec la nouvelle clé.
    - Si une clé a été révoquée, la clé est supprimée de l’enregistrement.
    - Si toutes les clés ont été révoquées, l’état de chiffrement de l’espace de noms est défini sur **Révoqué**. Il n’est pas possible d’accéder aux données à partir de l’espace de noms Service Bus. 

## <a name="considerations-when-using-geo-disaster-recovery"></a>Considérations relatives à l’utilisation de la géo-reprise d’activité après sinistre

### <a name="geo-disaster-recovery---encryption-with-system-assigned-identities"></a>La géo-reprise d'activité après sinistre – chiffrement avec des identités affectées par le système
Pour activer le chiffrement d’une clé managée par Microsoft à l’aide d’une clé managée par le client, une [stratégie d’accès](../key-vault/general/secure-your-key-vault.md) est configurée pour une identité managée affectée par le système sur l’instance Azure Key Vault spécifiée. Cela garantit un accès contrôlé à Azure Key Vault à partir de l’espace de noms Azure Service Bus.

Pour cette raison :

- Si [la géorécupération d’urgence](service-bus-geo-dr.md) est déjà activée pour l’espace de noms Service Bus et que vous cherchez à activer la clé gérée par le client, alors :
    - Brisez l’appairage.
    - [Configurez la stratégie d’accès](../key-vault/general/assign-access-policy-portal.md) pour l’identité managée affectée par le système pour les espaces de noms principal et secondaire sur le coffre de clés.
    - Configurez le chiffrement sur l’espace de noms principal.
    - Appairez à nouveau les espaces de noms principal et secondaire.
- Si vous envisagez d’activer la géo-reprise d'activité après sinistre sur un espace de noms Service Bus où la clé managée par le client est déjà configurée, alors procédez comme suit : 
    - [Configurez la stratégie d’accès](../key-vault/general/assign-access-policy-portal.md) de l’identité managée pour l’espace de noms secondaire sur le coffre de clés.
    - Appairez les espaces de noms principal et secondaire.

### <a name="geo-disaster-recovery---encryption-with-user-assigned-identities"></a>Géo-reprise d'activité après sinistre – chiffrement avec des identités affectées par l’utilisateur
Voici quelques recommandations : 

1.  Créez une identité managée et affectez-lui des autorisations de coffre de clé. 
2.  Ajoutez l’identité en tant qu’identité affectée par l’utilisateur et activez le chiffrement avec l’identité sur les deux espaces de noms. 
3.  Associer des espaces de noms 

Conditions d’activation de la géo-reprise d'activité après sinistre et du chiffrement avec des identités affectées par l’utilisateur :

1.  Le chiffrement doit déjà être activé pour l’espace de noms secondaire avec une identité affectée par l’utilisateur s’il doit être associé à un espace de noms principal pour lequel le chiffrement est activé. 
2.  Il n’est pas possible d’activer le chiffrement sur une base de données primaire déjà couplée, même si la base de données secondaire a une identité affectée par l’utilisateur associée à l’espace de noms.
    

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :
- [Vue d’ensemble de Service Bus](service-bus-messaging-overview.md)
- [Vue d'ensemble de Key Vault](../key-vault/general/overview.md)
