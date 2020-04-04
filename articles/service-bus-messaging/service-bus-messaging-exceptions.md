---
title: Guide de dépannage pour Azure Service Bus | Microsoft Docs
description: Cet article fournit la liste des exceptions de messagerie Azure Service Bus et les actions suggérées à entreprendre quand une exception se produit.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: fb27befadcf8e6d201d020e758cfd1ef9b695f41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240804"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Guide de dépannage pour Azure Service Bus
Cet article indique certaines des exceptions .NET générées par les API .NET Framework Service Bus et fournit également d’autres conseils pour la résolution des problèmes. 

## <a name="service-bus-messaging-exceptions"></a>Exceptions de la messagerie Service Bus
Cette section liste les exceptions .NET générées par les API .NET Framework. 

### <a name="exception-categories"></a>Catégories d'exceptions
Les API de messagerie génèrent des exceptions qui peuvent être classées dans les catégories suivantes, ainsi que l'action associée que vous pouvez mener pour essayer de les résoudre. La signification et les causes d’une exception peuvent varier en fonction du type d’entité de messagerie :

1. Erreur de codage utilisateur ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Action générale : essayez de corriger le code avant de poursuivre.
2. Erreur d’installation ou de configuration ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Action générale : révisez la configuration et modifiez-la si besoin.
3. Exceptions temporaires ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Action générale : relancez l'opération ou avertissez les utilisateurs. La classe `RetryPolicy` du SDK client peut être configurée pour traiter automatiquement les nouvelles tentatives. Pour plus d’informations, consultez les [conseils sur les nouvelles tentatives](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Autres exceptions ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Action générale : propre au type d’exception. Reportez-vous au tableau de la section suivante : 

### <a name="exception-types"></a>Types d'exceptions
Le tableau suivant répertorie les types d'exceptions de la messagerie, leurs causes et les propositions d'actions que vous pouvez effectuer.

