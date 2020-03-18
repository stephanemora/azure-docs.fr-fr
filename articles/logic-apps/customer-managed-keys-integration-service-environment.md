---
title: Configurer des clés gérées par le client pour chiffrer les données au repos dans des ISE
description: Créez et gérez vos propres clés de chiffrement afin de sécuriser les données au repos pour les environnements de service d’intégration (ISE) dans Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127637"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Configurer des clés gérées par le client afin de chiffrer les données au repos pour les environnements de service d’intégration (ISE) dans Azure Logic Apps

Azure Logic Apps s’appuie sur Stockage Azure pour stocker et [chiffrer automatiquement les données au repos](../storage/common/storage-service-encryption.md). Ce chiffrement protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Par défaut, Stockage Azure utilise des clés managées par Microsoft pour chiffrer vos données. Pour plus d’informations sur le fonctionnement du chiffrement Stockage Azure, consultez [Chiffrement du Stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md) et [Chiffrement des données au repos d’Azure](../security/fundamentals/encryption-atrest.md).

Quand vous créez un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) pour héberger vos applications logiques et que vous souhaitez davantage de contrôle sur les clés de chiffrement utilisées par Stockage Azure, vous pouvez configurer, utiliser et gérer votre propre clé à l’aide d’[Azure Key Vault](../key-vault/key-vault-overview.md). Cette fonctionnalité est également appelée « Bring Your Own Key » (BYOK) et votre clé est appelée « clé gérée par le client ».

