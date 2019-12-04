---
title: Autorité de certification autorisée pour le protocole HTTPS personnalisé sur Azure Front Door Service
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
ms.openlocfilehash: 62420889d9a4cb1e9d1c570a0845c704fca56cb3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184577"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Autorités de certification autorisées pour l’activation du protocole HTTPS personnalisé sur Azure Front Door Service

Pour un domaine personnalisé Azure Front Door Service, quand vous [activez la fonctionnalité HTTPS à l’aide de votre propre certificat](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), vous devez utiliser une autorité de certification autorisée pour créer votre certificat SSL. Si vous utilisez une autorité de certification non autorisée ou un certificat auto-signé, votre demande sera rejetée.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
