---
title: Configurer des identités managées dans des pools Batch
description: Découvrez comment activer des identités managées affectées par l’utilisateur sur des pools Batch et comment utiliser des identités managées dans les nœuds.
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: 243cf375b3ebf8bd64d73022ba44c3224b58872d
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110457767"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Configurer des identités managées dans des pools Batch

Les [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) évitent aux développeurs d’avoir à gérer les informations d’identification en fournissant une identité pour la ressource Azure dans Azure Active Directory (Azure AD) et en l’utilisant pour obtenir des jetons Azure Active Directory (Azure AD).

Cette rubrique explique comment activer des identités managées affectées par l’utilisateur sur des pools Batch et comment utiliser des identités managées dans les nœuds.

> [!IMPORTANT]
> Les pools doivent être configurés à l’aide de la [configuration de la machine virtuelle](nodes-and-pools.md#virtual-machine-configuration) afin d’utiliser des identités managées.
>
> La création de pools avec des identités managées est possible à l’aide de la [bibliothèque de gestion .NET Batch](/dotnet/api/overview/azure/batch#management-library), mais elle n’est actuellement pas prise en charge avec la [bibliothèque de client .NET Batch](/dotnet/api/overview/azure/batch#client-library).

## <a name="create-a-user-assigned-identity"></a>Créer une identité attribuée par l’utilisateur

Tout d’abord, [créez votre identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) dans le même locataire que votre compte Batch. Cette identité managée n’a pas besoin d’être dans le même groupe de ressources ou même dans le même abonnement.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>Créer un pool Batch avec des identités managées affectées par l’utilisateur

Une fois que vous avez créé une ou plusieurs identités managées affectées par l’utilisateur, vous pouvez créer un pool Batch avec cette identité managée à l’aide de la [bibliothèque de gestion .NET Batch](/dotnet/api/overview/azure/batch#management-library).

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        },
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Utiliser des identités managées affectées par l’utilisateur dans des nœuds Batch

Une fois que vous avez créé vos pools, vos identités managées affectées par l’utilisateur peuvent accéder aux nœuds des pools via Secure Shell (SSH) ou Bureau à distance (RDP). Vous pouvez également configurer vos tâches afin que les identités managées puissent accéder directement aux [ressources Azure qui prennent en charge les identités managées](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

Dans les nœuds Batch, vous pouvez obtenir des jetons d’identité managée et les utiliser pour vous authentifier par l’intermédiaire de l’authentification Azure AD via [Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md).

Pour Windows, le script PowerShell permettant d’obtenir un jeton d’accès pour l’authentification est le suivant :

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Pour Linux, le script Bash est le suivant :

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

Pour plus d’informations, consultez [Guide pratique de l’utilisation d’identités managées pour les ressources Azure sur une machine virtuelle Azure afin d’acquérir un jeton d’accès](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).
- Découvrez comment utiliser des [clés gérées par le client avec des identités managées par l’utilisateur](batch-customer-managed-key.md).
- Découvrez comment [activer la rotation automatique des certificats dans un pool Batch](automatic-certificate-rotation.md).
