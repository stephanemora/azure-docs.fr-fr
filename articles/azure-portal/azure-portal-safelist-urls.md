---
title: Mettre sur liste fiable les URL du portail Microsoft Azure | Microsoft Docs
description: Ajouter ces URL au contournement du serveur proxy pour communiquer avec le portail Microsoft Azure et ses services
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3747ca7504e1a8a6bbeb6237c1b3cb2e5e4afb5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667470"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Mettre sur liste fiable les URL du portail Microsoft Azure sur votre pare-feu ou serveur proxy

Pour bénéficier d’une connectivité et de performances de qualité entre votre réseau local ou WAN et le cloud Azure, configurez des appareils de sécurité en local pour contourner les restrictions de sécurité pour les URL du portail Microsoft Azure. Les administrateurs réseau déploient souvent des serveurs proxy, des pare-feu ou d’autres périphériques pour vous aider à sécuriser et contrôler l’accès à Internet des utilisateurs. Toutefois, les règles conçues pour protéger les utilisateurs peuvent parfois bloquer ou ralentir le trafic Internet lié aux activités professionnelles, y compris les communications entre vous et Azure. Pour optimiser la connectivité entre votre réseau et le portail Microsoft Azure et ses services, nous vous recommandons de mettre sur liste fiable les URL du portail Microsoft Azure.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URL du portail Microsoft Azure pour le contournement du proxy

Ajoutez la liste d’URL suivante à votre serveur proxy ou pare-feu pour autoriser le trafic réseau vers ces points de terminaison à contourner les restrictions :

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *. account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *. portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* \* .windows.net
* *.wpc.azureedge.net

> [!NOTE]
> Le trafic vers ces points de terminaison utilise les ports TCP standard pour HTTP (80) et HTTPS (443).
>
>
## <a name="next-steps"></a>Étapes suivantes

* Besoin de mettre sur liste fiable des adresses IP ? Téléchargez la liste des [plages d’adresses IP de centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* D’autres services Microsoft utilisent des URL et adresses IP supplémentaires pour la connectivité. Pour optimiser la connectivité réseau des services Microsoft 365, consultez [Configurer votre réseau pour Office 365](/office365/enterprise/set-up-network-for-office-365).
