---
title: Déboguer des en-têtes HTTP pour Azure CDN de Microsoft | Microsoft Docs
description: Les en-têtes de demande de cache de débogage fournissent des informations supplémentaires sur la stratégie de cache appliquée à la ressource demandée. Ces en-têtes sont spécifiques à Azure CDN de Microsoft.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 56f9b5b33600947920ff928eac53c0149afef682
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122564102"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Déboguer l’en-tête HTTP pour Azure CDN de Microsoft
L’en-tête de réponse de débogage, `X-Cache`, fournit des détails sur la couche de la pile CDN à partir de laquelle le contenu a été servi. Cet en-tête est spécifique à Azure CDN de Microsoft.

### <a name="response-header-format"></a>Format d’en-tête de réponse

En-tête | Description
-------|------------
Cache X : TCP_HIT | Cet en-tête est renvoyé lorsque le contenu est servi à partir du cache de périmètre CDN. 
X-Cache: TCP_REMOTE_HIT | Cet en-tête est renvoyé lorsque le contenu est traité à partir du cache régional CDN (couche de protection d’origine)
X-Cache: TCP_MISS | Cet en-tête est retourné en cas d’absence dans le cache, et le contenu est pris en charge à partir de l’origine.
X-Cache: PRIVATE_NOSTORE | Cet en-tête est renvoyé lorsque la requête ne peut pas être mise en cache, car l’en-tête de réponse Cache-Control est défini sur private (privé) ou sur no-store (sans stockage).
X-Cache: CONFIG_NOCACHE | Cet en-tête est renvoyé lorsque la requête est configurée pour ne pas effectuer de mise en cache dans le profil CDN.
X-Cache: N/A | Cet en-tête est renvoyé lorsque la requête a été refusée par une URL signée et le jeu de règles.

Pour plus d’informations sur les en-têtes HTTP pris en charge dans Azure CDN, consultez [De Front Door au back-end](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend).
