---
title: Fichier Include
description: Fichier Include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 01/22/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ddc0234accd9f434aad850d2404e2f3001c4bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742679"
---
## <a name="examples"></a>Exemples

### <a name="filter-on-system-properties"></a>Filtrer sur les propriétés système
Pour faire référence à une propriété système dans un filtre, utilisez le format suivant : `sys.<system-property-name>`. 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

### <a name="filter-on-message-properties"></a>Filtrer sur les propriétés de message
Voici les exemples d’utilisation des propriétés de message dans un filtre. Vous pouvez accéder aux propriétés de message à l’aide de `user.property-name` ou simplement `property-name`.

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>Filtrer sur les propriétés de message avec des caractères spéciaux
Si le nom de la propriété de message contient des caractères spéciaux, utilisez des guillemets doubles (`"`) pour encadrer le nom de la propriété. Par exemple, si le nom de la propriété est `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"`, utilisez la syntaxe suivante dans le filtre. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>Filtrer sur les propriétés de message avec des valeurs numériques
Les exemples suivants montrent comment utiliser des propriétés avec des valeurs numériques dans des filtres. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>Filtres basés sur des paramètres
Voici quelques exemples d’utilisation de filtres basés sur des paramètres. Dans ces exemples, `DataTimeMp` est une propriété de message de type `DateTime` et `@dtParam` est un paramètre transmis au filtre en tant qu’objet `DateTime`.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>Utilisation des clauses IN et NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Pour un exemple C#, consultez [Exemple de filtres de rubrique sur GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


### <a name="set-rule-action-for-a-sql-filter"></a>Définir l’action de la règle pour un filtre SQL

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="correlation-filter-using-correlationid"></a>Filtre de corrélation utilisant CorrelationID

```csharp
new CorrelationFilter("Contoso");
```

Il filtre les messages avec `CorrelationID` défini sur `Contoso`. 

### <a name="correlation-filter-using-system-and-user-properties"></a>Filtre de corrélation utilisant les propriétés system et user

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Il est équivalent à : `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

