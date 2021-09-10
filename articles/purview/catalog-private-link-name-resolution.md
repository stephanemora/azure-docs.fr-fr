---
title: Configurer la résolution de noms DNS pour les points de terminaison privés
description: Cet article donne un aperçu de la façon dont vous pouvez utiliser un point de terminaison privé pour votre compte Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: b6d0b4cb58562540cd3b2024631bb9cec2d320c9
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607046"
---
# <a name="configure-and-verify-dns-name-resolution-for-azure-purview-private-endpoints"></a>Configurer et vérifier la résolution de noms DNS pour les points de terminaison privés Azure Purview

## <a name="conceptual-overview"></a>Vue d'ensemble conceptuelle
Une résolution de noms précise est une condition essentielle lors de la configuration de points de terminaison privés pour vos comptes Azure Purview. 

Il se peut que vous deviez activer la résolution de noms interne dans vos paramètres DNS pour résoudre les adresses IP des points de terminaison privés en nom de domaine complet (FQDN) des sources de données et de votre machine de gestion vers le compte Azure Purview et le runtime d’intégration auto-hébergée, selon les scénarios que vous déployez.

L’exemple suivant montre la résolution de noms DNS Azure Purview à l’extérieur du réseau virtuel ou lorsqu’un point de terminaison privé Azure n’est pas configuré.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="Capture d’écran montrant la résolution de noms Azure Purview à l’extérieur de CorpNet.":::

L’exemple suivant illustre la résolution de noms DNS Azure Purview à partir du réseau virtuel.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="Capture d’écran montrant la résolution de noms Purview à partir de CorpNet.":::

## <a name="deployment-options"></a>Options de déploiement 

Utilisez l’une des options suivantes pour envoyer la résolution de noms interne lorsque vous utilisez des points de terminaison privés pour votre compte Azure Purview :

