---
title: Guide de dépannage pour Azure Service Bus | Microsoft Docs
description: Découvrez des conseils et recommandations pour résoudre certains problèmes que vous pourriez rencontrer lors de l’utilisation d’Azure Service Bus.
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: b44587747a59acb3c0124c0a76b63de68d6d8ae7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105031288"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Guide de dépannage pour Azure Service Bus
Cet article fournit des conseils et des recommandations pour résoudre certains problèmes que vous pourriez rencontrer lors de l’utilisation d’Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problèmes de connectivité, de certificat ou de délai d’expiration
Aidez-vous des étapes suivantes pour résoudre les problèmes de connectivité, de certificat ou de délai d’expiration pour tous les services sous *.servicebus.windows.net. 

- Accédez à `https://<yournamespace>.servicebus.windows.net/` ou utilisez [wget](https://www.gnu.org/software/wget/). Cet outil facilite les vérifications quand vous rencontrez des problèmes avec le filtrage des adresses IP, le réseau virtuel ou les chaînes de certificats, qui sont courants lors de l’utilisation du Kit de développement logiciel (SDK) Java.

    Voici un exemple de message de réussite :
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Voici un exemple de message d’échec :

    ```xml
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
- Pour trouver les adresses IP appropriées à ajouter à la liste d’autorisation de vos connexions, consultez [Quelles adresses IP dois-je ajouter à la liste d’autorisation](service-bus-faq.md#what-ip-addresses-do-i-need-to-add-to-allow-list). 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problèmes qui peuvent se produire avec les mises à niveau/redémarrages du service

### <a name="symptoms"></a>Symptômes
- Les demandes peuvent être momentanément limitées.
- Il peut y avoir une chute des messages/demandes entrants.
- Le fichier journal peut contenir des messages d’erreur.
- Les applications peuvent être déconnectées du service pendant quelques secondes.

### <a name="cause"></a>Cause
Les mises à niveau et redémarrages du service principal peuvent causer ces problèmes dans vos applications.

### <a name="resolution"></a>Résolution
Si le code d’application utilise le Kit de développement logiciel (SDK), la stratégie de nouvelles tentatives est déjà intégrée et active. L’application se reconnectera sans que cela ait un impact significatif sur l’application/le flux de travail.

## <a name="unauthorized-access-send-claims-are-required"></a>Accès non autorisé : Envoyer les revendications requises

### <a name="symptoms"></a>Symptômes 
Vous pouvez voir cette erreur lorsque vous tentez d’accéder à une rubrique Service Bus à partir de Visual Studio sur un ordinateur local à l’aide d’une identité gérée affectée par l’utilisateur avec des autorisations d’envoi.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Cause
L’identité ne dispose pas des autorisations nécessaires pour accéder à la rubrique Service Bus. 

### <a name="resolution"></a>Résolution
Pour résoudre cette erreur, installez la bibliothèque [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).  Pour plus d’informations, consultez [Authentification du développement local](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication). 

Pour savoir comment affecter des autorisations à des rôles, consultez [Authentifier une identité managée avec Azure Active Directory pour accéder aux ressources Azure Service Bus](service-bus-managed-service-identity.md).

## <a name="service-bus-exception-put-token-failed"></a>Exception Service Bus : Échec du jeton PUT

### <a name="symptoms"></a>Symptômes
Lorsque vous essayez d’envoyer plus de 1 000 messages à l’aide de la même connexion Service Bus, le message d’erreur suivant s’affiche : 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Cause
Il existe une limite au nombre de jetons utilisés pour envoyer et recevoir des messages en utilisant une seule connexion à un espace de noms Service Bus. C’est 1 000. 

### <a name="resolution"></a>Résolution
Ouvrez une nouvelle connexion à l’espace de noms Service Bus pour envoyer plus de messages.

## <a name="adding-virtual-network-rule-using-powershell-fails"></a>Échec de l’ajout d’une règle de réseau virtuel à l’aide de PowerShell

### <a name="symptoms"></a>Symptômes
Vous avez configuré deux sous-réseaux à partir d’un seul réseau virtuel dans une règle de réseau virtuel. Quand vous essayez de supprimer un sous-réseau à l’aide de l’applet de commande [Remove-AzServiceBusVirtualNetworkRule](/powershell/module/az.servicebus/remove-azservicebusvirtualnetworkrule), le sous-réseau n’est pas supprimé de la règle de réseau virtuel. 

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName $resourceGroupName -Namespace $serviceBusName -SubnetId $subnetId
```

### <a name="cause"></a>Cause
L’ID Azure Resource Manager que vous avez spécifié pour le sous-réseau n’est peut-être pas valide. Cela peut se produire quand le réseau virtuel se trouve dans un autre groupe de ressources que celui qui contient l’espace de noms Service Bus. Si vous ne spécifiez pas explicitement le groupe de ressources du réseau virtuel, la commande CLI construit l’ID Azure Resource Manager à l’aide du groupe de ressources de l’espace de noms Service Bus. Elle ne parvient donc pas à supprimer le sous-réseau de la règle de réseau. 

### <a name="resolution"></a>Résolution
Spécifiez l’ID Azure Resource Manager complet du sous-réseau qui inclut le nom du groupe de ressources contenant le réseau virtuel. Par exemple :

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName myRG -Namespace myNamespace -SubnetId "/subscriptions/SubscriptionId/resourcegroups/ResourceGroup/myOtherRG/providers/Microsoft.Network/virtualNetworks/myVNet/subnets/mySubnet"
```

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants : 

- [Exceptions Azure Resource Manager](service-bus-resource-manager-exceptions.md). Répertorie les exceptions générées lors de l’interaction avec Azure Service Bus à l’aide d’Azure Resource Manager (via des modèles ou des appels directs).
- [Exceptions de messagerie](service-bus-messaging-exceptions.md). Fournit une liste d’exceptions générées par .NET Framework pour Azure Service Bus.