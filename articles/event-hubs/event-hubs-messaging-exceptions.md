---
title: Guide de dépannage - Azure Event Hubs | Microsoft Docs
description: Cet article fournit la liste des exceptions de messagerie Azure Event Hubs et les actions suggérées.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: de5b95bd10bf72f60ba5d63c4b3a799556fcce33
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76309776"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Guide de dépannage pour Azure Event Hubs
Cet article liste certaines des exceptions .NET générées par les API .NET Framework Event Hubs et fournit également d’autres conseils pour résoudre les problèmes. 

## <a name="event-hubs-messaging-exceptions---net"></a>Exceptions de messagerie Event Hubs - .NET
Cette section liste les exceptions .NET générées par les API .NET Framework. 

### <a name="exception-categories"></a>Catégories d'exceptions

Les API .NET Event Hubs génèrent des exceptions qui peuvent être classées dans les catégories suivantes, accompagnées de l’action associée que vous pouvez mettre en place pour les résoudre.

1. Erreur de codage utilisateur : [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Action générale : essayez de corriger le code avant de poursuivre.
2. Erreur d’installation ou de configuration : [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Action générale : révisez la configuration et modifiez-la si besoin.
3. Exceptions temporaires : [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Action générale : relancez l'opération ou avertissez les utilisateurs.
4. Autres exceptions : [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Action générale : propre au type d’exception. Reportez-vous au tableau de la section suivante. 

### <a name="exception-types"></a>Types d'exceptions
Le tableau suivant répertorie les types d'exceptions de la messagerie, leurs causes et les propositions d'actions que vous pouvez effectuer.

| Type d’exception | Description/Cause/Exemples | Action suggérée | Remarques sur la nouvelle tentative automatique/immédiate |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Le serveur n’a pas répondu à l’opération demandée dans le délai spécifié, qui est contrôlé par le paramètre [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Le serveur peut avoir terminé l'opération demandée. Cette exception peut être levée à cause des délais observés sur le réseau ou une autre infrastructure. |Vérifiez la cohérence de l'état du système et réessayez si nécessaire.<br /> Consultez [TimeoutException](#timeoutexception). | Dans certains cas, l'exécution d'une nouvelle tentative peut aider ; ajouter une logique de nouvelle tentative au code. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |L’opération utilisateur demandée n’est pas autorisée sur le serveur ou le service. Consultez le message de l'exception pour obtenir plus d'informations. Par exemple, le paramètre [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) génère cette exception si le message a été reçu en mode [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . | Vérifiez le code et consultez la documentation. Assurez-vous que l'opération demandée est valide. | Une nouvelle tentative est inutile. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Une tentative est effectuée pour appeler une opération sur un objet qui a déjà été fermé, abandonné ou supprimé. Dans de rares cas, la transaction ambiante est déjà supprimée. | Vérifiez le code et veillez à ce qu’il n’appelle pas d’opérations sur un objet supprimé. | Une nouvelle tentative est inutile. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | L’objet [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) n’a pas pu obtenir de jeton, le jeton n’est pas valide ou le jeton ne contient pas les revendications nécessaires pour exécuter l’opération. | Assurez-vous que le fournisseur de jetons est créé avec les valeurs correctes. Vérifiez la configuration du service ACS (Access Control Service). | Dans certains cas, l'exécution d'une nouvelle tentative peut aider ; ajouter une logique de nouvelle tentative au code. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Un ou plusieurs des arguments fournis à la méthode ne sont pas valides. L’URI fourni à [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Ceate](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contient un ou plusieurs segments de chemin d’accès. Le schéma d’URI fourni à [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Ceate](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) n’est pas valide. La valeur de la propriété est supérieure à 32 ko. | Vérifiez le code appelant et assurez-vous que les arguments sont corrects. | Une nouvelle tentative ne sera pas bénéfique. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | L'entité associée à l'opération n'existe pas ou a été supprimée. | Assurez-vous que l'entité existe. | Une nouvelle tentative ne sera pas bénéfique. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Le client ne peut pas établir de connexion à Event Hub. |Assurez-vous que le nom d'hôte fourni est correct et que l'hôte est accessible. | Une nouvelle tentative peut aider en cas de problèmes de connectivité intermittents. |
| [Microsoft.ServiceBus.Messaging ServerBusyException ](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Le service n'est pas en mesure de traiter la demande pour l'instant. | Le client peut attendre pendant une période de temps, puis recommencer l'opération. <br /> Consultez [ServerBusyException](#serverbusyexception). | Le client peut réessayer après un certain temps. Si une nouvelle tentative provoque une exception différente, vérifiez le comportement de nouvelle tentative de cette exception. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Exception de messagerie générique qui peut être levée dans les cas suivants : une tentative est effectuée pour créer un [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) à l’aide d’un nom ou d’un chemin d’accès qui appartient à un autre type d’entité (par exemple, une rubrique). Une tentative est effectuée pour envoyer un message de taille supérieure à 1 Mo. Le serveur ou le service a rencontré une erreur lors du traitement de la demande. Consultez le message de l'exception pour obtenir plus d'informations. Cette exception est généralement temporaire. | Vérifiez le code et assurez-vous que seuls les objets sérialisables sont utilisés dans le corps du message (ou utilisez un sérialiseur personnalisé). Consultez la documentation pour connaître les types de valeurs des propriétés pris en charge et utilisez uniquement les types pris en charge. Vérifiez la propriété [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Si sa valeur est **true**, vous pouvez réessayer d’effectuer l’opération. | Le comportement de la nouvelle tentative n'est pas défini et peut ne pas être utile. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Tentative de création d'une entité dont le nom est déjà utilisé par une autre entité de l'espace de noms de ce service. | Supprimez l'entité existante ou choisissez un autre nom pour l'entité à créer. | Une nouvelle tentative ne sera pas bénéfique. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | L'entité de messagerie a atteint sa taille maximale autorisée. Cette exception peut être levée si le nombre maximal de destinataires (c’est-à-dire 5) a déjà été ouvert sur un niveau de regroupement par consommateur. | Créez de l’espace dans l’entité en recevant des messages à partir de l’entité ou de ses files d’attente secondaires. <br /> Consultez [QuotaExceededException](#quotaexceededexception). | Une nouvelle tentative peut aider si des messages ont été supprimés entre-temps. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Demande d'une opération d'exécution sur une entité désactivée. |Activez l'entité. | Une nouvelle tentative peut aider si l'entité a été activée entre-temps. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Une charge utile de message dépasse la limite de 1 Mo. Cette limite de 1 Mo correspond à l’intégralité du message, qui peut inclure des propriétés système et une surcharge .NET. | Réduisez la taille de la charge utile de message, puis recommencez l'opération. |Une nouvelle tentative ne sera pas bénéfique. |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indique que le quota d’une entité spécifique a été dépassé.

Cette exception peut être levée si le nombre maximal de destinataires (5) a déjà été ouvert sur un niveau de regroupement par consommateur.

#### <a name="event-hubs"></a>Event Hubs
Event Hubs a une limite de 20 groupes de consommateurs par Event Hub. Lorsque vous essayez d’en créer plus, vous recevez une [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
Une [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indique qu’une opération lancée par l’utilisateur dépasse le délai d’expiration de l’opération. 

Pour Event Hubs, le délai d'attente est spécifié au sein de la chaîne de connexion ou par [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Le message d'erreur peut varier, mais il contient toujours la valeur du délai d'attente spécifiée pour l'opération en cours. 

#### <a name="common-causes"></a>Causes courantes
Il existe deux causes communes pour cette erreur : une configuration incorrecte ou une erreur de service temporaire.

1. **Configuration incorrecte** Le délai d’expiration de l’opération est peut-être trop court pour un bon fonctionnement. La valeur par défaut du délai d'expiration de l'opération dans le Kit de développement logiciel (SDK) client est de 60 secondes. Vérifiez si votre code définit une valeur trop petite. Selon la condition du réseau et l’utilisation du processeur, la réalisation d’une opération peut prendre plus ou moins de temps. Le délai d’expiration de l’opération ne doit donc pas être défini à une valeur trop basse.
2. **Erreur de service temporaire** Parfois, le service Event Hubs peut rencontrer des retards de traitement des requêtes, par exemple pendant les périodes de trafic élevé. Dans ce cas, vous pouvez réessayer l'opération après un certain délai, jusqu'à ce que l'opération réussisse. Si la même opération échoue encore après plusieurs tentatives, consultez le [Site d’état des services Azure](https://azure.microsoft.com/status/) pour voir s’il existe des interruptions de service connues.

### <a name="serverbusyexception"></a>ServerBusyException

Une exception [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) ou [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indique qu’un serveur est surchargé. Il existe deux codes d’erreur pertinents pour cette exception.

#### <a name="error-code-50002"></a>Code d’erreur 50002

Cette erreur peut se produire pour deux raisons :

1. La charge n’est pas répartie de manière égale entre toutes les partitions du hub d’événements et une partition atteint la limite d’unité de débit locale.
    
    Résolution : Passez en revue la stratégie de distribution de partition ou essayez [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient).

2. L’espace de noms Event Hubs n’a pas suffisamment d’unités de débit (pour le vérifier, consultez l’écran **Métriques** dans la fenêtre de l’espace de noms Event Hubs du [portail Azure](https://portal.azure.com)). Le portail affiche des informations agrégées (1 minute), mais nous mesurons le débit en temps réel. Cette valeur n’est donc qu’une estimation.

    Résolution : Augmentez les unités de débit sur l’espace de noms. Vous pouvez réaliser cette opération sur le portail, dans la fenêtre **Mise à l’échelle** de l’écran de l’espace de noms Event Hubs. Vous pouvez également utiliser la [majoration automatique](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>Code d’erreur 50001

Cette erreur survient rarement. Elle se présente lorsque le conteneur exécutant le code pour votre espace de noms n’a pas suffisamment de ressources d’UC ; pas plus de quelques secondes avant que l’équilibrage de charge des Event Hubs commence.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Limiter le nombre d’appels à la méthode GetRuntimeInformation
Azure Event Hubs prend en charge jusqu’à 50 appels par seconde à l’instance GetRuntimeInfo par seconde. Une fois la limite atteinte; vous pouvez recevoir une exception semblable à ce qui suit :

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problèmes de connectivité, de certificat ou de délai d’expiration
Aidez-vous des étapes suivantes pour résoudre les problèmes de connectivité, de certificat ou de délai d’expiration pour tous les services sous *.servicebus.windows.net. 

- Accédez à `https://<yournamespacename>.servicebus.windows.net/` ou utilisez [wget](https://www.gnu.org/software/wget/). Cet outil facilite les vérifications quand vous rencontrez des problèmes avec le filtrage des adresses IP, le réseau virtuel ou les chaînes de certificats (problème fréquent avec le SDK Java).

    Voici un exemple de message de réussite :
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Voici un exemple de message d’échec :

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Exécutez la commande suivante pour vérifier si un port est bloqué sur le pare-feu. Les ports utilisés sont 443 (HTTPS), 5671 (AMQP) et 9093 (Kafka). Selon la bibliothèque que vous utilisez, d’autres ports peuvent également être utilisés. Voici l’exemple de commande qui vérifie si le port 5671 est bloqué.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Sur Linux :

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Si vous constatez des problèmes de connectivité intermittents, exécutez la commande suivante pour détecter les paquets supprimés. Cette commande essaiera d’établir 25 connexions TCP différentes toutes les une seconde avec le service. Ensuite, vous pouvez vérifier le nombre d’entre elles ayant réussi ou échoué, ainsi que la latence de connexion TCP. Vous pouvez télécharger l’outil `psping` à partir d’[ici](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Vous pouvez utiliser des commandes équivalentes dans d’autres outils, par exemple `tnc`, `ping`, etc. 
- Si les étapes précédentes n’ont pas résolu le problème, obtenez une trace réseau et analysez-la à l’aide d’un outil tel que [Wireshark](https://www.wireshark.org/). Contactez le [support Microsoft](https://support.microsoft.com/) si nécessaire. 

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)
* [Créer un hub d’événements](event-hubs-create.md)
* [FAQ sur les hubs d’événements](event-hubs-faq.md)
