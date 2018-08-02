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
ms.openlocfilehash: e7652fe1b211e6811a4a3aa61bc2aa9d2f529dda
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205814"
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
     return this.CreateServiceRemotingInstanceListeners();
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
Il est utile d’en créer un manuellement lorsque vous devez remplacer les propriétés [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory).
La création de fabrique est une opération coûteuse. ServiceProxyFactory conserve le cache interne du client de communication.
Il est recommandé de mettre en cache ServiceProxyFactory aussi longtemps que possible.

## <a name="remoting-exception-handling"></a>Gestion des exceptions à distance
Toutes les exceptions à distance levées par l’API de service sont envoyées au client en tant que AggregateException. Les exceptions à distance doivent être des contrats de données sérialisables. Sinon, l’API du proxy lève [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) en indiquant l’erreur de sérialisation.

Le proxy de service gère toutes les exceptions de basculement pour la partition de service pour laquelle il a été créé. Il résout à nouveau les points de terminaison s’il existe des exceptions de basculement (exceptions non temporaires) et retente l’appel avec le point de terminaison correct. Le nombre de nouvelles tentatives pour des exceptions de basculement est indéfini.
En cas d’exceptions transitoires, le proxy retente l’appel.

Les paramètres de nouvelle tentative par défaut sont fournis par [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

L’utilisateur peut configurer ces valeurs en passant l’objet OperationRetrySettings au constructeur ServiceProxyFactory.

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
  [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
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

1. Procédez à la mise à niveau du service V1 vers le service V2 à l’aide de l’attribut ci-dessous.
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

    c) Ajoutez l’attribut d’assembly sur les interfaces de communication à distance pour utiliser V1, V2 Listener et le client V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Procédez à la mise à niveau de V1 Client vers V2 Client à l’aide de l’attribut V2 Client.
Cette étape permet de s’assurer de Client utilise la pile V2.
Aucune modification du projet/service Client n’est requise. La création de projets Client avec l’assembly de l’interface mise à jour est suffisante.

3. Cette étape est facultative. Utilisez l’attribut V2Listener, puis mettez à niveau vers le service V2.
Cette étape permet de s’assurer que le service écoute uniquement sur l’écouteur V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
```


## <a name="how-to-use-remoting-v2interfacecompatible-stack"></a>Utilisation de la pile Remoting V2(InterfaceCompatible)
 La pile Remoting V2(InterfaceCompatible aka V2_1) présente toutes les fonctionnalités de la pile Remoting V2. Son interface est compatible avec celle de la pile Remoting V1, mais elle n’est pas rétrocompatible avec V2 et V1. Pour effectuer la mise à niveau de V1 vers V2_1 sans affecter la disponibilité du service, suivez l’article ci-dessous, [Mise à niveau de V1 vers V2(InterfaceCompatible)](#how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible).


### <a name="using-assembly-attribute-to-use-remoting-v2interfacecompatible-stack"></a>Utilisation de l’attribut d’assembly pour se servir de la pile Remoting V2(InterfaceCompatible)

Voici les étapes à suivre pour modifier la pile V2_1.

1. Ajoutez une ressource de point de terminaison nommée « ServiceEndpointV2_1 » au manifeste de service.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2.  Utilisez la méthode d’extension de Remoting pour créer un écouteur de communication à distance.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Ajoutez [l’attribut d’assembly](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) sur les interfaces de communication à distance.

  ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

  ```
Aucune modification n’est requise dans le projet Client.
Générez l’assembly Client avec l’assembly de l’interface, pour faire en sorte que l’attribut d’assembly soit utilisé.

### <a name="using-explicit-remoting-classes-to-create-listener-clientfactory-for-v2interfacecompatible-version"></a>Utilisation de classes de communication à distance explicites afin de créer Listener/ClientFactory pour la version V2(InterfaceCompatible)
Voici la procédure à suivre :
1.  Ajoutez une ressource de point de terminaison nommée « ServiceEndpointV2_1 » au manifeste de service.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2. Utilisez [Remoting V2Listener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Le nom de la ressource de point de terminaison de service par défaut utilisé est « ServiceEndpointV2_1 » et doit être défini dans le manifeste de service.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
  ```

3. Utilisez V2 [Client Factory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible"></a>Mise à niveau de Remoting V1 vers Remoting V2(InterfaceCompatible)
Pour procéder à la mise à niveau de la version V1 vers V2(InterfaceCompatible aka V2_1), des mises à niveau en 2 étapes sont requises. Étapes suivantes à exécuter dans l’ordre indiqué.

1. Procédez à la mise à niveau du service V1 vers le service V2_1 à l’aide de l’attribut suivant.
Cette modification permet de s’assurer que le service écoute sur V1 et V2_1 Listener.

    a) Ajoutez une ressource de point de terminaison nommée « ServiceEndpointV2_1 » au manifeste de service.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
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

    c) Ajoutez l’attribut d’assembly sur les interfaces de communication à distance pour utiliser V1, V2_1 Listener et le client V2_1.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Procédez à la mise à niveau du client V1 vers le client V2_1 à l’aide de l’attribut de client V2_1.
Cette étape permet de s’assurer que le client utilise la pile V2_1.
Aucune modification du projet/service Client n’est requise. La création de projets Client avec l’assembly de l’interface mise à jour est suffisante.

3. Cette étape est facultative. Supprimez la version V1 Listener de l’attribut, puis procédez à la mise à niveau vers le service V2.
Cette étape permet de s’assurer que le service écoute uniquement sur l’écouteur V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
```

### <a name="how-to-use-custom-serialization-with-remoting-wrapped-message"></a>Utilisation de la sérialisation personnalisée avec Remoting Wrapped Message
Dans Remoting Wrapped Message, nous créons un objet encapsulé unique qui contient tous les paramètres sous forme de champ.
Voici la procédure à suivre :

1. Implémentez l’interface IServiceRemotingMessageSerializationProvider pour permettre l’implémentation de la sérialisation personnalisée.
    Voici l’extrait de code permettant de voir comment se présente une implémentation.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
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
