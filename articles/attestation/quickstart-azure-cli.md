---
title: Configurer Azure Attestation avec Azure CLI
description: Comment installer et configurer un fournisseur d’attestation avec Azure CLI
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: dee9e7596c0a30301d9e0453ef22a6dfe9541522
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "96020940"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Démarrage rapide : Configurer Azure Attestation avec Azure CLI

Démarrez avec Azure Attestation en utilisant Azure CLI pour configurer une attestation.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>Bien démarrer

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

1. Exécutez la commande [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) pour créer un fournisseur d’attestation :

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   Le paramètre **--certs-input-path** spécifie un jeu de clés de signature approuvées. Si vous spécifiez un nom de fichier pour ce paramètre, le fournisseur d’attestation doit être configuré uniquement avec des stratégies au format JWT signé. Sinon, la stratégie peut être configurée dans du texte ou dans un format JWT non signé. Pour plus d’informations sur JWT, consultez [Concepts de base](basic-concepts.md). Pour voir des exemples de certificats, consultez [Exemples de certificat de signataire de stratégie d’attestation](policy-signer-examples.md).

1. Exécutez la commande [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) pour récupérer les propriétés du fournisseur d’attestation, comme les propriétés status et AttestURI :

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
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
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>Gestion des stratégies

Pour gérer les stratégies, un utilisateur Azure AD doit avoir les autorisations suivantes pour `Actions` :

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

Ces autorisations peuvent être attribuées à un utilisateur Azure AD par le biais d’un rôle tel que `Owner` (autorisations génériques), `Contributor` (autorisations génériques) ou `Attestation Contributor` (autorisations spécifiques pour Azure Attestation uniquement).  

Pour lire les stratégies, un utilisateur Azure AD doit avoir l’autorisation suivante pour `Actions` :

- `Microsoft.Attestation/attestationProviders/attestation/read`

Cette autorisation peut être attribuée à un utilisateur Azure AD par le biais d’un rôle tel que `Reader` (autorisations génériques) ou `Attestation Reader` (autorisations spécifiques pour Azure Attestation uniquement).

Utilisez les commandes décrites ici pour gérer les stratégies d’un fournisseur d’attestation, un environnement TEE à la fois.

La commande [az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) retourne la stratégie actuelle pour l’environnement TEE spécifié :

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> La commande affiche la stratégie à la fois au format texte et au format JWT.

Les types de TEE suivants sont pris en charge :

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

Utilisez la commande [az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) afin de définir une nouvelle stratégie pour l’environnement TEE spécifié.

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

La stratégie d’attestation au format JWT doit contenir une revendication nommée `AttestationPolicy`. Une stratégie signée doit l’être avec une clé correspondant à l’un des certificats de signataire de stratégie existants.

Pour obtenir des exemples de stratégies, consultez [Exemples de stratégies d’attestation](policy-examples.md).

La commande [az attestation policy reset](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) définit une nouvelle stratégie pour l’environnement TEE spécifié.

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>Gestion des certificats du signataire de stratégie

Utilisez les commandes suivantes afin de gérer les certificats de signataire de stratégie pour un fournisseur d’attestations :

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

Un certificat de signataire de stratégie est un jeton JWT signé avec une revendication nommée `maa-policyCertificate`. La valeur de la revendication est un objet JWK qui contient la clé de signature approuvée à ajouter. Le JWT doit être signé avec une clé privée correspondant à l’un des certificats de signataire de stratégie existants. Pour plus d’informations sur JWT et JWK, consultez [Concepts de base](basic-concepts.md).

Toute manipulation sémantique du certificat du signataire de stratégie doit être effectuée en dehors d’Azure CLI. En ce qui concerne Azure CLI, il s’agit d’une simple chaîne.

Pour voir des exemples de certificats, consultez [Exemples de certificat de signataire de stratégie d’attestation](policy-signer-examples.md).

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour créer et signer une stratégie d’attestation](author-sign-policy.md)
- [Implémenter une attestation avec une enclave SGX à l’aide d’exemples de code](/samples/browse/?expanded=azure&terms=attestation)
