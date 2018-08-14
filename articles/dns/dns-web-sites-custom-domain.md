---
title: Didacticiel - Créer des enregistrements Azure DNS personnalisés pour une application web
description: Dans ce didacticiel, vous apprendrez à créer des enregistrements DNS de domaine personnalisés pour une application web à l’aide d’Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/20/2018
ms.author: victorh
ms.openlocfilehash: b39c2c672869bb446e58134a85130d10491fe047
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621111"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Didacticiel : créer des enregistrements DNS pour une application web dans un domaine personnalisé 

Vous pouvez configurer Azure DNS pour héberger un domaine personnalisé pour vos applications web. Par exemple, vous pouvez créer une application web Azure et y donner l’accès à vos utilisateurs par www.contoso.com ou contoso.com comme nom de domaine complet (FQDN).

> [!NOTE]
> Contoso.com est utilisé comme exemple tout au long de ce didacticiel. Changez votre nom de votre domaine par contoso.com.

Pour cela, vous devez créer trois enregistrements :

* un enregistrement « A » racine pointant vers contoso.com
* un enregistrement « TXT » racine pour la vérification
* un enregistrement « CNAME » pour le nom www qui pointe vers l’enregistrement A

N'oubliez pas que si vous créez un enregistrement A pour une application web dans Azure, l'enregistrement A doit être mis à jour manuellement si l’adresse IP sous-jacente pour l'application web change.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un enregistrement A et TXT pour votre domaine personnalisé
> * Créer un enregistrement CNAME pour votre domaine personnalisé
> * Tester les nouveaux enregistrements
> * Ajouter les noms d'hôte personnalisés à votre application web
> * Tester les noms d'hôtes personnalisés


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Prérequis

- [Créez une application App Service](../app-service/app-service-web-get-started-html.md), ou utilisez une application créée pour un autre didacticiel.

- Créez une zone DNS dans Azure DNS et déléguez cette zone de votre bureau d’enregistrement à Azure DNS.

   1. Pour créer une zone DNS, suivez la procédure décrite dans [Créer une zone DNS](dns-getstarted-create-dnszone.md).
   2. Pour déléguer votre zone à Azure DNS, suivez la procédure décrite dans [Délégation de domaine DNS](dns-domain-delegation.md).

Après avoir créé une zone et l’avoir déléguée à Azure DNS, vous pouvez ensuite créer des enregistrements pour votre domaine personnalisé.

## <a name="create-an-a-record-and-txt-record"></a>Créer un enregistrement A et un enregistrement TXT

Un enregistrement A est utilisé pour mapper un nom vers son adresse IP. Dans l’exemple suivant, nous allons assigner \@ en tant qu’enregistrement A pour une adresse IPv4. \@ représente habituellement le domaine racine.

### <a name="get-the-ipv4-address"></a>Obtenir l’adresse IPv4

Dans le volet de navigation gauche de la page App Services du portail Azure, sélectionnez **Domaines personnalisés**. 

![Menu Domaines personnalisés](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Dans la page **Domaines personnalisés**, copiez l’adresse IPv4 de l’application :

![Navigation au sein du portail pour accéder à l’application Azure](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>Créer l’enregistrement A

```powershell
New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>Créer l’enregistrement TXT

App Services utilise cet enregistrement uniquement au moment de la configuration, pour vérifier que vous possédez le domaine personnalisé. Une fois votre domaine personnalisé validé et configuré dans App Service, vous pourrez supprimer cet enregistrement TXT.

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name `"@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>Créer un enregistrement CNAME

Si votre domaine est déjà géré par Azure DNS (consultez [Délégation de domaine DNS](dns-domain-delegation.md)), vous pouvez utiliser l’exemple suivant pour créer un enregistrement CNAME pour contoso.azurewebsites.net.

Ouvrez Azure PowerShell et créez un enregistrement CNAME. Cet exemple crée un jeu d’enregistrements de type CNAME avec une « durée de vie » de 600 secondes dans la zone DNS nommée « contoso.com » avec l’alias pour l’application web contoso.azurewebsites.net.

### <a name="create-the-record"></a>Créer l’enregistrement

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -cname "contoso.azurewebsites.net")
```

L’exemple suivant est la réponse :

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>Tester les nouveaux enregistrements

Vous pouvez valider que les enregistrements ont été correctement créés en interrogeant « www.contoso.com » et « contoso.com » à l'aide de nslookup, comme indiqué ci-dessous :

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>Ajouter des noms d’hôte personnalisés

Vous pouvez maintenant ajouter les noms d’hôte personnalisés à votre application web :

```powershell
set-AzureRmWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>Tester les noms d'hôtes personnalisés

Ouvrez un navigateur web et accédez à `http://www.<your domainname>` et `http://<you domain name>`.

> [!NOTE]
> Veillez à inclure le préfixe `http://`, sinon votre navigateur peut tenter de prédire une URL pour vous !

Vous devez voir la même page pour les deux URL. Par exemple : 

![Service d’application Contoso](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’avez plus besoin des ressources créées dans ce didacticiel, supprimez le groupe de ressources **myresourcegroup**.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à créer des zones privées Azure DNS.

> [!div class="nextstepaction"]
> [Prise en main des zones Azure DNS privées à l’aide de PowerShell](private-dns-getstarted-powershell.md)
