---
title: Configurer Azure Attestation avec Azure CLI
description: Comment installer et configurer un fournisseur d’attestation avec Azure CLI
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: ae283785b4d4dc80c6b9b6c3997aaf82c9ff0f2f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178709"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Démarrage rapide : Configurer Azure Attestation avec Azure CLI

Démarrez avec [Azure Attestation en utilisant Azure CLI](/cli/azure/ext/attestation/attestation).

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="get-started"></a>Bien démarrer

1. Installer cette extension à l’aide de la commande CLI ci-dessous

   ```azurecli
   az extension add --name attestation
   ```
   
1. Vérifier la version

   ```azurecli
   az extension show --name attestation --query version
   ```

1. Utilisez la commande suivante pour vous connecter à Azure :

   ```azurecli
   az login
   ```

1. Si nécessaire, basculez vers l’abonnement pour Azure Attestation :

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Inscrivez le fournisseur de ressources Microsoft.Attestation dans l’abonnement avec la commande [az provider register](/cli/azure/provider#az_provider_register) :

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Pour plus d’informations sur les fournisseurs de ressources Azure, y compris sur leur configuration et leur gestion, consultez [Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > L’inscription à l’abonnement ne se fait qu’une seule fois pour le fournisseur de ressources.

1. Créez un groupe de ressources pour le fournisseur d’attestations. Vous pouvez placer d’autres ressources Azure dans ce même groupe de ressources, y compris une machine virtuelle avec une instance d’application cliente. Exécutez la commande [az group create](/cli/azure/group#az_group_create) pour créer un groupe de ressources ou utiliser un groupe de ressources existant :

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Créer et gérer un fournisseur d’attestations

Voici les commandes que vous pouvez utiliser pour créer et gérer le fournisseur d’attestation :

1. Exécutez la commande [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) pour créer un fournisseur d’attestation sans exigence de signature de stratégie :

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. Exécutez la commande [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) pour récupérer les propriétés du fournisseur d’attestation, comme les propriétés status et AttestURI :

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   Cette commande affiche des valeurs semblables aux suivantes :

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

Vous pouvez supprimer un fournisseur d’attestation à l’aide de la commande [az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) :

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>Gestion des stratégies

Utilisez les commandes décrites ici pour gérer les stratégies d’un fournisseur d’attestation, un type d’attestation à la fois.

La commande [az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) retourne la stratégie actuelle pour l’environnement TEE spécifié :

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> La commande affiche la stratégie à la fois au format texte et au format JWT.

Les types de TEE suivants sont pris en charge :

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

Utilisez la commande [az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) permettant de définir une nouvelle stratégie pour le type d’attestation spécifié.

Si vous souhaitez définir la stratégie au format texte pour un genre donné de type d’attestation à l’aide du chemin de fichier :

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

Pour définir la stratégie au format JWT pour un genre donné de type d’attestation à l’aide du chemin de fichier :

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK -f "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour créer et signer une stratégie d’attestation](author-sign-policy.md)
- [Implémenter une attestation avec une enclave SGX à l’aide d’exemples de code](/samples/browse/?expanded=azure&terms=attestation)
