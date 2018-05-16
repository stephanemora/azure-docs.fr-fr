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
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 21fc80d7cb274197ae75d2fd5524e76e1e6288d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quotas et limitations dans Azure Media Services v3

Cette rubrique décrit les quotas et les limitations dans Azure Media Services v3.

| Ressource | Limite par défaut | 
| --- | --- | 
| Éléments multimédias par compte Azure Media Services | 1 000 000|
| JobInputs par travail | 100 |
| JobOutputs par travail | 30 (fixe) |
| Taille du fichier| Dans certains scénarios, la taille maximale des fichiers pris en charge pour le traitement dans Media Services est soumise à une limite. <sup>(1)</sup> |
| Travaux par compte Media Services | 50,000<sup>(2)</sup> |
| LiveEvents par compte Media Services |5.|
| Comptes Media Services dans un même abonnement | 25 (fixe) |
| StreamingPolicies | 1 000 000<sup>(3)</sup> |
| LiveOutputs à l’état En cours d’exécution par LiveEvent |3|
| LiveOutputs à l’état Arrêté par LiveEvent |50|
| Comptes de stockage | 1 000<sup>(4)</sup> (fixe) |
| Points de terminaison de diffusion en continu en cours d’exécution par compte Media Services|2|
| Transformations par compte Media Services | 20 |
| StreamingLocators uniques associés à un élément multimédia à un moment donné | 20<sup>(5)</sup> |
  
<sup>1</sup>La taille maximale prise en charge pour un seul objet blob est actuellement de 5 To dans Stockage Blob Azure. Toutefois, des limites supplémentaires sont applicables dans Azure Media Services en fonction des tailles de machine virtuelle utilisées par le service. Si votre fichier source est supérieur à 260 Go, votre travail échouera probablement. Si vous avez du contenu 4K qui dépasse la limite de 260 Go, contactez-nous à l’adresse amshelp@microsoft.com afin d’identifier des solutions d’atténuation potentielles permettant de prendre en charge votre scénario.

<sup>2</sup> Ce nombre comprend les travaux en file d’attente, terminés, actifs et annulés. Il n’inclut pas les travaux supprimés. 

Les enregistrements de travaux de votre compte qui ont plus de 90 jours sont automatiquement supprimés, même si le nombre total d’enregistrements est inférieur au quota maximal. 

<sup>3</sup> Un nombre limite de 1 million d’entrées StreamingPolicy a été défini pour les différentes stratégies Media Services (par exemple, pour la stratégie StreamingLocator ou pour ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Vous devez appliquer le même ID de stratégie si vous utilisez toujours le même nombre de jours, les mêmes autorisations d’accès, etc. 

<sup>4</sup> Les comptes de stockage doivent provenir du même abonnement Azure.

<sup>5</sup> Les StreamingLocators ne sont pas conçus pour gérer le contrôle d’accès par utilisateur. Pour accorder différents droits d’accès aux utilisateurs, utilisez les solutions de gestion des droits numériques (DRM).

## <a name="support-ticket"></a>Ticket de support

Pour les ressources qui ne sont pas fixes, vous pouvez demander d’augmenter les quotas en ouvrant un [ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Merci d’inclure des informations détaillées dans la requête sur les modifications souhaitées pour les quotas, les scénarios de cas d’usage et les régions requises. <br/>Ne créez **pas** d’autres comptes Azure Media Services pour obtenir des limites supérieures.

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](media-services-overview.md)
