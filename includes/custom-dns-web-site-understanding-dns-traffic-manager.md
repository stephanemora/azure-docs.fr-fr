---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: f42a97cdd74d360bc047ef561cbe626d526f9e4a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66136221"
---
Le DNS (Domain Name System) permet de localiser des éléments sur Internet. Par exemple, lorsque vous entrez une adresse dans votre navigateur ou cliquez sur un lien d'une page Web, le DNS est utilisé pour traduire le domaine en adresse IP. L'adresse IP joue le même rôle qu'une adresse postale, tout en étant moins significative. Par exemple, il est bien plus facile de se souvenir d'un nom DNS tel que **contoso.com** que d'une adresse IP telle que 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Le système DNS est basé sur des *enregistrements*. Les enregistrements associent un *nom*spécifique, tel que **contoso.com**, à une adresse IP ou un autre nom DNS. Lorsqu'une application (par exemple, un navigateur Web) recherche un nom dans le DNS, elle trouve l'enregistrement et utilise sa cible comme adresse. Si la valeur ciblée correspond à une adresse IP, le navigateur l'utilise. Si la cible est un autre nom DNS, l'application doit à nouveau procéder à une résolution. En fin de compte, toute résolution de nom génère une adresse IP.

Lorsque vous créez un site web Azure, un nom DNS lui est automatiquement attribué. Ce nom se présente sous la forme **&lt;nom_site&gt;.azurewebsites.net**. Lorsque vous ajoutez votre site web en tant que point de terminaison Azure Traffic Manager, il est accessible via le domaine **&lt;votreprofiltrafficmanager&gt;.trafficmanager.net**.

> [!NOTE]
> Si votre site web est configuré en tant que point de terminaison Traffic Manager, vous utiliserez l’adresse **.trafficmanager.net** lors de la création d’enregistrements DNS.
> 
> Seuls les enregistrements CNAME peuvent être utilisés avec Traffic Manager.
> 
> 

Il existe de nombreux types d'enregistrements, chacun présentant ses propres fonctions et limites, mais pour les sites web configurés en tant que points de terminaison Traffic Manager, seul le type d'enregistrement *CNAME* nous intéresse.

### <a name="cname-or-alias-record"></a>Enregistrement CNAME ou d'alias
Un enregistrement CNAME mappe un *spécifique* nom DNS, tel que **mail.contoso.com** ou **www\.contoso.com**, vers un autre nom de domaine (canonique). Dans le cas des sites web Azure utilisant Traffic Manager, le nom de domaine canonique correspond au nom de domaine **&lt;monapp>.trafficmanager.net** de votre profil Traffic Manager. Une fois créé, l’enregistrement CNAME crée un alias pour le nom de domaine **&lt;monapp>.trafficmanager.net**. L’entrée CNAME devient automatiquement l’adresse IP de votre nom de domaine **&lt;monapp>.trafficmanager.net**. Ainsi, même si l’adresse IP du site web change, vous n’avez rien à faire.

Lorsque le trafic parvient à Traffic Manager, il est acheminé vers votre site web à l'aide de la méthode d'équilibrage de charge pour laquelle il est configuré. Cela se fait de manière totalement transparente pour les visiteurs de votre site web. Ils ne voient que le nom de domaine personnalisé dans votre navigateur.

> [!NOTE]
> Certains bureaux d’enregistrement de domaine permet uniquement de mappage de sous-domaines lors de l’utilisation d’un enregistrement CNAME, tel que **www\.contoso.com**et pas d’un nom racine, comme **contoso.com**. Pour plus d'informations sur les enregistrements CNAME, consultez la documentation fournie par votre bureau d'enregistrement, la <a href="https://en.wikipedia.org/wiki/CNAME_record">page Wikipédia sur l'enregistrement CNAME</a> ou le document <a href="https://tools.ietf.org/html/rfc1035">Noms de domaine IETF - Implémentation et spécification</a>.

