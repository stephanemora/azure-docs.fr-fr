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
ms.openlocfilehash: ab3cbdf938409f4eacffa10ae5cb20f8c36b5856
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124667"
---
# <a name="azure-event-hubs---troubleshooting-guide"></a>Azure Event Hubs - Guide de résolution des problèmes
Cet article fournit des conseils et des recommandations pour résoudre certains problèmes que vous pourriez rencontrer lors de l’utilisation d’Azure EventHubs.

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
- Si vous constatez des problèmes de connectivité intermittents, exécutez la commande suivante pour détecter les paquets supprimés. Cette commande essaiera d’établir 25 connexions TCP différentes toutes les 1 seconde avec le service. Ensuite, vous pouvez vérifier le nombre d’entre elles ayant réussi ou échoué, ainsi que la latence de connexion TCP. Vous pouvez télécharger l’outil `psping` à partir d’[ici](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
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

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)
* [Créer un hub d’événements](event-hubs-create.md)
* [FAQ sur les hubs d’événements](event-hubs-faq.md)
