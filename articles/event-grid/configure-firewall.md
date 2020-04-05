---
title: Configurer le pare-feu IP pour les rubriques ou domaines Azure Event Grid (version préliminaire)
description: Cet article explique comment configurer les paramètres de pare-feu pour les rubriques ou domaines Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300508"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>Configurer le pare-feu IP pour les rubriques ou domaines Azure Event Grid (version préliminaire)
Par défaut, la rubrique et le domaine sont accessibles sur Internet tant que la demande s’accompagne d’une authentification et d’une autorisation valides. Avec le pare-feu IP, vous pouvez les limiter à un ensemble d’adresses IPv4 ou de plages d’adresses IPv4 dans la notation [CIDR (Classless InterDomain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). Les serveurs de publication provenant de toute autre adresse IP seront rejetés et recevront une réponse 403 (interdit). Pour plus d’informations sur les fonctionnalités de sécurité réseau prises en charge par Event Grid, consultez [Sécurité réseau pour Event Grid](network-security.md).

Cet article explique comment configurer les paramètres de pare-feu IP pour les rubriques ou domaines Azure Event Grid.

## <a name="use-azure-portal"></a>Utiliser le portail Azure
Cette section montre comment utiliser le Portail Azure afin de créer des règles de pare-feu IP entrantes. Les étapes présentées dans cette section concernent les rubriques. Vous pouvez utiliser des étapes similaires pour créer des règles IP entrantes pour les **domaines**. 

1. Dans le [Portail Azure](https://portal.azure.com), accédez à la rubrique ou au domaine de votre grille d’événements, puis basculez vers l’onglet **Mise en réseau**.
2. Sélectionnez **Réseaux publics** pour autoriser l’ensemble du réseau, y compris Internet, à accéder à la ressource. 

    Vous pouvez limiter le trafic à l’aide de règles de pare-feu basées sur IP. Spécifiez une seule adresse IPv4 ou une plage d’adresses IP dans la notation CIDR (Classless Inter-Domain Routing). 

    ![Page des réseaux publics](./media/configure-firewall/public-networks-page.png)
3. Sélectionnez **Points de terminaison privés uniquement** pour autoriser uniquement les connexions de point de terminaison privées à accéder à cette ressource. Utilisez l’onglet **Connexions des points de terminaison privés** de cette page pour gérer les connexions. 

    ![Page des réseaux publics](./media/configure-firewall/private-endpoints-page.png)
4. Sélectionnez **Enregistrer** dans la barre d’outils. 



## <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure
Cette section vous montre comment utiliser les commandes Azure CLI pour créer des rubriques avec des règles IP entrantes. Les étapes présentées dans cette section concernent les rubriques. Vous pouvez utiliser des étapes similaires pour créer des règles IP entrantes pour les **domaines**. 


### <a name="enable-public-network-access-for-an-existing-topic"></a>Activer l’accès réseau public pour une rubrique existante
Par défaut, l’accès au réseau public est activé pour les rubriques et les domaines. Vous pouvez limiter le trafic en configurant des règles de pare-feu IP entrantes. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Désactiver l’accès au réseau public pour une rubrique existante
Lorsque l’accès au réseau public est désactivé pour une rubrique ou un domaine, le trafic sur l’Internet public n’est pas autorisé. Seules les connexions des points de terminaison privés sont autorisées à accéder à ces ressources. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>Créer une rubrique avec des règles IP entrantes
L’exemple de commande CLI suivant crée une rubrique Event Grid avec des règles IP entrantes en une seule étape. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>Créer d’abord la rubrique, puis ajouter des règles IP entrantes
Cet exemple crée d’abord une rubrique Event Grid, puis ajoute des règles IP entrantes pour la rubrique dans une commande distincte. Il met également à jour les règles IP entrantes qui ont été définies dans la deuxième commande. 

```azurecli-interactive

# create the event grid topic first
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\""}

# add inbound IP rules
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}

# later, update topic with additional ip rules or remove them. 
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""}, {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```


## <a name="use-powershell"></a>Utiliser PowerShell
Cette section vous montre comment utiliser les commandes Azure PowerShell pour créer des rubriques Azure Event Grid avec des règles de pare-feu IP entrantes. Les étapes présentées dans cette section concernent les rubriques. Vous pouvez utiliser des étapes similaires pour créer des règles IP entrantes pour les **domaines**. 

### <a name="prerequisite"></a>Configuration requise
Suivez les instructions de [Procédure : utiliser le portail pour créer une application Azure AD et un principal de service pouvant accéder aux ressources](../active-directory/develop/howto-create-service-principal-portal.md) pour créer une application Azure Active Directory et notez les valeurs suivantes :

- ID de l’annuaire (locataire)
- ID d’application (client)
- Secret de l’application (client)

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

### <a name="enable-public-network-access-for-an-existing-topic"></a>Activer l’accès réseau public pour une rubrique existante
Par défaut, l’accès au réseau public est activé pour les rubriques et les domaines. Vous pouvez limiter le trafic en configurant des règles de pare-feu IP entrantes. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Désactiver l’accès au réseau public pour une rubrique existante
Lorsque l’accès au réseau public est désactivé pour une rubrique ou un domaine, le trafic sur l’Internet public n’est pas autorisé. Seules les connexions des points de terminaison privés sont autorisées à accéder à ces ressources. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Créer une rubrique Event Grid avec des règles de trafic entrant en une seule étape

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Créer d’abord la rubrique Event Grid, puis ajouter des règles IP entrantes

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"}, @{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’information sur la surveillance des remises des événements, consultez [Surveiller la remise des messages Event Grid](monitor-event-delivery.md).
* Pour plus d’informations sur la clé d’authentification, consultez la page [Sécurité et authentification Azure Event Grid](security-authentication.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
