---
title: Quotas et limitations dans Azure Media Services v3 | Microsoft Docs
description: Cette rubrique décrit les quotas et les limitations dans Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2018
ms.author: juliako
ms.openlocfilehash: b50ba825f675c84f551f9a1d191aa93eaed9a628
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070852"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quotas et limitations dans Azure Media Services v3

Cet article décrit les quotas et les limitations dans Azure Media Services v3.

| Ressource | Limite par défaut | 
| --- | --- | 
| Éléments multimédias par compte Azure Media Services | 1 000 000|
| Filtres de manifeste dynamique|100|
| JobInputs par travail | 50 (fixe)|
| JobOutputs par travail/TransformOutputs dans une transformation | 20 (fixe) |
| Fichiers par JobInput|10 (fixe)|
| Taille du fichier| Dans certains scénarios, la taille maximale des fichiers pris en charge pour le traitement dans Media Services est soumise à une limite. <sup>(1)</sup> |
| Travaux par compte Media Services | 500 000 <sup>(2)</sup> (fixe)|
| Énumération des transformations|Pagination de la réponse avec 1 000 transformations par page|
| Énumération des travaux|Pagination de la réponse avec 500 travaux par page|
| LiveEvents par compte Media Services |5.|
| Comptes Media Services dans un même abonnement | 25 (fixe) |
| LiveOutputs à l’état En cours d’exécution par LiveEvent |3|
| LiveOutputs à l’état Arrêté par LiveEvent |50|
| Comptes de stockage | 100<sup>(4)</sup> (fixe) |
| Points de terminaison de diffusion en continu en cours d’exécution par compte Media Services|2|
| StreamingPolicies | 100 <sup>(3)</sup> |
| Transformations par compte Media Services | 100 (fixe)|
| StreamingLocators uniques associés à un élément multimédia à un moment donné | 100<sup>(5)</sup> (fixe) |

<sup>1</sup> La taille maximale prise en charge pour un objet blob est actuellement de 5 To dans Stockage Blob Azure. Toutefois, des limites supplémentaires sont applicables dans Azure Media Services en fonction des tailles de machine virtuelle utilisées par le service. Si votre fichier source est supérieur à 260 Go, votre travail échouera probablement. Si vous avez du contenu 4K qui dépasse la limite de 260 Go, contactez-nous à l’adresse amshelp@microsoft.com afin d’identifier des solutions d’atténuation potentielles permettant de prendre en charge votre scénario.

<sup>2</sup> Ce nombre comprend les travaux en file d’attente, terminés, actifs et annulés. Il n’inclut pas les travaux supprimés. 

Les enregistrements de travaux de votre compte qui ont plus de 90 jours sont automatiquement supprimés, même si le nombre total d’enregistrements est inférieur au quota maximal. 

<sup>3</sup> Lorsque vous utilisez une stratégie [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personnalisée, vous devez concevoir un ensemble limité de ces stratégies pour votre compte Media Services et les réutiliser pour vos éléments StreamingLocators chaque fois que les mêmes protocoles et options de chiffrement sont nécessaires. Vous ne devez pas créer une stratégie StreamingPolicy pour chaque élément StreamingLocator.

<sup>4</sup> Les comptes de stockage doivent provenir du même abonnement Azure.

<sup>5</sup> Les StreamingLocators ne sont pas conçus pour gérer le contrôle d’accès par utilisateur. Pour accorder différents droits d’accès aux utilisateurs, utilisez les solutions de gestion des droits numériques (DRM).

## <a name="support-ticket"></a>Ticket de support

Pour les ressources qui ne sont pas fixes, vous pouvez demander d’augmenter les quotas en ouvrant un [ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Incluez des informations détaillées dans la requête sur les modifications souhaitées pour les quotas, les scénarios de cas d’usage et les régions requises. <br/>Ne créez **pas** d’autres comptes Azure Media Services pour obtenir des limites supérieures.

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](media-services-overview.md)
