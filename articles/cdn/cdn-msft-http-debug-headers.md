---
title: Déboguer des en-têtes HTTP pour Azure CDN de Microsoft | Microsoft Docs
description: Les en-têtes de demande de cache de débogage fournissent des informations supplémentaires sur la stratégie de cache appliquée à la ressource demandée. Ces en-têtes sont spécifiques à Azure CDN de Microsoft.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68814071"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Déboguer l’en-tête HTTP pour Azure CDN de Microsoft
L’en-tête de réponse de débogage, `X-Cache`, fournit des détails sur la couche de la pile CDN à partir de laquelle le contenu a été servi. Cet en-tête est spécifique à Azure CDN de Microsoft.

### <a name="response-header-format"></a>Format d’en-tête de réponse

En-tête | Description
-------|------------
Cache X : TCP_HIT | Cet en-tête est renvoyé lorsque le contenu est servi à partir du cache de périmètre CDN. 
Cache X : TCP_REMOTE_HIT | Cet en-tête est renvoyé lorsque le contenu est traité à partir du cache régional CDN (couche de protection d’origine)
Cache X : TCP_MISS | Cet en-tête est retourné en cas d’absence dans le cache, et le contenu est pris en charge à partir de l’origine. 


