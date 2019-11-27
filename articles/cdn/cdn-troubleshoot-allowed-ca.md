---
title: Autorité de certification autorisée pour le protocole HTTPS personnalisé sur Azure CDN
description: Si vous utilisez votre propre certificat pour activer HTTPS sur un domaine personnalisé, vous devrez utiliser une autorité de certification autorisée (CA) pour le créer.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5462502514a3e327913122fe99fd699856891216
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083103"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autorités de certification autorisées pour le protocole HTTPS personnalisé sur Azure CDN

Pour un domaine personnalisé Azure CDN (Content Delivery Network) sur un point de terminaison **Azure CDN Standard de Microsoft**, lorsque vous [activez la fonctionnalité HTTPS à l’aide de votre propre certificat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), vous devez utiliser une autorité de certification autorisée pour créer votre certificat SSL. Si vous utilisez une autorité de certification non autorisée ou un certificat auto-signé, votre demande sera rejetée.

> [!NOTE]
> Seuls les profils **Azure CDN Standard de Microsoft** permettent d’utiliser son propre certificat pour activer le protocole HTTPS personnalisé. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