Cette rubrique montre comment configurer et spécifier votre propre clé de chiffrement à utiliser lors de la création de votre ISE à l'aide de l'API REST Logic Apps. Pour connaître les étapes générales permettant de créer un environnement ISE à l'aide de l'API REST Logic Apps, consultez [Créer un environnement de service d'intégration (ISE) à l'aide de l'API REST Logic Apps](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Considérations

* À l’heure actuelle, la prise en charge des clés gérées par le client pour un ISE est disponible uniquement dans les régions Azure suivantes : USA Ouest 2, USA Est et USA Centre Sud

* Vous pouvez spécifier une clé gérée par le client *uniquement quand vous créez votre ISE*, pas après. Vous ne pouvez pas désactiver cette clé après la création de votre environnement ISE. Actuellement, il n’existe pas de prise en charge pour faire pivoter une clé gérée par le client pour un ISE.

* Pour prendre en charge les clés gérées par le client, il faut que l’[identité managée affectée par le système](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) de votre ISE soit activée. Cette identité permet à l’ISE d’authentifier l’accès aux ressources dans d’autres locataires Azure Active Directory (Azure AD) afin que vous n’ayez pas à vous connecter avec vos informations d’identification.

* Actuellement, pour créer un ISE qui prend en charge les clés gérées par le client et dont l’identité affectée par le système est activée, vous devez appeler l’API REST Logic Apps à l’aide d’une requête PUT HTTPS.

* Dans les *30 minutes* qui suivent l’envoi de la requête PUT HTTPS qui crée votre ISE, vous devez [accorder à l’identité attribuée par le système de votre ISE l’accès au coffre de clés](#identity-access-to-key-vault). Si vous ne le faites pas, la création de l’ISE échoue et génère une erreur d’autorisation.

## <a name="prerequisites"></a>Prérequis

* Les [conditions préalables](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) et la [configuration requise pour activer l'accès à votre environnement ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) sont les mêmes que lorsque vous créez un environnement ISE à partir du portail Azure.

* Un coffre de clés Azure pour lequel les propriétés **Suppression réversible** et **Ne pas vider** sont activées

  Pour plus d’informations sur l’activation de ces propriétés, consultez [Vue d’ensemble de la suppression réversible d’Azure Key Vault](../key-vault/key-vault-ovw-soft-delete.md) et [Configurer des clés gérées par le client avec Azure Key Vault](../storage/common/storage-encryption-keys-portal.md). Si vous ne connaissez pas Azure Key Vault, découvrez [comment créer un coffre de clés](../key-vault/quick-create-portal.md#create-a-vault) à l’aide du portail Azure ou à l’aide de la commande Azure PowerShell [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault).

* Dans votre coffre de clés, une clé créée avec les valeurs de propriétés suivantes :

  | Propriété | Valeur |
  |----------|-------|
  | **Type de clé** | RSA |
  | **Taille de clé RSA** | 2 048 |
  | **Activé** | Oui |
  |||

  ![Créez votre clé de chiffrement gérée par le client](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Pour plus d’informations, consultez [Configurer des clés gérées par le client avec Azure Key Vault](../storage/common/storage-encryption-keys-portal.md) ou la commande Azure PowerShell [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey).

* Un outil que vous pouvez utiliser pour créer votre ISE en appelant l’API REST Logic Apps avec une requête PUT HTTPS. Par exemple, vous pouvez utiliser [Postman](https://www.getpostman.com/downloads/) ou créer une application logique qui effectue cette tâche

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Créer un ISE avec coffre de clés et prise en charge des identités managées

Pour créer votre ISE en appelant l’API REST Logic Apps, émettez cette requête PUT HTTPS :

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Avec l’API REST version 2019-05-01 de Logic Apps, vous devez émettre votre propre requête PUT HTTP pour les connecteurs ISE.

Le déploiement prend généralement deux heures maximum. Parfois, le déploiement peut prendre jusqu’à quatre heures. Pour suivre l'état du déploiement, accédez au [portail Azure](https://portal.azure.com) et sélectionnez l'icône Notifications sur la barre d'outils Azure. Le volet Notifications s'ouvre alors.

> [!NOTE]
> Si le déploiement échoue ou si vous supprimez votre ISE, Azure peut prendre jusqu’à une heure avant de libérer vos sous-réseaux. Ce délai signifie que vous devrez peut-être attendre avant de réutiliser ces sous-réseaux dans un autre ISE.
>
> Si vous supprimez votre réseau virtuel, Azure a généralement besoin de jusqu'à deux heures pour libérer vos sous-réseaux, mais cette opération peut prendre plus longtemps. 
> Lors de la suppression de réseaux virtuels, assurez-vous qu’aucune ressource n’est restée connectée. 
> Consultez [Supprimer un réseau virtuel](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>En-tête de requête

Dans l’en-tête de la requête, incluez les propriétés suivantes :

* `Content-type`: affectez la valeur `application/json` à cette propriété.

* `Authorization`: affectez comme valeur de cette propriété le jeton du porteur pour le client qui a accès à l’abonnement Azure ou au groupe de ressources que vous souhaitez utiliser

### <a name="request-body"></a>Corps de la demande

Dans le corps de la requête, activez la prise en charge de ces éléments supplémentaires en spécifiant leurs informations dans votre définition d’ISE :

* L’identité managée affectée par le système que votre ISE utilise pour accéder à votre coffre de clés
* Votre coffre de clés et la clé gérée par le client que vous souhaitez utiliser

#### <a name="request-body-syntax"></a>Syntaxe du corps de la demande

Voici la syntaxe du corps de la requête, qui décrit les propriétés à utiliser lors de la création de votre ISE :

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Exemple de corps de requête

Cet exemple de corps de requête montre les exemples de valeurs :

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Accorder l’accès à votre coffre de clés

Dans les *30 minutes* qui suivent l’envoi de la requête PUT HTTP qui crée votre ISE, vous devez ajouter une stratégie d’accès à votre coffre de clés pour l’identité attribuée par le système de votre ISE. Si vous ne le faites pas, la création de votre ISE échoue et vous recevez une erreur d’autorisation. 

Pour cette tâche, vous pouvez utiliser la commande Azure PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) ou effectuer ces étapes dans le portail Azure :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre coffre de clés Azure.

1. Dans le menu de votre coffre de clés, sélectionnez **Stratégies d’accès** > **Ajouter une stratégie d’accès**, par exemple :

   ![Ajouter une stratégie d’accès pour une identité managée affectée par le système](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Une fois le volet **Ajouter une stratégie d’accès** ouvert, effectuez les étapes suivantes :

   1. Sélectionnez ces options :

      | Paramètre | Valeurs |
      |---------|--------|
      | **Configurer à partir du modèle (facultatif)** | Gestion de clés |
      | **Autorisations de clé** | - **Opérations de gestion des clés** : Obtenir, Lister <p><p>- **Opérations de chiffrement** : Ne pas inclure la clé, Inclure la clé |
      |||

      ![Sélectionnez « Gestion de clés » > « Autorisations de clé ».](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Pour **Sélectionner le principal**, sélectionnez **Aucune sélection**. Une fois le volet **Principal** ouvert, dans la zone de recherche, recherchez et sélectionnez votre ISE. Une fois ces opérations effectuées, choisissez **Sélectionner** > **Enregistrer**.

      ![Sélectionnez votre ISE à utiliser comme principal](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Quand vous en avez terminé avec le volet **Stratégies d’accès**, sélectionnez **Enregistrer**.

Pour plus d’informations, consultez [Fournir une authentification Key Vault avec une identité managée](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Azure Key Vault](../key-vault/key-vault-overview.md)