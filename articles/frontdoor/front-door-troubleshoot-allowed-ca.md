---
title: Autorité de certification autorisée pour le protocole HTTPS personnalisé sur Azure Front Door
description: Si vous utilisez votre propre certificat pour activer HTTPS sur un domaine personnalisé, vous devrez utiliser une autorité de certification autorisée (CA) pour le créer.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 611f5730afed4c3a84b81d6acfd33b633c532bbc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874668"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autorités de certification autorisées pour l’activation du protocole HTTPS personnalisé sur Azure Front Door

Pour un domaine personnalisé Azure Front Door, lorsque vous [activez la fonctionnalité HTTPS à l'aide de votre propre certificat](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), vous devez utiliser une autorité de certification autorisée pour créer votre certificat TLS/SSL. Si vous utilisez une autorité de certification non autorisée ou un certificat auto-signé, votre demande sera rejetée.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
