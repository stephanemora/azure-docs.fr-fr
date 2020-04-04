---
title: Mettre à jour le niveau tarifaire d’une rubrique ou d’un domaine Azure Event Grid
description: Cet article explique comment mettre à jour le niveau tarifaire d’une rubrique ou d’un domaine Azure Event Grid (De base vers Premium, Premium vers De base) à l’aide du portail Azure, d’Azure CLI et d’Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300592"
---
# <a name="update-pricing-tier"></a>Mettre à jour un niveau tarifaire 
Cet article explique comment mettre à jour le niveau tarifaire d’une rubrique ou d’un domaine Azure Event Grid à l’aide du portail Azure, d’Azure CLI et d’Azure PowerShell. 

## <a name="use-azure-portal"></a>Utiliser le portail Azure
Cette section montre comment modifier le niveau tarifaire d’une rubrique ou d’un domaine dans le portail Azure. 

### <a name="overview-page"></a>Page Vue d’ensemble
Vous pouvez modifier le niveau tarifaire d’une rubrique ou d’un domaine dans la page **Vue d’ensemble**. L’exemple suivant montre comment faire monter une rubrique du niveau De base au niveau Premium. Les étapes pour descendre du niveau Premium au niveau De base sont similaires.

1. Dans le [portail Azure](https://portal.azure.com), accédez à la page de la rubrique ou du domaine. 
2. Sur la page **Vue d’ensemble**, sélectionnez le niveau tarifaire actuel (dans l’exemple suivant, **De base**).
    
    ![Sélectionner le niveau tarifaire actuel](./media/update-tier/select-tier.png)
3. Sur la page **Niveau tarifaire**, modifiez le niveau, puis sélectionnez **OK**. 

    ![Mettre à jour le niveau tarifaire](./media/update-tier/change-tier.png)
4. Vérifiez l’état de l’opération dans la page **Notifications**.

    ![État de la mise à jour](./media/update-tier/status.png)    
5. Vérifiez que le niveau mis à jour s’affiche sur la page **Vue d’ensemble**. 

    ![État de la mise à jour](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Page Mise en réseau
Vous pouvez **passer** du niveau De base au niveau Premium via la page **Mise en réseau**. En revanche, vous ne pouvez pas revenir du niveau Premium au niveau De base via cette page. 

1. Dans le [portail Azure](https://portal.azure.com), accédez à la page de la rubrique ou du domaine. 
2. Sur la page **Mise en réseau**, basculez vers l’onglet **Connexions des points de terminaison privés (préversion)** . 
3. Si le niveau tarifaire actuel est **De base**, le message suivant s’affiche. Sélectionnez-le. 

    ![Mettre à jour le niveau sur la page Connexions des points de terminaison privés](./media/update-tier/private-endpoint-connections-page.png)
4. Sur la page **Mettre à jour vers le niveau tarifaire Premium**, sélectionnez **Oui**. 
    
    ![Confirmer la mise à niveau](./media/update-tier/message-private-endpoint-connection.png)
5. Vérifiez l’état de l’opération dans la page **Notifications**.

    ![État de la mise à jour](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure
Cette section montre comment utiliser les commandes Azure CLI pour modifier le niveau tarifaire d’une rubrique ou d’un domaine. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Faire monter une rubrique du niveau De base au niveau Premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Faire descendre une rubrique du niveau Premium au niveau De base

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Faire monter un domaine du niveau De base au niveau Premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Faire descendre un domaine du niveau Premium au niveau De base

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell
Cette section montre comment utiliser des commandes PowerShell pour modifier le niveau tarifaire d’une rubrique ou d’un domaine. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Préparer un jeton et des en-têtes pour des appels d’API REST 
Exécutez les commandes préalables suivantes pour recevoir un jeton d’authentification à utiliser avec les appels d’API REST, ainsi que les informations d’en-tête et d’autorisation. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Faire monter une rubrique du niveau De base au niveau Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Faire descendre une rubrique du niveau Premium au niveau De base

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Faire monter un domaine du niveau De base au niveau Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Faire descendre un domaine du niveau Premium au niveau De base

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Étapes suivantes
Pour les rubriques et domaines de niveau Premium, vous pouvez configurer des points de terminaison privés pour restreindre l’accès à des réseaux virtuels sélectionnés. Pour obtenir des instructions pas à pas, consultez [Configurer des points de terminaison privés](configure-private-endpoints.md).
