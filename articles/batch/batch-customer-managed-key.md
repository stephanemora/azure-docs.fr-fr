---
title: Configurer des clés managées par le client pour votre compte Azure Batch avec Azure Key Vault et l’identité managée
description: En savoir plus sur le chiffrement des données Batch à l’aide de clés gérées par le client.
author: pkshultz
ms.topic: how-to
ms.date: 02/11/2021
ms.author: peshultz
ms.openlocfilehash: 36883489e1a9a50406ad1b1bde78d2f25ee227b7
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989321"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Configurer des clés managées par le client pour votre compte Azure Batch avec Azure Key Vault et l’identité managée

Par défaut Azure Batch utilise des clés managées par la plate-forme pour chiffrer toutes les données client stockées au sein d’Azure Batch Service, telles que les certificats ou les métadonnées de travail/tâche. Si vous le souhaitez, vous pouvez utiliser vos propres clés, c’est-à-dire des clés managées par le client, pour chiffrer les données stockées dans Azure Batch.

Les clés que vous fournissez doivent être générées dans [Azure Key Vault](../key-vault/general/basic-concepts.md), et elles doivent être accessibles avec des [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

Il existe deux types d’identités managées : [*celles affectées par le système* et *celles affectées par l’utilisateur*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

Vous pouvez soit créer votre compte Batch avec une identité managée affectée par le système, soit créer une identité managée distincte affectée par l’utilisateur qui aura accès aux clés gérées par le client. Consultez le [tableau comparatif](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) pour comprendre les différences et déterminer l’option qui convient le mieux à votre solution. Par exemple, si vous souhaitez utiliser la même identité managée pour accéder à plusieurs ressources Azure, vous aurez besoin d’une identité managée affectée par l’utilisateur. Sinon, une identité managée affectée par le système associée à votre compte Batch peut suffire. L’utilisation d’une identité managée affectée par l’utilisateur vous donne également la possibilité d’appliquer des clés gérées par le client au moment de la création du compte Batch, comme indiqué [dans l’exemple ci-dessous](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Créer un compte Batch avec une identité managée affectée par le système

Si vous n’avez pas besoin d’une identité managée distincte affectée par l’utilisateur, vous pouvez activer l’identité managée affectée par le système lorsque vous créez votre compte Batch.

### <a name="azure-portal"></a>Portail Azure

Dans le [Portail Azure](https://portal.azure.com/), lorsque vous créez des comptes Batch, sélectionnez **Système affecté** dans le type d’identité sous l’onglet **Avancé**.

![Capture d’écran d’un nouveau compte Batch avec le type d’identité affecté par le système.](./media/batch-customer-managed-key/create-batch-account.png)

Une fois le compte créé, vous trouverez un GUID unique dans le champ **ID du principal d’identité** dans la section **Propriétés**. Le **type d’identité** affichera `System assigned`.

![Capture d’écran montrant un GUID unique dans le champ ID de principal d’identité.](./media/batch-customer-managed-key/linked-batch-principal.png)

Vous aurez besoin de cette valeur pour permettre à ce compte Batch d’accéder au coffre de clés.

### <a name="azure-cli"></a>Azure CLI

Lorsque vous créez un nouveau compte Batch, précisez `SystemAssigned` pour le paramètre `--identity`.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

Une fois le compte créé, vous pouvez vérifier que l’identité managée affectée par le système a été activée sur ce compte. Veillez à noter le `PrincipalId`, car cette valeur sera nécessaire pour accorder à ce compte Batch l’accès au coffre de clés.

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> L’identité managée affectée par le système créée dans un compte Batch est utilisée uniquement pour récupérer des clés gérées par le client dans le coffre de clés. Cette identité n’est pas disponible sur les pools Batch. Pour utiliser une identité managée affectée par l’utilisateur dans un pool, consultez [Configurer des identités managées dans des pools Batch](managed-identity-pools.md).

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur

Si vous préférez, vous pouvez [créer une identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) qui peut être utilisée pour accéder à vos clés gérées par le client.

Vous aurez besoin de la valeur **ID client** de cette identité pour qu’elle puisse accéder au coffre de clés.

## <a name="configure-your-azure-key-vault-instance"></a>Configurer votre instance Azure Key Vault

L’instance d’Azure Key Vault dans laquelle vos clés seront générées doit être créée dans le même locataire que votre compte Batch. Elle n’a pas besoin d’être dans le même groupe de ressources ou même dans le même abonnement.

### <a name="create-an-azure-key-vault"></a>Créer un Azure Key Vault

Lors de la [création d’une instance d’Azure Key Vault](../key-vault/general/quick-create-portal.md) avec des clés gérées par le client pour Azure Batch, assurez-vous que la **suppression réversible** et la **protection contre le vidage** sont activées.

![Capture d’écran de l’écran de création du coffre de clés.](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Ajouter une stratégie d’accès à votre instance Azure Key Vault

Dans le Portail Azure, une fois Key Vault créé, ajoutez l’accès au compte Batch à l’aide de l’identité managée dans la **stratégie d’accès** sous **Paramètres**. Sous **Autorisations de clé**, sélectionnez **Obtenir**, **Inclure la clé** et **Ne pas inclure la clé**.

![Capture d’écran montrant l’écran Ajouter une stratégie d’accès.](./media/batch-customer-managed-key/key-permissions.png)

Dans le champ **Sélectionner** sous **Principal**, indiquez l’un des éléments suivants :

- Pour une identité managée affectée par le système : Entrez le `principalId` que vous avez récupéré précédemment ou le nom du compte Batch.
- Pour une identité managée affectée par l’utilisateur : Entrez l’**ID client** que vous avez récupéré précédemment ou le nom de l’identité managée affectée par l’utilisateur.

![Capture d’écran de l’écran Principal.](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Générer une clé dans Azure Key Vault

Dans le Portail Azure, accédez à l’instance Key Vault dans la section **Clé**, puis **sélectionnez Générer/importer**. Choisissez le **type de clé** `RSA` et une **taille de clé RSA** d’au moins `2048` bits. Les types de clés `EC` ne sont actuellement pas pris en charge en tant que clé gérée par le client sur un compte Batch.

![Créer une clé](./media/batch-customer-managed-key/create-key.png)

Une fois que la clé a été créée, cliquez sur la nouvelle clé et la version actuelle, copiez l’**identificateur de clé** dans la section **Propriétés**.  Assurez-vous que sous **Opérations autorisées**, les options **Inclure la clé** et **Ne pas inclure la clé** sont activées.

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>Activer des clés gérées par le client sur un compte Batch

Une fois que vous avez suivi les étapes ci-dessus, vous pouvez activer les clés gérées par le client sur votre compte Batch.

### <a name="azure-portal"></a>Portail Azure

Dans le [Portail Azure](https://portal.azure.com/), accédez à la page compte Batch. Dans la section **Chiffrement**, activez **Clé managée par le client**. Vous pouvez utiliser directement l’identificateur de clé, ou sélectionner le coffre de clés, puis cliquer sur **Sélectionner un coffre de clés et une clé**.

![Capture d’écran montrant la section Chiffrement et l’option permettant d’activer la clé gérée par le client](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

Une fois que le compte Batch a été créé avec une identité managée affectée par le système et que l’accès à Key Vault a été accordé, mettez à jour le compte Batch avec l’URL `{Key Identifier}` sous le paramètre `keyVaultProperties`. Définissez également **encryption_key_source** sur `Microsoft.KeyVault`.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>Créer un compte Batch avec une identité managée affectée par l’utilisateur et des clés gérées par le client

En utilisant le client .NET de gestion Batch, vous pouvez créer un compte Batch qui aura une identité managée affectée par l’utilisateur et des clés gérées par le client.

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>Mettez à jour la version de la clé managée par le client

Lors de la création d’une nouvelle version d’une clé, mettez à jour le compte Batch afin qu’il utilise cette nouvelle version. Procédez comme suit :

1. Accédez à votre compte Batch dans le Portail Azure et affichez les paramètres de chiffrement.
2. Saisissez l’URI de la nouvelle version de clé. Vous pouvez également sélectionner à nouveau le coffre de clés et la clé pour mettre à jour la version.
3. Enregistrez vos modifications.

Vous pouvez également utiliser Azure CLI pour mettre à jour la version.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>Utiliser une clé différente pour le chiffrement Batch

Pour modifier la clé utilisée pour le chiffrement Batch, procédez comme suit :

1. Accédez à votre compte Batch et affichez les paramètres de chiffrement.
2. Entrez l’URI de la nouvelle clé. Vous pouvez également sélectionner le coffre de clés et choisir une nouvelle clé.
3. Enregistrez vos modifications.

Vous pouvez également utiliser Azure CLI pour vous servir d’une clé différente.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>Forum aux questions

- **Les clés managées par le client sont-elles prises en charge pour les comptes Batch existants ?** Non. Les clés managées par le client sont uniquement prises en charge pour les nouveaux comptes Batch.
- **Puis-je sélectionner des tailles de clé RSA supérieures à 2048 bits ?** Oui, les tailles de clé RSA de `3072` et `4096` bits sont également prises en charge.
- **Quelles sont les opérations disponibles après la révocation d’une clé managée par le client ?** La seule opération autorisée est la suppression de compte si Batch perd l’accès à la clé managée par le client.
- **Comment puis-je restaurer l’accès à mon compte Batch si je supprime accidentellement la clé de Key Vault ?** Étant donné que la protection de purge et la suppression réversible sont activées, vous pouvez restaurer les clés existantes. Pour plus d’informations, consultez [Restaurer un Azure Key Vault](../key-vault/general/key-vault-recovery.md).
- **Puis-je désactiver les clés managées par le client ?** Vous pouvez à tout moment définir le type de chiffrement du compte Batch sur « clé managée par Microsoft ». Ensuite, vous êtes libre de supprimer ou de modifier la clé.
- **Comment faire pivoter mes clés ?** Les clés managées par le client ne pivotent pas automatiquement. Pour faire pivoter la clé, mettez à jour l’identificateur de clé auquel le compte est associé.
- **Après la restauration de l’accès, combien de temps faut-il pour que le compte Batch fonctionne à nouveau ?** Cela peut prendre jusqu’à 10 minutes pour que le compte soit à nouveau accessible après la restauration.
- **Qu’arrive-t-il à mes ressources lorsque le compte Batch est indisponible ?** Les pools en cours d’exécution continuent à s’exécuter lorsque l’accès Batch aux clés managées par le client est perdu. Toutefois, les nœuds passent à l’état indisponible et les tâches cessent de s’exécuter (elles sont replacées dans la file d’attente). Une fois l’accès restauré, les nœuds sont à nouveau disponibles et les tâches redémarrent.
- **Ce mécanisme de chiffrement s’applique-t-il aux disques de machine virtuelle dans un pool Batch ?** Non. Pour les pools de configuration d’Azure Cloud Services (qui sont [déconseillés](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)), aucun chiffrement n’est appliqué pour le système d’exploitation et le disque temporaire. Pour les pools de configuration de machine virtuelle, le système d’exploitation et tous les disques de données spécifiés sont chiffrés par défaut avec une clé gérée par la plateforme Microsoft. Actuellement, vous ne pouvez pas spécifier votre propre clé pour ces disques. Pour chiffrer le disque temporaire des machines virtuelles pour un pool Batch avec une clé managée par la plate-forme Microsoft, vous devez activer la propriété [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) dans votre pool de [configuration de machine virtuelle](/rest/api/batchservice/pool/add#virtualmachineconfiguration). Pour les environnements très sensibles, nous vous recommandons d’activer le chiffrement de disque temporaire et d’éviter de stocker des données sensibles sur le système d’exploitation et les disques de données. Pour plus d’informations, consultez [Créer un pool avec le chiffrement de disque activé](./disk-encryption.md).
- **L’identité managée affectée par le système sur le compte Batch est-elle disponible sur les nœuds de calcul ?** Non. L’identité managée affectée par le système est actuellement utilisée uniquement pour accéder au coffre de clés Azure pour la clé gérée par le client. Pour utiliser une identité managée affectée par l’utilisateur sur des nœuds de calcul, consultez [Configurer des identités managées dans des pools Batch](managed-identity-pools.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [meilleures pratiques en matière de sécurité dans Azure Batch](security-best-practices.md).
- En savoir plus sur [Azure Key Vault](../key-vault/general/basic-concepts.md).
