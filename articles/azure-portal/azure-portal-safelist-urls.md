---
title: Autoriser les URL du portail Azure sur votre pare-feu ou serveur proxy
description: Pour optimiser la connectivité entre votre réseau et le portail Microsoft Azure et ses services, nous vous recommandons de mettre sur liste fiable ces URL.
ms.date: 06/21/2021
ms.topic: conceptual
ms.openlocfilehash: 70e2f55a381c38b0a4244f742b7b2c51e6ca81f6
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516358"
---
# <a name="allow-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Autoriser les URL du portail Azure sur votre pare-feu ou serveur proxy

Pour optimiser la connectivité entre votre réseau et le portail Microsoft Azure et ses services, nous vous recommandons de mettre sur liste fiable les URL spécifiques du portail Azure. Faire cela permet d’améliorer les performances et la connectivité entre votre réseau local ou étendu et le cloud Azure.

Les administrateurs réseau déploient souvent des serveurs proxy, des pare-feu ou d’autres périphériques pour vous aider à sécuriser et contrôler l’accès à Internet des utilisateurs. Les règles conçues pour protéger les utilisateurs peuvent parfois bloquer ou ralentir le trafic Internet lié aux activités professionnelles. Ce trafic comprend les communications entre vous et Azure sur les URL répertoriées ici.

> [!TIP]
> Pour faciliter le diagnostic des problèmes liés aux connexions réseau à ces domaines, consultez https://portal.azure.com/selfhelp.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL du portail Microsoft Azure pour le contournement du proxy

Les points de terminaison d’URL à autoriser pour le portail Azure sont spécifiques au cloud Azure sur lequel votre organisation est déployée. Sélectionnez votre cloud, puis ajoutez la liste d’URL à votre serveur proxy ou pare-feu pour autoriser le trafic réseau vers ces points de terminaison afin de contourner les restrictions. Nous vous déconseillons d’ajouter des URL supplémentaires liées au portail, à l’exception de celles répertoriées ici, même si vous souhaitez peut-être ajouter des URL relatives à d’autres produits et services Microsoft.

#### <a name="public-cloud"></a>[Cloud public](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[U.S. U.S. Government](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Cloud China Government](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Le trafic vers ces points de terminaison utilise les ports TCP standard pour HTTP (80) et HTTPS (443).
