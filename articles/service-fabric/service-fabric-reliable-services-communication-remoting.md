---
title: Communication à distance des services à l’aide de C# dans Service Fabric | Microsoft Docs
description: La communication à distance dans Service Fabric permet aux clients et aux services de communiquer avec les services C# en utilisant un appel de procédure distante.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 7afa50484c3ebf258bbdd2b7f16c9cd051710d28
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437890"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Communication à distance des services dans C# avec Reliable Services
> [!div class="op_single_selector"]
> * [C# sur Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java sur Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Pour les services qui ne sont pas liés à une pile ou un protocole de communication particulier, comme WebAPI, Windows Communication Foundation (WCF) ou autres, l’infrastructure Reliable Services fournit un mécanisme de communication à distance pour configurer rapidement et facilement des appels de procédure distante pour les services. Cet article explique comment configurer les appels de procédure distante pour les services écrits en C#.

## <a name="set-up-remoting-on-a-service"></a>Configuration de la communication à distance sur un service
La configuration de communication à distance pour un service s'effectue en deux étapes simples :

1. Créez une interface pour implémenter votre service. Cette interface définit les méthodes disponibles pour un appel de procédure distante sur votre service. Ces méthodes doivent être des méthodes asynchrones retournant des tâches. L'interface doit implémenter `Microsoft.ServiceFabric.Services.Remoting.IService` pour signaler que le service dispose d'une interface de communication à distance.
2. Utilisez un écouteur de communication à distance dans votre service. RemotingListener est une implémentation `ICommunicationListener` qui fournit des fonctionnalités de communication à distance. L’espace de noms `Microsoft.ServiceFabric.Services.Remoting.Runtime` contient une méthode d’extension `CreateServiceRemotingListener` pour les services avec et sans état qui peuvent être utilisés pour créer un écouteur de communication à distance à l’aide du protocole de transport de communication à distance par défaut.

>[!NOTE]
>L’espace de noms `Remoting` est disponible sous la forme d’un package NuGet distinct appelé `Microsoft.ServiceFabric.Services.Remoting`

Par exemple, le service sans état suivant expose une méthode unique pour obtenir « Hello World » sur un appel de procédure distante :

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context => this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Les arguments et les types de retour dans l’interface de service peuvent être de type simple, complexe ou personnalisé, mais ils doivent être sérialisables par l’élément [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).NET.
>
>

## <a name="call-remote-service-methods"></a>Méthodes d’appel de service distant
L’appel de méthodes sur un service à l’aide de la pile de communication à distance est effectué avec un proxy local pour le service via la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . La méthode `ServiceProxy` crée un proxy local à l’aide de l’interface implémentée par le service. Avec ce proxy, vous pouvez appeler à distance des méthodes sur l’interface.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

L'infrastructure de communication à distance propage les exceptions levées par le service au client. Par conséquent, lorsqu’il reçoit `ServiceProxy`, le client est responsable de la prise en charge des exceptions levées par le service.

## <a name="service-proxy-lifetime"></a>Durée de vie du proxy du service
Comme la création de proxy de service est une opération légère, vous pouvez en créer autant de fois que nécessaire. Les instances de proxy de service peuvent être réutilisées tant qu’elles sont nécessaires. Si un appel de procédure distante lève une exception, vous pouvez toujours réutiliser la même instance de proxy. Chaque proxy de service contient un client de communication qui permet d’envoyer des messages sur le réseau. Lors de l’invocation d’appels distants, des vérifications en interne sont effectuées pour déterminer si le client de communication est valide. Selon les résultats de ces vérifications, le client de communication est recréé, si nécessaire. Par conséquent, si une exception se produit, il est inutile de recréer `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>Durée de vie de la fabrique ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) est une fabrique qui crée des instances de proxy pour différentes interfaces de communication à distance. Si vous utilisez l’API `ServiceProxy.Create` pour créer un proxy, le framework crée un singleton ServiceProxy.
Il est utile d’en créer un manuellement lorsque vous devez remplacer les propriétés [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory).
La création de fabrique est une opération coûteuse. ServiceProxyFactory conserve le cache interne du client de communication.
Il est recommandé de mettre en cache ServiceProxyFactory aussi longtemps que possible.

## <a name="remoting-exception-handling"></a>Gestion des exceptions à distance
Toutes les exceptions à distance levées par l’API de service sont envoyées au client en tant que AggregateException. Les exceptions à distance doivent être des contrats de données sérialisables. Sinon, l’API du proxy lève [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) en indiquant l’erreur de sérialisation.

Le proxy de service gère toutes les exceptions de basculement pour la partition de service pour laquelle il a été créé. Il résout à nouveau les points de terminaison s’il existe des exceptions de basculement (exceptions non temporaires) et retente l’appel avec le point de terminaison correct. Le nombre de nouvelles tentatives pour des exceptions de basculement est indéfini.
En cas d’exceptions transitoires, le proxy retente l’appel.

Les paramètres de nouvelle tentative par défaut sont fournis par [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Vous pouvez configurer ces valeurs en passant l’objet OperationRetrySettings au constructeur ServiceProxyFactory.

## <a name="how-to-use-the-remoting-v2-stack"></a>Utilisation de la pile Remoting V2

À partir du package NuGet Remoting version 2.8, vous avez la possibilité d’utiliser la pile Remoting V2. La pile Remoting V2 est plus performante et fournit des fonctionnalités telles que des API de sérialisation personnalisées et plus enfichables.
Le code du modèle continue d’utiliser la pile de Remoting V1.
Remoting V2 n’est pas compatible avec V1 (pile Remoting précédente). Par conséquent, suivez les instructions ci-dessous sur [la mise à niveau de la version V1 vers V2](#how-to-upgrade-from-remoting-v1-to-remoting-v2) sans affecter la disponibilité du service.

Les approches suivantes sont disponibles pour activer la pile V2.

### <a name="using-an-assembly-attribute-to-use-the-v2-stack"></a>Utilisation d’un attribut d’assembly pour utiliser la pile V2

Ces étapes modifient le code du modèle pour utiliser la pile V2 avec un attribut d’assembly.

1. Modifiez la ressource de point de terminaison de `"ServiceEndpoint"` à `"ServiceEndpointV2"` dans le manifeste de service.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Utilisez la méthode d'extension `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` pour créer des écouteurs de la communication à distance (identiques pour V1 et V2).

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3. Marquez l’assembly qui contient les interfaces de la communication à distance avec un attribut `FabricTransportServiceRemotingProvider`.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```

Aucune modification du code n’est requise dans le projet client.
Générez l’assembly client avec l’assembly de l’interface, pour faire en sorte que l’attribut d’assembly illustré ci-dessus soit utilisé.

### <a name="using-explicit-v2-classes-to-use-the-v2-stack"></a>Utilisation de classes V2 explicites pour utiliser la pile V2

Au lieu d’utiliser un attribut d’assembly, la pile V2 peut également être activée à l’aide de classes V2 explicites.

Ces étapes modifient le code du modèle pour utiliser la pile V2 avec des classes V2 explicites.

1. Modifiez la ressource de point de terminaison de `"ServiceEndpoint"` à `"ServiceEndpointV2"` dans le manifeste de service.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Utilisez [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) à partir de l’espace de noms `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime`.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. Utilisez [FabricTransportServiceRemotingClientFactory ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) à partir de l’espace de noms `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` pour créer des clients.

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Mise à niveau de Remoting V1 vers Remoting V2.
Pour procéder à la mise à niveau de la version V1 vers V2, des mises à niveau en 2 étapes sont requises. Étapes suivantes à exécuter dans l’ordre indiqué.

1. Procédez à la mise à niveau de V1 Service vers V2 Service à l’aide de l’attribut CompactListener.
Cette modification permet de s’assurer que le service écoute sur V1 et V2 Listener.

    a)  Ajoutez une ressource de point de terminaison nommée « ServiceEndpointV2 » au manifeste du service.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b)  Utilisez la méthode d’extension suivante pour créer un écouteur de communication à distance.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) Ajoutez l’attribut d’Assembly sur les Interfaces de communication à distance pour utiliser CompatListener et V2 Client.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Procédez à la mise à niveau de V1 Client vers V2 Client à l’aide de l’attribut V2 Client.
