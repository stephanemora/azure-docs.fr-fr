---
title: Forum Aux Questions (FAQ) sur Azure Service Bus | Microsoft Docs
description: Cet article fournit des réponses aux questions fréquemment posées (FAQ) sur Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 3cd4e69481fb452391e6dc027cb41fd6dae71b7e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760247"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure Service Bus - Forum Aux Questions (FAQ)

Cet article évoque certaines questions fréquemment posées sur Microsoft Azure Service Bus. Vous pouvez également consulter les [Questions fréquentes (FAQ) du support Azure](https://azure.microsoft.com/support/faq/) pour obtenir des informations générales de support et de tarification Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Questions générales sur Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Qu’est-ce qu’Azure Service Bus ?
[Azure Service Bus](service-bus-messaging-overview.md) est une plateforme cloud de messagerie asynchrone qui vous permet d’envoyer des données entre systèmes découplés. Microsoft propose cette fonctionnalité en tant que service, ce qui signifie que vous n’avez pas besoin d’héberger votre propre matériel pour l’utiliser.

### <a name="what-is-a-service-bus-namespace"></a>Présentation des espaces de noms Service Bus
Un [espace de nom](service-bus-create-namespace-portal.md) fournit un conteneur d’étendue pour l’adressage des ressources Service Bus au sein de votre application. Il est nécessaire de créer un espace de noms pour utiliser Service Bus. C’est l’une des premières étapes à effectuer au démarrage.

### <a name="what-is-an-azure-service-bus-queue"></a>Présentation des files d’attente Azure Service Bus
Une [file d’attente Service Bus](service-bus-queues-topics-subscriptions.md) est une entité dans laquelle les messages sont stockés. Les files d’attente sont utiles lorsque vous disposez de plusieurs applications ou de plusieurs parties d’une application distribuée qui doivent communiquer entre elles. La file d’attente est comparable à un centre de distribution, dans la mesure où plusieurs produits (messages) sont reçus, puis envoyés à partir de cet emplacement.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Présentation des rubriques et des abonnements Azure Service Bus
Une rubrique peut être visualisée comme une file d’attente. En cas d’utilisation de plusieurs abonnements, elle devient un modèle de messagerie plus riche ; plus simplement, il s’agit d’un outil de communication de type un-à-plusieurs. Ce modèle de publication/abonnement (ou *pub/sub*) permet à une application d’envoyer un message à une rubrique avec plusieurs abonnements pour que ce message soit reçu par plusieurs applications.

### <a name="what-is-a-partitioned-entity"></a>Présentation des entités partitionnées
Une file d’attente ou une rubrique classique est gérée par un seul courtier de messages et stockée dans une seule banque de messagerie. Uniquement prise en charge pour les niveaux de messagerie De base et Standard, une [file d’attente ou une rubrique partitionnée](service-bus-partitioning.md) est gérée par plusieurs répartiteurs de messages et stockée dans plusieurs banques de messagerie. Cette caractéristique signifie que le débit global d’une file d’attente ou d’une rubrique partitionnée n’est plus limité par les performances d’un seul répartiteur de messages ou d’une seule banque de messagerie. En outre, la panne temporaire d’une banque de messagerie ne rend pas une rubrique ou une file d’attente partitionnée indisponible.

Le classement n’est pas garanti lors de l’utilisation d’entités partitionnées. Si une partition n’est pas disponible, vous pouvez toujours envoyer et recevoir des messages des autres partitions.

 Les entités partitionnées ne sont plus prises en charge dans la [référence SKU Premium](service-bus-premium-messaging.md). 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Quels ports du pare-feu dois-je ouvrir ? 
Vous pouvez utiliser les protocoles suivants avec Azure Service Bus pour envoyer et recevoir des messages :

- Advanced Message Queuing Protocol (AMQP)
- Service Bus Messaging Protocol (SBMP)
- HTTP

Consultez le tableau suivant pour savoir quels ports de sortie vous devez ouvrir afin d’utiliser ces protocoles dans le but de communiquer avec Azure Event Hubs. 

| Protocol | Ports | Détails | 
| -------- | ----- | ------- | 
| AMQP | 5671 et 5672 | Consultez le [Guide du protocole AMQP](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 à 9354 | Consultez l’article sur le [Mode de connectivité](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Quelles adresses IP dois-je ajouter à la liste verte ?
Pour trouver les adresses IP à ajouter à la liste verte de vos connexions, procédez comme suit :

1. Exécutez la commande suivante depuis une invite de commandes : 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Notez l’adresse IP renvoyée dans `Non-authoritative answer`. Cette adresse IP est statique. La seule modification susceptible d’entraîner une conséquence serait une restauration de l’espace de noms sur un autre cluster.

Si vous utilisez la redondance de zone pour votre espace de noms, vous devez suivre quelques étapes supplémentaires : 

1. Tout d’abord, exécutez nslookup sur l’espace de noms.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Notez le nom dans la section **Réponse ne faisant pas autorité**, qui se présente dans l’un des formats suivants : 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Exécutez nslookup pour chacun d’eux avec des suffixes s1, s2 et s3 pour obtenir les adresses IP des 3 instances en cours d’exécution dans 3 zones de disponibilité. 


## <a name="best-practices"></a>Meilleures pratiques
### <a name="what-are-some-azure-service-bus-best-practices"></a>Présentation des meilleures pratiques Azure Service Bus
Consultez [Meilleures pratiques relatives aux améliorations du niveau de performance avec Service Bus][Best practices for performance improvements using Service Bus] : cet article explique comment optimiser le niveau de performance lors de l’échange de messages.

### <a name="what-should-i-know-before-creating-entities"></a>Quelles sont les informations à connaître pour pouvoir créer des entités ?
Les propriétés suivantes de file d’attente et de rubrique sont immuables. Tenez compte de cette restriction lorsque vous configurez vos entités, dans la mesure où ces propriétés ne peuvent pas être modifiées sans créer une nouvelle entité de remplacement.

* Partitionnement
* Sessions
* Détection des doublons
* Entité rapide

## <a name="pricing"></a>Tarifs
Cette section répond à certaines questions fréquemment posées sur la tarification de Service Bus.

L’article [Tarification et facturation de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) explique les paramètres de facturation dans Service Bus. Pour obtenir des informations spécifiques sur les options de tarification de Service Bus, consultez les [détails sur la tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Vous pouvez également consulter les [Questions fréquentes (FAQ) du support Azure](https://azure.microsoft.com/support/faq/) pour obtenir des informations générales sur la tarification Azure. 

### <a name="how-do-you-charge-for-service-bus"></a>Quel est le coût de Service Bus ?
Pour obtenir toutes les informations sur la tarification Service Bus, voir la section [détails de tarification Service Bus][Pricing overview]. Outre les prix indiqués, vous êtes facturé pour les transferts de données associés aux sorties à l’extérieur du centre de données dans lequel votre application est déployée.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Quelle est l’utilisation de Service Bus soumise au transfert de données ? Laquelle ne l’est pas ?
Les transferts de données au sein d’une région Azure donnée sont effectués gratuitement, de même que les transferts de données entrants. Le transfert de données en dehors d’une région est soumis à des frais de sortie consultables [ici](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Service Bus facture-t-il le stockage ?
Non, Service Bus ne facture pas le stockage ? Toutefois, il existe un quota limitant la quantité maximale de données qui peuvent être conservées par la file d’attente/rubrique. Voir le Forum aux questions suivants.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Je dispose d’un espace de noms Service Bus Standard. Pourquoi des frais sont-ils facturés dans le groupe de ressources « $system » ?
Azure Service Bus a récemment mis à niveau les composants de facturation. C’est pourquoi, si vous disposez d’un espace de noms Service Bus Standard, des postes peuvent apparaître pour la ressource « /subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system » sous le groupe de ressources « $system ».

Ces frais représentent les frais de base par abonnement Azure pour lequel un espace de noms Service Bus Standard est approvisionné. 

Il est important de noter qu’il ne s’agit pas de nouveaux frais, c’est-à-dire qu’ils existaient également dans le modèle de facturation précédent. La seule modification est qu’ils apparaissent maintenant sous « $system ». Cela est dû aux contraintes posées par le nouveau système de facturation, qui regroupe les frais engendrés au niveau de l’abonnement, non liés à une ressource en particulier, sous l’ID de ressource « $system ».

## <a name="quotas"></a>Quotas

Pour obtenir la liste des limites et des quotas Service Bus, consultez [Vue d’ensemble des quotas Service Bus][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Service Bus fixe-t-il des quotas d’utilisation ?
Par défaut, pour n’importe quel service cloud, Microsoft définit un quota d’utilisation agrégée mensuel calculé avec tous les abonnements d’un client. Si vos besoins dépassent ces limites, vous pouvez contacter le service client à tout moment, afin de nous permettre d’ajuster ces limites en fonction de vos exigences. Pour Service Bus, le quota d’utilisation agrégée est de 5 milliards de messages par mois.

Microsoft se réserve le droit de désactiver un compte client ayant dépassé son quota d’utilisation pour un mois donné. Des notifications par e-mail sont envoyées plusieurs fois pour contacter le client avant de procéder à quoi que ce soit. Les clients qui dépassent ces quotas restent responsables de frais de dépassement occasionnés.

À l’instar d’autres services sur Azure, Service Bus applique un ensemble de quotas spécifiques pour assurer une utilisation juste des ressources. Pour plus d’informations sur ces quotas, consultez [Quotas Service Bus][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Comment gérer les messages de taille > à 1 Mo ?
Les services de messagerie Service Bus (files d’attente et rubriques/abonnements) permettent à l'application d'envoyer des messages dont la taille peut aller jusqu'à 256 Ko (niveau standard) ou 1 Mo (niveau Premium). En présence de messages de taille supérieure à 1 Mo, utilisez le modèle de vérification des requêtes décrit dans [ce billet de blog](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Dépannage
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Pourquoi ne suis-je pas en mesure de créer un espace de noms après l'avoir supprimé d'un autre abonnement ? 
Lorsque vous supprimez un espace de noms d’un abonnement, patientez pendant 4 heures avant de le recréer avec le même nom dans un autre abonnement. Sinon, le message d’erreur suivant peut s’afficher : `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Quelles sont les exceptions générées par les API Azure Service Bus et les actions recommandées ?
Pour obtenir la liste des exceptions Service Bus potentielles, consultez [Vue d’ensemble des exceptions][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Qu’est-ce qu’une signature d’accès partagé et quels langages prennent en charge la génération d’une signature ?
Les signatures d’accès partagé sont un mécanisme d’authentification basé sur des hachages sécurisés SHA-256 ou des URI. Pour plus d’informations sur la génération de vos propres signatures en Node.js, PHP, Java, Python et C#, consultez l’article [Signatures d’accès partagé][Shared Access Signatures].

## <a name="subscription-and-namespace-management"></a>Gestion des abonnements et des espaces de noms
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Migration d’un espace de noms vers un autre abonnement Azure

Vous pouvez déplacer un espace de noms d’un abonnement Azure vers un autre, à l’aide du [portail Azure](https://portal.azure.com) ou de commandes PowerShell. Pour exécuter cette opération, l’espace de noms doit être déjà actif. L’utilisateur qui exécute les commandes doit être administrateur dans les abonnements source et cible.

#### <a name="portal"></a>Portail

Pour utiliser le Portail Azure et migrer les espaces de noms Service Bus vers un autre abonnement, suivez les instructions décrites [ici](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

La séquence suivante de commandes PowerShell déplace un espace de noms d’un abonnement Azure vers un autre. Pour exécuter cette opération, l'espace de noms doit être déjà actif, et l'utilisateur qui exécute les commandes PowerShell doit être administrateur des abonnements à la fois source et cible.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Service Bus, lisez les articles suivants :

* [Présentation d’Azure Service Bus Premium (billet de blog)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Présentation d’Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Vue d’ensemble de Service Bus](service-bus-messaging-overview.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
