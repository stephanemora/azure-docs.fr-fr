---
title: Activer la rotation automatique des certificats dans un pool Batch
description: Vous pouvez créer un pool batch avec une identité managée et un certificat qui seront automatiquement renouvelés.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: e8bea49b2980deb8f20258ab7ea5619ece8cd2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962271"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Activer la rotation automatique des certificats dans un pool Batch

 Vous pouvez créer un pool Batch avec un certificat qui sera automatiquement renouvelé. Pour ce faire, votre pool doit être créé avec une [identité managée affectée par l’utilisateur](managed-identity-pools.md) qui aura accès au certificat dans [Azure Key Vault](../key-vault/general/overview.md).

> [!IMPORTANT]
> La prise en charge des pools Azure Batch avec des identités managées affectées par l’utilisateur est actuellement en préversion publique pour les régions suivantes : USA Ouest 2, USA Centre Sud, USA Est, US Gov Arizona et US Gov Virginie.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Créer une identité attribuée par l’utilisateur

Tout d’abord, [créez votre identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) dans le même locataire que votre compte Batch. Cette identité managée n’a pas besoin d’être dans le même groupe de ressources ou même dans le même abonnement.

Veillez à noter l’**ID client** de l’identité managée affectée par l’utilisateur. Vous aurez besoin de cette valeur ultérieurement.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Capture d’écran montrant l’ID client d’une identité managée affectée par l’utilisateur dans le Portail Azure.":::

## <a name="create-your-certificate"></a>Créer votre certificat

Vous devez ensuite créer un certificat et l’ajouter à Azure Key Vault. Si vous n’avez pas déjà créé un coffre de clés, vous devez d’abord le faire. Pour obtenir des instructions, consultez [Démarrage rapide : Définir et récupérer un certificat dans Azure Key Vault à l’aide du portail Azure](../key-vault/certificates/quick-create-portal.md).

Lors de la création de votre certificat, veillez à définir le **Type d’action de la durée de vie** sur le renouvellement automatique et spécifiez le nombre de jours après lequel le certificat doit être renouvelé.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Capture d’écran de l’écran de création de certificat dans le portail Azure.":::

Une fois votre certificat créé, notez son **Identificateur de secret**. Vous aurez besoin de cette valeur ultérieurement.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="Capture d’écran montrant l’identificateur de secret d’un certificat.":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Ajouter une stratégie d’accès dans Azure Key Vault

Dans votre coffre de clés, attribuez une stratégie d’accès Key Vault qui permet à votre identité managée affectée par l’utilisateur d’accéder aux secrets et aux certificats. Pour obtenir des instructions détaillées, consultez [Attribuer une stratégie d’accès Key Vault à l’aide du portail Azure](../key-vault/general/assign-access-policy-portal.md).

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>Créer un pool Batch avec une identité managée affectée par l’utilisateur

Créez un pool Batch avec votre identité managée à l’aide de la [bibliothèque de gestion Batch .NET](/dotnet/api/overview/azure/batch#management-library). Pour plus d’informations, consultez [Configurer des identités managées dans des pools Batch](managed-identity-pools.md).

L’exemple suivant utilise l’API REST de gestion Batch pour créer un pool. Veillez à utiliser l’**Identificateur de secret** de votre certificat pour `observedCertificates` et l'**ID client** de votre identité managée pour `msiClientId`, en remplaçant les données d’exemple ci-dessous.

URI de l’API REST

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

Corps de la requête

```json
{
    "name": "test2",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "KVExtensions",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>Valider le certificat

Pour vérifier que le certificat a bien été déployé, connectez-vous au nœud de calcul. Vous devez obtenir une sortie similaire à la suivante :

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).
- Découvrez comment utiliser des [clés gérées par le client avec des identités managées par l’utilisateur](batch-customer-managed-key.md).