| **Type d'exception** | **Description/Cause/Exemples** | **Action suggérée** | **Remarques sur la nouvelle tentative automatique/immédiate** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Le serveur n’a pas répondu à l’opération demandée dans le délai spécifié, qui est contrôlé par le paramètre [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Le serveur peut avoir terminé l'opération demandée. Cela peut se produire en raison des délais observés sur le réseau ou une autre infrastructure. |Vérifiez la cohérence de l'état du système et réessayez si nécessaire. Consultez [Exceptions au délai d’expiration](#timeoutexception). |Dans certains cas, l'exécution d'une nouvelle tentative peut aider ; ajouter une logique de nouvelle tentative au code. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |L’opération utilisateur demandée n’est pas autorisée sur le serveur ou le service. Consultez le message de l'exception pour obtenir plus d'informations. Par exemple, le paramètre [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) génère cette exception si le message a été reçu en mode [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode). |Vérifiez le code et consultez la documentation. Assurez-vous que l'opération demandée est valide. |La nouvelle tentative ne résout pas le problème. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Une tentative est effectuée pour appeler une opération sur un objet qui a déjà été fermé, abandonné ou supprimé. Dans de rares cas, la transaction ambiante est déjà supprimée. |Vérifiez le code et veillez à ce qu’il n’appelle pas d’opérations sur un objet supprimé. |La nouvelle tentative ne résout pas le problème. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |L’objet [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) n’a pas pu obtenir de jeton, le jeton n’est pas valide ou le jeton ne contient pas les revendications nécessaires pour exécuter l’opération. |Assurez-vous que le fournisseur de jetons est créé avec les valeurs correctes. Vérifiez la configuration du service ACS (Access Control Service). |Dans certains cas, l'exécution d'une nouvelle tentative peut aider ; ajouter une logique de nouvelle tentative au code. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Un ou plusieurs des arguments fournis à la méthode ne sont pas valides.<br /> L’URI fourni à [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Ceate](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contient un ou plusieurs segments de chemin d’accès.<br /> Le schéma d’URI fourni à [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Ceate](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) n’est pas valide. <br />La valeur de la propriété est supérieure à 32 ko. |Vérifiez le code appelant et assurez-vous que les arguments sont corrects. |La nouvelle tentative ne résout pas le problème. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |L’entité associée à l’opération n’existe pas ou a été supprimée. |Assurez-vous que l'entité existe. |La nouvelle tentative ne résout pas le problème. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Tentative de réception d'un message avec un numéro de séquence spécifique. Ce message est introuvable. |Vérifiez que le message n’a pas déjà été reçu. Vérifiez la file d'attente de lettres mortes pour voir si le message a été désactivé. |La nouvelle tentative ne résout pas le problème. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Le client ne peut pas établir de connexion à Service Bus. |Assurez-vous que le nom d'hôte fourni est correct et que l'hôte est accessible. |Une nouvelle tentative peut aider en cas de problèmes de connectivité intermittents. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Le service n’est pas en mesure de traiter la demande pour l’instant. |Le client peut attendre pendant une période de temps, puis recommencer l'opération. |Le client peut réessayer après un certain temps. Si une nouvelle tentative provoque une exception différente, vérifiez le comportement de nouvelle tentative de cette exception. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Le jeton de verrouillage associé au message a expiré ou le jeton de verrouillage est introuvable. |Supprimez le message. |La nouvelle tentative ne résout pas le problème. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Le verrou associé à cette session est perdu. |Abandonnez l’objet [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |La nouvelle tentative ne résout pas le problème. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Exception de messagerie générique qui peut être levée dans les cas suivants :<p>une tentative est effectuée pour créer un [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) à l’aide d’un nom ou d’un chemin d’accès qui appartient à un autre type d’entité (par exemple, une rubrique).</p><p>Une tentative est effectuée pour envoyer un message de taille supérieure à 256 Ko. </p>Le serveur ou le service a rencontré une erreur lors du traitement de la demande. Consultez le message de l'exception pour obtenir plus d'informations. Il s’agit généralement d’une exception temporaire.</p><p>La demande a été arrêtée parce que l’entité est limitée. Code d’erreur : 50001, 50002, 50008. </p> | Vérifiez le code et assurez-vous que seuls les objets sérialisables sont utilisés dans le corps du message (ou utilisez un sérialiseur personnalisé). <p>Consultez la documentation pour connaître les types de valeurs des propriétés pris en charge et utilisez uniquement les types pris en charge.</p><p> Vérifiez la propriété [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Si sa valeur est **true**, vous pouvez réessayer d’effectuer l’opération. </p>| Si l’exception est due à une limitation, attendez quelques secondes, puis réessayez l’opération. Le comportement de la nouvelle tentative n’est pas défini et peut ne pas être utile dans d’autres scénarios.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Tentative de création d'une entité dont le nom est déjà utilisé par une autre entité de l'espace de noms de ce service. |Supprimez l'entité existante ou choisissez un autre nom pour l'entité à créer. |La nouvelle tentative ne résout pas le problème. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |L’entité de messagerie a atteint sa taille maximale autorisée, ou le nombre maximal de connexions à un espace de noms a été dépassé. |Créez de l’espace dans l’entité en recevant des messages à partir de l’entité ou de ses files d’attente secondaires. Consultez [QuotaExceededException](#quotaexceededexception). |Une nouvelle tentative peut aider si des messages ont été supprimés entre-temps. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus renvoie cette exception si vous essayez de créer une action de règle non valide. Service Bus associe cette exception à un message désactivé si une erreur se produit lors du traitement de l'action de règle pour ce message. |Vérifiez que l'action de règle est correcte. |La nouvelle tentative ne résout pas le problème. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus renvoie cette exception si vous essayez de créer un filtre non valide. Service Bus associe cette exception à un message désactivé si une erreur se produit lors du traitement du filtre pour ce message. |Vérifiez l'exactitude du filtre. |La nouvelle tentative ne résout pas le problème. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Tentative d'acceptation d'une session avec un ID de session spécifique, mais la session est actuellement verrouillée par un autre client. |Assurez-vous que la session est déverrouillée par les autres clients. |Une nouvelle tentative peut aider si la session a été libérée entre-temps. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Trop d'opérations font partie de la transaction. |Réduisez le nombre d'opérations qui font partie de cette transaction. |La nouvelle tentative ne résout pas le problème. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Demande d'une opération d'exécution sur une entité désactivée. |Activez l'entité. |Une nouvelle tentative peut aider si l'entité a été activée entre-temps. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus renvoie cette exception si vous envoyez un message à une rubrique avec le filtre préalable activé et si aucun des filtres ne correspond. |Assurez-vous qu'au moins un filtre correspond. |La nouvelle tentative ne résout pas le problème. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Une charge utile de message dépasse la limite de 256 Ko. La limite de 256 Ko correspond à la taille totale du message, qui peut inclure des propriétés système et toute surcharge .NET. |Réduisez la taille de la charge utile de message, puis recommencez l'opération. |La nouvelle tentative ne résout pas le problème. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |La transaction ambiante (*Transaction.Current*) n'est pas valide. Elle a peut-être été terminée ou annulée. Une exception en interne peut fournir des informations supplémentaires. | |La nouvelle tentative ne résout pas le problème. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Une opération est tentée sur une transaction incertaine, ou une tentative est faite pour valider la transaction et la transaction devient incertaine. |Votre application doit gérer cette exception (comme un cas spécial), car la transaction a peut-être déjà été validée. |- |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indique que le quota d’une entité spécifique a été dépassé.

#### <a name="queues-and-topics"></a>Files d’attente et rubriques
Pour les files d’attente et les rubriques, il s’agit souvent de la taille de la file d’attente. La propriété du message d’erreur contient davantage d’informations, comme dans l’exemple suivant :

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Ce message indique que la rubrique a dépassé sa limite de taille, dans ce cas 1 Go (limite de taille par défaut). 

#### <a name="namespaces"></a>Espaces de noms

En ce qui concerne les espaces de noms, [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) peut indiquer qu’une application a dépassé le nombre maximal de connexions à un espace de noms. Par exemple :

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Causes courantes
Il existe deux causes courantes pour cette erreur : la file d'attente de lettres mortes et des récepteurs de messages non fonctionnels.

1. **[File d’attente de lettres mortes](service-bus-dead-letter-queues.md)** Un lecteur ne parvient pas à terminer les messages et ceux-ci sont renvoyés à la file d’attente/rubrique après expiration du verrouillage. Cela peut se produire si le lecteur rencontre une exception qui l’empêche d’appeler [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Une fois un message lu 10 fois, il passe à la file d'attente de lettres mortes par défaut. Ce comportement est contrôlé par la propriété [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) et a une valeur par défaut de 10. Quand les messages s'accumulent dans la file d'attente de lettres mortes, ils prennent de la place.
   
    Pour résoudre ce problème, lisez et terminez les messages de la file d'attente de lettres mortes, comme vous le feriez pour une autre file d'attente. Vous pouvez utiliser la méthode [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) pour aider à mettre en forme le chemin d’accès de la file d’attente de lettres mortes.
2. **Récepteur arrêté**. Un récepteur a cessé de recevoir des messages d’une file d’attente ou d’un abonnement. Pour l’identifier, examinez la propriété [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , qui affiche la répartition complète des messages. Si la valeur de la propriété [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) est élevée ou augmente, les messages ne sont pas lus aussi rapidement qu’ils sont écrits.

### <a name="timeoutexception"></a>TimeoutException
Une [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indique qu’une opération lancée par l’utilisateur dépasse le délai d’expiration de l’opération. 

Vous devez vérifier la valeur de la propriété [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) car cette limite, si elle est atteinte, peut également entraîner une exception [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

#### <a name="queues-and-topics"></a>Files d’attente et rubriques
Pour les files d’attente et les rubriques, le délai d’attente est spécifié dans la propriété [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings), au sein de la chaîne de connexion ou par le biais de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Le message d'erreur peut varier, mais il contient toujours la valeur du délai d'attente spécifiée pour l'opération en cours. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problèmes de connectivité, de certificat ou de délai d’expiration
Aidez-vous des étapes suivantes pour résoudre les problèmes de connectivité, de certificat ou de délai d’expiration pour tous les services sous *.servicebus.windows.net. 

- Accédez à `https://<yournamespace>.servicebus.windows.net/` ou utilisez [wget](https://www.gnu.org/software/wget/). Cet outil facilite les vérifications quand vous rencontrez des problèmes avec le filtrage des adresses IP, le réseau virtuel ou les chaînes de certificats (problème fréquent avec le SDK Java).

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
- Exécutez la commande suivante pour vérifier si un port est bloqué sur le pare-feu. Les ports utilisés sont les suivants : 443 (HTTPS), 5671 (AMQP) et 9354 (Net Messaging/SBMP). Selon la bibliothèque que vous utilisez, d’autres ports peuvent également être utilisés. Voici l’exemple de commande qui vérifie si le port 5671 est bloqué. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Sur Linux :

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Si vous constatez des problèmes de connectivité intermittents, exécutez la commande suivante pour détecter les paquets supprimés. Cette commande essaiera d’établir 25 connexions TCP différentes toutes les 1 seconde avec le service. Ensuite, vous pouvez vérifier le nombre d’entre elles ayant réussi ou échoué, ainsi que la latence de connexion TCP. Vous pouvez télécharger l’outil `psping` à partir d’[ici](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Vous pouvez utiliser des commandes équivalentes dans d’autres outils, par exemple `tnc`, `ping`, etc. 
- Si les étapes précédentes n’ont pas résolu le problème, obtenez une trace réseau et analysez-la à l’aide d’un outil tel que [Wireshark](https://www.wireshark.org/). Contactez le [support Microsoft](https://support.microsoft.com/) si nécessaire. 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problèmes qui peuvent se produire avec les mises à niveau/redémarrages du service
Les mises à niveau et redémarrages du service principal peuvent avoir l’impact suivant sur vos applications :

- Les demandes peuvent être momentanément limitées.
- Il peut y avoir une chute des messages/demandes entrants.
- Le fichier journal peut contenir des messages d’erreur.
- Les applications peuvent être déconnectées du service pendant quelques secondes.

Si le code d’application utilise le kit de développement logiciel (SDK), la stratégie de nouvelle tentative est déjà intégrée et active. L’application se reconnectera sans que cela ait un impact significatif sur l’application/le flux de travail.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations complètes sur l’API .NET Service Bus, consultez les [informations de référence sur l’API .NET Azure](/dotnet/api/overview/azure/service-bus).

Pour en savoir plus sur [Service Bus](https://azure.microsoft.com/services/service-bus/), lisez les articles suivants :

* [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
* [Architecture de Service Bus](service-bus-architecture.md)

