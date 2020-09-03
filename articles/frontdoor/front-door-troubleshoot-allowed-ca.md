---
title: Autorité de certification autorisée pour le protocole HTTPS personnalisé sur Azure Front Door
description: Si vous utilisez votre propre certificat pour activer HTTPS sur un domaine personnalisé, vous devrez utiliser une autorité de certification autorisée (CA) pour le créer.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: duau
ms.openlocfilehash: 7bdef37561687b49b030d8237472c0d35f945c13
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399121"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autorités de certification autorisées pour l’activation du protocole HTTPS personnalisé sur Azure Front Door

Pour un domaine personnalisé Azure Front Door, lorsque vous [activez la fonctionnalité HTTPS à l'aide de votre propre certificat](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), vous devez utiliser une autorité de certification autorisée pour créer votre certificat TLS/SSL. Si vous utilisez une autorité de certification non autorisée ou un certificat auto-signé, votre demande sera rejetée.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
