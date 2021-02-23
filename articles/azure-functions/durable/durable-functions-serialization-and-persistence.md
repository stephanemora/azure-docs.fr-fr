---
title: Persistance et sérialisation des données dans Durable Functions – Azure
description: Découvrez comment l’extension Durable Functions pour Azure Functions rend les données persistantes.
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 34845afe0efd0131e0c568eac1169ddc10be5d47
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520748"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Persistance et sérialisation des données dans Durable Functions (Azure Functions)

Durable Functions conserve automatiquement les paramètres des fonctions, les valeurs renvoyées et autres états dans un backend durable afin d’assurer une exécution fiable. Toutefois, le volume et la fréquence des données conservées dans un stockage durable peuvent avoir un impact sur les performances des applications et les coûts des transactions de stockage. Selon le type de données que votre application stocke, il peut être nécessaire de prendre en compte les stratégies de conservation des données et de confidentialité.

## <a name="azure-storage"></a>Stockage Azure

Par défaut, Durable Functions conserve les données dans des files d’attente, des tables et des blobs dans un compte [Stockage Azure](https://azure.microsoft.com/services/storage/) que vous spécifiez.

### <a name="queues"></a>Files d’attente

Durable Functions utilise les files d’attente Stockage Azure pour planifier de manière fiable toutes les exécutions de fonctions. Ces messages en file d’attente contiennent des entrées ou sorties de fonction, selon que le message est utilisé pour planifier une exécution ou retourner une valeur à un appel de fonction. Ces messages en file d’attente incluent également des métadonnées supplémentaires que Durable Functions utilise à des fins internes, telles que le routage et la corrélation de bout en bout. Une fois que la fonction a fini de s’exécuter en réponse à un message reçu, ce message est supprimé et le résultat de l’exécution peut également être conservé dans des tables Stockage Azure ou des blobs Stockage Azure.

Au sein d’un seul [hub de tâches](durable-functions-task-hubs.md), Durable Functions crée et ajoute des messages à une file d’attente d’*éléments de travail* nommée `<taskhub>-workitem` pour la planification des fonctions d’activité et à une ou plusieurs *files d’attente de contrôle* nommées `<taskhub>-control-##` pour planifier ou reprendre les fonctions d’orchestrateur et d’entité. Le nombre de files d’attente de contrôle est égal au nombre de partitions configurées pour votre application. Pour plus d’informations sur les files d’attente et les partitions, consultez la [documentation relative aux performances et la scalabilité](durable-functions-perf-and-scale.md).

### <a name="tables"></a>Tables

Une fois que les orchestrations traitent les messages avec succès, les enregistrements de leurs actions résultantes sont conservés dans la table d’*historique* nommée `<taskhub>History`. Les entrées d’orchestration, les sorties et les données d’état personnalisées sont également conservées dans la table d’*instances* nommée `<taskhub>Instances`.

### <a name="blobs"></a>Objets blob

Dans la plupart des cas, Durable Functions n’utilise pas de blobs Stockage Azure pour conserver les données. Toutefois, les files d’attente et les tables ont des [limites de taille](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits) qui peuvent empêcher Durable Functions de conserver toutes les données requises dans une ligne de stockage ou un message en file d’attente. Par exemple, lorsqu’une donnée devant être conservée dans une file d’attente est supérieure à 45 ko lorsqu’elle est sérialisée, Durable Functions compresse les données et les stocke dans un blob à la place. Lorsque des données persistantes sont ainsi stockées dans un blob, Durable Functions stocke une référence à ce blob dans la ligne de la table ou dans le message en file d’attente. Lorsque Durable Functions doit récupérer les données, il les récupère automatiquement à partir du blob. Ces blobs sont stockés dans le conteneur de blobs `<taskhub>-largemessages`.

> [!NOTE]
> Les étapes supplémentaires de compression et d’opération de blob pour les messages volumineux peuvent être coûteuses en termes de coûts de l’UC et de latence d’E/S. En outre, Durable Functions doit charger les données persistantes en mémoire et peut le faire pour de nombreuses exécutions de fonction différentes en même temps. En conséquence, la persistance des charges utiles de données volumineuses peut également entraîner une utilisation élevée de la mémoire. Pour réduire au minimum la surcharge de mémoire, envisagez de conserver les charges utiles de données volumineuses manuellement (par exemple, dans Stockage Blob) et de transmettre plutôt des références à ces données. De cette façon, votre code peut charger les données uniquement lorsque cela est nécessaire pour éviter les charges redondantes lors des [relectures des fonctions d’orchestrateur](durable-functions-orchestrations.md#reliability). Toutefois, le stockage des charges utiles sur le disque *n’est pas* recommandé, car la disponibilité de l’état sur disque n’est pas garantie, les fonctions pouvant s’exécuter sur différentes machines virtuelles tout au long de leur durée de vie.

### <a name="types-of-data-that-is-serialized-and-persisted"></a>Types de données sérialisées et rendues persistantes
La liste suivante répertorie les différents types de données qui seront sérialisées et rendues persistantes lors de l’utilisation des fonctionnalités de Durable Functions :

- Toutes les entrées et sorties des fonctions d’orchestrateur, d’activité et d’entité, y compris les ID et les exceptions non prises en charge
- Noms des fonctions d’orchestrateur, d’activité et d’entité
- Noms et charges utiles des événements externes
- Charges utiles de l’état de l’orchestration personnalisée
- Messages d’arrêt de l’orchestration
- Charges utiles du minuteur durable
- URL, en-têtes et charges utiles de requête HTTP et de réponse durables
- Appel d’entité et charges utiles de signal
- Charges utiles d’état d’entité

### <a name="working-with-sensitive-data"></a>Travailler avec des données sensibles
Lorsque vous utilisez Stockage Azure, toutes les données sont automatiquement chiffrées au repos. Toutefois, toute personne ayant accès au compte de stockage peut lire les données sous leur forme non chiffrée. Si vous avez besoin d’une protection renforcée pour les données sensibles, envisagez d’abord de chiffrer les données à l’aide de vos propres clés de chiffrement afin que Durable Functions conserve les données sous une forme pré-chiffrée.

Les utilisateurs .NET ont également la possibilité d’implémenter des fournisseurs de sérialisation personnalisés qui fournissent un chiffrement automatique. Vous trouverez un exemple de sérialisation personnalisée avec chiffrement dans [cet exemple GitHub](https://github.com/charleszipp/azure-durable-entities-encryption).

> [!NOTE]
> Si vous décidez d’implémenter le chiffrement au niveau de l’application, sachez que les orchestrations et les entités peuvent exister pendant une durée indéterminée. Cela est important lorsque vient le moment de permuter vos clés de chiffrement, car une orchestration ou des entités peuvent s’exécuter plus longtemps que votre stratégie de rotation des clés. Si une rotation de clés se produit, la clé utilisée pour chiffrer vos données peut ne plus être disponible pour les déchiffrer lors de la prochaine exécution de l’orchestration ou de l’entité. Le chiffrement des clients est donc recommandé uniquement lorsque des orchestrations et des entités sont censées s’exécuter pendant des périodes relativement courtes.

## <a name="customizing-serialization-and-deserialization"></a>Personnalisation de la sérialisation et de la désérialisation

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>Logique de sérialisation par défaut

Durable Functions utilise en interne [Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) pour sérialiser les données d’orchestration et d’entité au format JSON. Les paramètres par défaut que Durable Functions utilise pour Json.NET sont les suivants :

**Entrées, sorties et état :**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**Exceptions :**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

Vous trouverez [ici](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm) une documentation plus détaillée sur `JsonSerializerSettings`.

## <a name="customizing-serialization-with-net-attributes"></a>Personnalisation de la sérialisation avec des attributs .NET

Lors de la sérialisation de données, Json.NET recherche [divers attributs](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm) sur les classes et les propriétés qui contrôlent la façon dont les données sont sérialisées et désérialisées à partir du format JSON. Si vous possédez le code source du type de données transmis aux API de Durable Functions, pensez à ajouter ces attributs au type pour personnaliser la sérialisation et la désérialisation.

## <a name="customizing-serialization-with-dependency-injection"></a>Personnalisation de la sérialisation avec injection de dépendances

Les applications de fonction qui ciblent .NET et s’exécutent sur le runtime de Functions v3 peuvent utiliser l’[injection de dépendances](../functions-dotnet-dependency-injection.md) pour personnaliser la façon dont les données et les exceptions sont sérialisées. L’exemple de code ci-dessous montre comment utiliser l’injection de dépendances pour remplacer les paramètres de sérialisation Json.NET par défaut à l’aide des implémentations personnalisées des interfaces de service `IMessageSerializerSettingsFactory` et `IErrorSerializerSettingsFactory`.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>Logique de sérialisation et de désérialisation

Les applications Node d’Azure Functions utilisent [`JSON.stringify()` pour la sérialisation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) et [`JSON.Parse()` pour la désérialisation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse). La plupart des types doivent sérialiser et désérialiser de façon transparente. Dans les cas où la logique par défaut est insuffisante, la définition d’une méthode `toJSON()` sur l’objet détournera la logique de sérialisation. Toutefois, il n’existe pas de méthode analogue pour la désérialisation des objets.

Pour une personnalisation complète du pipeline de sérialisation/désérialisation, envisagez de gérer la sérialisation et la désérialisation avec votre propre code et de transmettre les données sous forme de chaînes.


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>Logique de sérialisation et de désérialisation

Il est fortement recommandé d’utiliser des annotations de type pour vous assurer que Durable Functions sérialise et désérialise vos données correctement. Si de nombreux types intégrés sont gérés automatiquement, certains types de données intégrés nécessitent des annotations de type pour préserver le type pendant la désérialisation.

Pour les types de données personnalisés, vous devez définir la sérialisation et la désérialisation JSON d’un type de données en exportant une méthode `to_json` et `from_json` statique à partir de votre classe.

---