-  [Déployer de nouvelles zones DNS privé Azure](#option-1---deploy-new-azure-private-dns-zones) dans votre environnement Azure dans le cadre du déploiement de points de terminaison privés. (Option par défaut).
- [Utiliser les zones DNS privé Azure existantes](#option-2---use-existing-azure-private-dns-zones). Utilisez cette option si vous utilisez un point de terminaison privé dans un modèle hub-and-spoke à partir d’un autre abonnement ou même dans le même abonnement. 
- [Utiliser vos propres serveurs DNS](#option-3---use-your-own-dns-servers) si vous n’utilisez pas de redirecteurs DNS et que vous gérez les enregistrements A directement dans vos serveurs DNS locaux.

## <a name="option-1---deploy-new-azure-private-dns-zones"></a>Option 1 : Déployer de nouvelles zones DNS privé Azure  

Pour activer la résolution de noms interne, vous pouvez déployer les zones Azure DNS requises dans votre abonnement Azure où le compte Azure Purview est déployé. 

Lorsque vous créez des points de terminaison privés de portail et de compte, les enregistrements de ressource CNAME DNS pour Azure Purview sont automatiquement mis à jour vers un alias dans un sous-domaine avec le préfixe `privatelink`. Par défaut, nous créons également une [zone DNS privée](../dns/private-dns-overview.md) qui correspond au sous-domaine `privatelink` pour Azure Purview en tant que privatelink.purview.azure.com comprenant des enregistrements de ressources A DNS pour les points de terminaison privés. Si vous activez des points de terminaison privés d’ingestion, des zones DNS supplémentaires sont requises pour les ressources managées. 

Le tableau suivant présente un exemple de zones DNS privé Azure et d’enregistrements A DNS qui sont déployés dans le cadre de la configuration du point de terminaison privé pour un compte Azure Purview si vous activez l’_intégration DNS privé_ pendant le déploiement : 

Point de terminaison privé  |Point de terminaison privé associé à  |Zone DNS (nouveau)  |Enregistrement A (exemple) |
|---------|---------|---------|---------|
|Compte     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
|Portail     |Compte Azure Purview          |`privatelink.purview.azure.com`        |Web         |
|Ingestion     |Compte de stockage managé Purview – Blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|Ingestion   |Compte de stockage managé Purview – File d’attente         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|Ingestion     |Compte de stockage managé Purview – Event Hub         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

Lorsque vous résolvez l’URL du point de terminaison Azure Purview à l’extérieur du réseau virtuel avec le point de terminaison privé, elle correspond au point de terminaison public d’Azure Purview. En cas de résolution à partir du réseau virtuel hébergeant le point de terminaison privé, l’URL du point de terminaison Azure Purview correspond à l’adresse IP du point de terminaison privé.

Par exemple, si le nom d’un compte Azure Purview est « PurviewA », lorsqu’il est résolu à l’extérieur du réseau virtuel qui héberge le point de terminaison privé, il s’agira de :

| Name | Type | Valeur |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | Un | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |

Les enregistrements de ressources DNS correspondant à PurviewA, lorsqu’ils sont résolus dans le réseau virtuel hébergeant le point de terminaison privé, sont les suivants :

| Name | Type | Valeur |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | Un | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |

## <a name="option-2---use-existing-azure-private-dns-zones"></a>Option 2 : Utiliser les zones DNS privé Azure existantes
Pendant le déploiement des points de terminaison privés Azure Purview, vous pouvez choisir l’_intégration DNS privé_ en utilisant les zones DNS privé Azure existantes. C’est généralement le cas pour les organisations où le point de terminaison privé est utilisé pour d’autres services dans Azure. Dans ce cas, lors du déploiement des points de terminaison privés, veillez à sélectionner les zones DNS existantes au lieu d’en créer de nouvelles. 

Cela s’applique également si votre organisation utilise un abonnement central ou hub pour toutes les zones DNS privé Azure.

La liste suivante présente les zones Azure DNS et les enregistrements A requis pour les points de terminaison privés Purview :

> [!NOTE]
> Mettez à jour tous les noms avec `PurviewA`, `scaneastusabcd1234` et `atlas-12345678-1234-1234-abcd-123456789abc` avec le nom des ressources Azure correspondantes dans votre environnement. Par exemple, au lieu de `scaneastusabcd1234`, utilisez le nom de votre compte de stockage managé Azure Purview.

Point de terminaison privé  |Point de terminaison privé associé à  |Zone DNS (existante)  |Enregistrement A (exemple) |
|---------|---------|---------|---------|
|Compte     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
|Portail     |Compte Azure Purview          |`privatelink.purview.azure.com`        |Web         |
|Ingestion     |Compte de stockage managé Purview – Blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|Ingestion   |Compte de stockage managé Purview – File d’attente         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|Ingestion     |Compte de stockage managé Purview – Event Hub         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

Pour plus d’informations, consultez [Charges de travail de réseau virtuel sans serveur DNS personnalisé](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server) et [Charges de travail locales à l’aide de scénarios de redirecteur DNS](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) dans [Configuration DNS de point de terminaison privé Azure](../private-link/private-endpoint-dns.md).

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Diagramme illustrant la résolution de noms Azure Purview"lightbox="media/catalog-private-link/purview-name-resolution-diagram.png":::

Si vous utilisez un serveur DNS personnalisé sur votre réseau, les clients doivent pouvoir résoudre le nom de domaine complet FQDN du point de terminaison Azure Purview en l’adresse IP du point de terminaison privé. Configurez votre serveur DNS de manière à déléguer votre sous-domaine Private Link à la zone DNS privée du réseau virtuel. Ou bien, configurez les enregistrements A pour `PurviewA.privatelink.purview.azure.com` avec l’adresse IP du point de terminaison privé.
Une fois le déploiement du point de terminaison privé terminé, assurez-vous qu’il existe une [liaison de réseau virtuel](../dns/private-dns-virtual-network-links.md) pour la résolution de noms sur la zone DNS privé Azure correspondante vers le réseau virtuel Azure où le point de terminaison privé a été déployé. 

Pour plus d’informations, consultez [Configuration DNS des points de terminaison privés Azure](../private-link/private-endpoint-dns.md).

## <a name="option-3---use-your-own-dns-servers"></a>Option 3 : Utiliser vos propres serveurs DNS

Si vous n’utilisez pas de redirecteurs DNS et que vous gérez les enregistrements A directement dans vos serveurs DNS locaux pour résoudre les points de terminaison par le biais de leurs adresses IP privées, vous devrez peut-être créer les enregistrements A suivants dans vos serveurs DNS.

> [!NOTE]
> Mettez à jour tous les noms avec `PurviewA`, `scaneastusabcd1234` et `atlas-12345678-1234-1234-abcd-123456789abc` avec le nom des ressources Azure correspondantes dans votre environnement. Par exemple, au lieu de `scaneastusabcd1234`, utilisez le nom de votre compte de stockage managé Azure Purview.

| Name | Type | Valeur |
| ---------- | -------- | --------------- |
| `web.purview.azure.com` | Un | \<portal private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.blob.core.windows.net` | A | \<blob-ingestion private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.queue.core.windows.net` | A | \<queue-ingestion private endpoint IP address of Azure Purview> |
| `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`| A | \<namespace-ingestion private endpoint IP address of Azure Purview> |
| `PurviewA.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.scan.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.catalog.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.proxy.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.guardian.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.manifest.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cdn.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.hub.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.catalog.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cseo.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datascan.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datashare.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datasource.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.policy.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.sensitivity.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |

## <a name="verify-and-dns-test-name-resolution-and-connectivity"></a>Vérification et test DNS de la résolution de noms et de la connectivité 

1. Si vous utilisez des zones DNS privé Azure, assurez-vous que les zones DNS suivantes et les enregistrements A correspondants sont créés dans votre abonnement Azure :

   |Point de terminaison privé  |Point de terminaison privé associé à  |Zone DNS  |Enregistrement A (exemple) |
   |---------|---------|---------|---------|
   |Compte     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
   |Portail     |Compte Azure Purview          |`privatelink.purview.azure.com`        |Web         |
   |Ingestion     |Compte de stockage managé Purview – Blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
   |Ingestion   |Compte de stockage managé Purview – File d’attente         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
   |Ingestion     |Compte de stockage managé Purview – Event Hub         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

2. Créez des [liaisons de réseau virtuel](../dns/private-dns-virtual-network-links.md) dans vos zones DNS privé Azure pour vos réseaux virtuels Azure afin de permettre la résolution de noms interne.
   
3. À partir de votre PC de gestion et de la machine virtuelle du runtime d’intégration auto-hébergé, testez la résolution de noms et la connectivité réseau à votre compte Azure Purview à l’aide d’outils tels que Nslookup.exe et PowerShell.

Pour tester la résolution de noms, vous devez résoudre les noms de domaine complets FQDN suivants par le biais de leurs adresses IP privées : (À la place de PurviewA, scaneastusabcd1234 ou atlas-12345678-1234-1234-abcd-123456789abc, utilisez le nom d’hôte associé au nom de votre compte Purview et aux noms des ressources managées.)

- `PurviewA.purview.azure.com`
- `web.purview.azure.com`
- `scaneastusabcd1234.blob.core.windows.net`
- `scaneastusabcd1234.queue.core.windows.net`
- `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`

Pour tester la connectivité réseau, à partir de la machine virtuelle du runtime d’intégration auto-hébergé, vous pouvez lancer la console PowerShell et tester la connectivité à l’aide de `Test-NetConnection`. Vous devez résoudre chaque point de terminaison par son point de terminaison privé et obtenir la valeur True pour TcpTestSucceeded. (À la place de PurviewA, scaneastusabcd1234 ou atlas-12345678-1234-1234-abcd-123456789abc, utilisez le nom d’hôte associé au nom de votre compte Purview et aux noms des ressources managées.)

- `Test-NetConnection -ComputerName PurviewA.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName web.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.blob.core.windows.net -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.queue.core.windows.net -port 443`
- `Test-NetConnection -ComputerName atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net -port 443` 

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes de configuration des point de terminaison privés pour votre compte Azure Purview](catalog-private-link-troubleshoot.md)
- [Gérer des sources de données dans Azure Purview](./manage-data-sources.md)
