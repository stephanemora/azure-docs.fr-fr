---
title: Prévention contre la perte de données
description: Les fonctionnalités de prévention contre la perte de données de Cognitive Services permettent aux clients de configurer la liste des URL sortantes auxquelles leurs ressources Cognitive Services sont autorisées à accéder. Cette configuration permet aux clients de bénéficier d'un niveau de contrôle supplémentaire pour prévenir la perte de données.
author: gclarkmt
ms.author: gregc
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/02/2021
ms.custom: template-concept
ms.openlocfilehash: c9a5ac391607249393d362f0e0b5b007989ea138
ms.sourcegitcommit: 9caa850a2b26773e238f8ba6f4ca151c47260915
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2021
ms.locfileid: "113601379"
---
# <a name="configure-data-loss-prevention-for-azure-cognitive-services"></a>Configurer la protection contre la perte de données pour Azure Cognitive Services

Les fonctionnalités de prévention contre la perte de données de Cognitive Services permettent aux clients de configurer la liste des URL sortantes auxquelles leurs ressources Cognitive Services sont autorisées à accéder. Cela permet aux clients de bénéficier d'un niveau de contrôle supplémentaire pour prévenir la perte de données. Dans cet article, nous allons vous présenter les étapes à suivre afin d'activer la fonctionnalité de prévention contre la perte de données pour les ressources Cognitive Services.

## <a name="prerequisites"></a>Prérequis

Pour adresser une requête, vous devez disposer d’un compte Azure et d’un abonnement Azure Cognitive Services. Si vous avez déjà un compte, passez à la section suivante. Si vous n’avez pas de compte, ce guide va vous aider à en créer un en quelques minutes : [Créer un compte Cognitive Services pour Azure](cognitive-services-apis-create-account.md).

Vous pouvez obtenir votre clé d’abonnement sur le [portail Azure](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) après la [création de votre compte](https://azure.microsoft.com/free/cognitive-services/).

## <a name="enabling-data-loss-prevention"></a>Activer la prévention contre la perte de données

L'activation de la prévention contre la perte de données se décompose en deux parties. Tout d'abord, la propriété restrictOutboundNetworkAccess doit être définie sur true. Une fois cette propriété définie sur true, vous devez également fournir la liste des URL approuvées. La liste des URL est ajoutée à la propriété allowedFqdnList. La propriété allowedFqdnList contient un tableau d'URL séparées par des virgules.

>[!NOTE]
>
> * La valeur de propriété `allowedFqdnList` prend en charge un maximum de 1 000 URL.
> * La propriété prend en charge les adresses IP et les noms de domaine complets, c'est-à-dire `www.microsoft.com`, les valeurs.
> * La prise en compte de la liste mise à jour peut prendre une quinzaine de minutes. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installez [Azure CLI](/cli/azure/install-azure-cli) et [connectez-vous](/cli/azure/authenticate-azure-cli) ou sélectionnez **Essayer**.

1. Consultez les détails de la ressource Cognitive Services.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
    ```

1. Consultez les propriétés actuelles de la ressource Cognitive Services.

    ```azurecli-interactive
    az rest -m get \
        -u /subscriptions/{subscription ID}}/resourceGroups/{resource group}/providers/Microsoft.CognitiveServices/accounts/{account name}?api-version=2021-04-30 \
    ```

1. Configurer la propriété restrictOutboundNetworkAccess et mettre à jour la propriété allowedFqdnList avec les URL approuvées

    ```azurecli-interactive
    az rest -m patch \
        -u /subscriptions/{subscription ID}}/resourceGroups/{resource group}/providers/Microsoft.CognitiveServices/accounts/{account name}?api-version=2021-04-30 \
        -b '{"properties": { "restrictOutboundNetworkAccess": true, "allowedFqdnList": [ "microsoft.com" ] }}'
    ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installez [Azure PowerShell](/powershell/azure/install-az-ps) et [connectez-vous](/powershell/azure/authenticate-azureps) ou sélectionnez **Essayer**.

1. Affichez les propriétés actuelles de la ressource Cognitive Services.

    ```azurepowershell-interactive
    $getParams = @{
        ResourceGroupName = 'myresourcegroup'
        ResourceProviderName = 'Microsoft.CognitiveServices'
        ResourceType = 'accounts'
        Name = 'myaccount'
        ApiVersion = '2021-04-30'
        Method = 'GET'
    }
    Invoke-AzRestMethod @getParams
    ```

1. Configurer la propriété restrictOutboundNetworkAccess et mettre à jour la propriété allowedFqdnList avec les URL approuvées

    ```azurepowershell-interactive
    $patchParams = @{
        ResourceGroupName = 'myresourcegroup'
        ResourceProviderName = 'Microsoft.CognitiveServices'
        ResourceType = 'accounts'
        Name = 'myaccount'
        ApiVersion = '2021-04-30'
        Payload = '{"properties": { "restrictOutboundNetworkAccess": true, "allowedFqdnList": [ "microsoft.com" ] }}'
        Method = 'PATCH'
    }
    Invoke-AzRestMethod @patchParams
    ```

---

## <a name="supported-services"></a>Services pris en charge

Les services suivants prennent en charge la configuration de la prévention contre la perte de données :

- Vision par ordinateur
- Content Moderator
- Vision personnalisée
- Face
- Form Recognizer
- Speech Service
- QnA Maker

## <a name="next-steps"></a>Étapes suivantes

- [Configurer des réseaux virtuels](cognitive-services-virtual-networks.md)
