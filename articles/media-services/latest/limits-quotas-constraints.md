---
title: Quotas et limitations dans Azure Media Services v3 | Microsoft Docs
description: Cette rubrique décrit les quotas et les limitations dans Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.openlocfilehash: 42b8c4caa53ffa6b3bc1148544c75602597ac452
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153841"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quotas et limitations dans Azure Media Services v3

Cet article décrit les quotas et les limitations dans Azure Media Services v3.

| Ressource | Limite par défaut | 
| --- | --- | 
| Éléments multimédias par compte Azure Media Services | 1 000 000|
| Filtres de manifeste dynamique|100|
| JobInputs par travail | 50 (fixe)|
| JobOutputs par travail | 20 (fixe) |
| TransformOutputs dans une forme Transformer | 20 (fixe) |
| Fichiers par JobInput|10 (fixe)|
| Taille du fichier| Dans certains scénarios, la taille maximale des fichiers pris en charge pour le traitement dans Media Services est soumise à une limite. <sup>(1)</sup> |
| Travaux par compte Media Services | 500 000 <sup>(2)</sup> (fixe)|
| Énumération des transformations|Pagination de la réponse avec 1 000 transformations par page|
| Énumération des travaux|Pagination de la réponse avec 500 travaux par page|
| Événements en temps réel par compte Media Services |5.|
| Comptes Media Services dans un même abonnement | 25 (fixe) |
| Sorties en direct par événement en direct |3 <sup>(3)</sup> |
| Durée de la sortie de Live max | 25 heures |
| Comptes de stockage | 100<sup>(4)</sup> (fixe) |
| Points de terminaison de streaming (arrêtés ou en cours d’exécution) par compte Media Services|2 (fixe)|
| Stratégies de diffusion en continu | 100 <sup>(5)</sup> |
| Transformations par compte Media Services | 100 (fixe)|
| Localisateurs de streaming uniques associés à une ressource à un moment donné | 100<sup>(6)</sup> (fixe) |
| Stratégie de clé de contenu |30 | 

<sup>1</sup> La taille maximale prise en charge pour un objet blob est actuellement de 5 To dans Stockage Blob Azure. Limites supplémentaires s’appliquent dans Media Services basée sur les tailles de machines virtuelles qui sont utilisés par le service. La limite de taille s’applique aux fichiers que vous téléchargez et également les fichiers qui sont générés à la suite de traitement (encodage ou l’analyse) de Media Services. Si votre fichier source est supérieur à 260 Go, votre travail échouera probablement. 

Le tableau suivant indique les limites sur réservées multimédia unités S1, S2 et S3. Si votre fichier source est supérieur à la limite définie dans la table, votre travail d’encodage échoue. Si vous encodez des sources de résolution 4K de longue durée, vous devez utiliser des unités réservées multimédia de S3 pour optimiser les performances nécessité. Si vous avez du contenu 4K qui dépasse la limite de 260 Go sur les unités réservées multimédia S3, contactez-nous à l’adresse amshelp@microsoft.com pour les solutions d’atténuation possibles prendre en charge votre scénario.

|Type d’unité réservée de média   |Taille maximale en entrée (Go)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Ce nombre comprend les travaux en file d’attente, terminés, actifs et annulés. Il n’inclut pas les travaux supprimés. 

Les enregistrements de travaux de votre compte qui ont plus de 90 jours sont automatiquement supprimés, même si le nombre total d’enregistrements est inférieur au quota maximal. 

<sup>3</sup> sorties live démarrer lors de la création et d’arrêter quand supprimé.

<sup>4</sup> Les comptes de stockage doivent provenir du même abonnement Azure.

<sup>5</sup> lorsque vous utilisez un personnalisé [stratégie de diffusion en continu](https://docs.microsoft.com/rest/api/media/streamingpolicies), vous devez concevoir un ensemble limité de telles stratégies pour votre compte Media Services et leur réutilisation pour votre StreamingLocators chaque fois que le chiffrement même options et protocoles sont nécessaires. Vous ne devez pas créer une stratégie de diffusion en continu pour chaque localisateur de diffusion en continu.

<sup>6</sup> localisateurs de diffusion en continu ne sont pas conçus pour gérer le contrôle d’accès par utilisateur. Pour accorder différents droits d’accès aux utilisateurs, utilisez les solutions de gestion des droits numériques (DRM).

## <a name="support-ticket"></a>Ticket de support

Pour les ressources qui ne sont pas fixes, vous pouvez demander d’augmenter les quotas en ouvrant un [ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Incluez des informations détaillées dans la requête sur les modifications souhaitées pour les quotas, les scénarios de cas d’usage et les régions requises. <br/>Ne créez **pas** d’autres comptes Azure Media Services pour obtenir des limites supérieures.

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](media-services-overview.md)