Cette étape permet de s’assurer de Client utilise la pile V2.
Aucune modification du projet/service Client n’est requise. La création de projets Client avec l’assembly de l’interface mise à jour est suffisante.

3. Cette étape est facultative. Utilisez l’attribut V2Listener, puis mettez à niveau vers le service V2.
Cette étape permet de s’assurer que le service écoute uniquement sur l’écouteur V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Comment utiliser la sérialisation personnalisée avec Remoting V2.
L’exemple suivant utilise la sérialisation Json avec Remoting V2.
1. Implémentez l’interface IServiceRemotingMessageSerializationProvider pour permettre l’implémentation de la sérialisation personnalisée.
    Voici l’extrait de code permettant de voir comment se présente une implémentation.

 ```csharp
    public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
    {
        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> requestBodyTypes)
        {
            return new ServiceRemotingRequestJsonMessageBodySerializer(serviceInterfaceType, requestBodyTypes);
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> responseBodyTypes)
        {
            return new ServiceRemotingResponseJsonMessageBodySerializer(serviceInterfaceType, responseBodyTypes);
        }

        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
            return new JsonMessageFactory();
        }
    }

    class JsonMessageFactory : IServiceRemotingMessageBodyFactory
    {
        public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
            int numberOfParameters)
        {
            return new JsonRemotingRequestBody();
        }

        public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
        {
            return new JsonRemotingResponseBody();
        }
    }

    class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
    {
        public ServiceRemotingRequestJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
        {
            if (serviceRemotingRequestMessageBody == null)
            {
                return null;
            }

            var writeStream = new MemoryStream();
            var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream));

            var serializer = JsonSerializer.Create(new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);

            jsonWriter.Flush();
            var segment = new ArraySegment<byte>(writeStream.ToArray());
            var segments = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(segments);
        }

        public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (JsonReader reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingRequestBody>(reader);
            }
        }
    }

    class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
    {
        public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
        {
            var json = JsonConvert.SerializeObject(responseMessageBody, new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            var bytes = Encoding.UTF8.GetBytes(json);
            var segment = new ArraySegment<byte>(bytes);
            var list = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(list);
        }

        public IServiceRemotingResponseMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (var reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingResponseBody>(reader);
            }
        }
    }

    internal class JsonRemotingResponseBody : IServiceRemotingResponseMessageBody
    {
        public object Value;

        public void Set(object response)
        {
            this.Value = response;
        }

        public object Get(Type paramType)
        {
            return this.Value;
        }
    }

    class JsonRemotingRequestBody : IServiceRemotingRequestMessageBody
    {
        public readonly Dictionary<string, object> parameters = new Dictionary<string, object>();        

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.parameters[parameName] = parameter;
        }

        public object GetParameter(int position, string parameName, Type paramType)
        {
            return this.parameters[parameName];
        }
    }
 ```

2.    Remplacez le fournisseur de sérialisation par défaut par JsonSerializationProvider pour Remoting Listener.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    Remplacez le fournisseur de sérialisation par défaut par JsonSerializationProvider pour Remoting Client Factory.

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>Étapes suivantes
* [API Web avec OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Communication WCF avec Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Sécurisation des communications pour Reliable Services](service-fabric-reliable-services-secure-communication.md)

