---
title: Mettre sur liste fiable les URL du portail Microsoft Azure | Microsoft Docs
description: Ajouter ces URL au contournement du serveur proxy pour communiquer avec le portail Microsoft Azure et ses services
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3f81d41bc6d8ce07ea4e7b11c7c48f9b68d70466
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310563"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Mettre sur liste fiable les URL du portail Microsoft Azure sur votre pare-feu ou serveur proxy

Pour bénéficier d’une connectivité et de performances de qualité entre votre réseau local ou WAN et le cloud Azure, configurez des appareils de sécurité en local pour contourner les restrictions de sécurité pour les URL du portail Microsoft Azure. Les administrateurs réseau déploient souvent des serveurs proxy, des pare-feu ou d’autres périphériques pour vous aider à sécuriser et contrôler l’accès à Internet des utilisateurs. Toutefois, les règles conçues pour protéger les utilisateurs peuvent parfois bloquer ou ralentir le trafic Internet lié aux activités professionnelles, y compris les communications entre vous et Azure. Pour optimiser la connectivité entre votre réseau et le portail Microsoft Azure et ses services, nous vous recommandons de mettre sur liste fiable les URL du portail Microsoft Azure.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL du portail Microsoft Azure pour le contournement du proxy

Les points de terminaison d’URL à mettre sur liste fiable pour le portail Azure sont spécifiques au cloud Azure sur lequel votre organisation est déployée. Sélectionnez votre cloud, puis ajoutez la liste d’URL à votre serveur proxy ou pare-feu pour autoriser le trafic réseau vers ces points de terminaison afin de contourner les restrictions.

#### <a name="public-cloudtabpublic-cloud"></a>[Cloud public](#tab/public-cloud)
```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
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

#### <a name="us-government-cloudtabus-government-cloud"></a>[U.S. U.S. Government](#tab/us-government-cloud)
```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloudtabchina-government-cloud"></a>[Cloud China Government](#tab/china-government-cloud)
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
>
>
## <a name="next-steps"></a>Étapes suivantes

Besoin de mettre sur liste fiable des adresses IP ? Téléchargez la liste des plages d’adresses IP de centres de données Microsoft Azure pour votre cloud :

* [Monde](https://www.microsoft.com/download/details.aspx?id=56519)
* [U.S. Government](https://www.microsoft.com/download/details.aspx?id=57063)
* [Allemagne](https://www.microsoft.com/download/details.aspx?id=57064)
* [Chine](https://www.microsoft.com/download/details.aspx?id=57062)

D’autres services Microsoft utilisent des URL et adresses IP supplémentaires pour la connectivité. Pour optimiser la connectivité réseau des services Microsoft 365, consultez [Configurer votre réseau pour Office 365](/office365/enterprise/set-up-network-for-office-365).
