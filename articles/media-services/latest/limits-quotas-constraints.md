---
title: Quotas et limitations dans Azure Media Services v3 | Microsoft Docs
description: Cette rubrique décrit les quotas et les limitations dans Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 5a4f7e31cb17f47e8796ab99c1f8a089339903df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888425"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Quotas et limitations dans Azure Media Services v3

Cet article décrit les quotas et les limitations dans Azure Media Services v3.

| Ressource | Limite par défaut | 
| --- | --- | 
| Éléments multimédias par compte Azure Media Services | 1 000 000|
| Filtres de manifeste dynamique|100|
| JobInputs par travail | 50 (fixe)|
| JobOutputs par travail | 20 (fixe) |
| TransformOutputs dans une transformation | 20 (fixe) |
| Fichiers par JobInput|10 (fixe)|
| Taille du fichier| Dans certains scénarios, la taille maximale des fichiers pris en charge pour le traitement dans Media Services est soumise à une limite. <sup>(1)</sup> |
| Travaux par compte Media Services | 500 000 <sup>(2)</sup> (fixe)|
| Événements en temps réel par compte Media Services |5|
| Comptes Media Services dans un même abonnement | 25 (fixe) |
| Sorties en direct par événement en direct |3 <sup>(3)</sup> |
| Durée maximale de la sortie en direct | 25 heures |
| Comptes de stockage | 100<sup>(4)</sup> (fixe) |
| Points de terminaison de streaming (arrêtés ou en cours d’exécution) par compte Media Services|2 (fixe)|
| Stratégies de diffusion en continu | 100 <sup>(5)</sup> |
| Transformations par compte Media Services | 100 (fixe)|
| Localisateurs de streaming uniques associés à une ressource à un moment donné | 100<sup>(6)</sup> (fixe) |
| Options de stratégie de clé de contenu |30 | 
| Licences par mois pour chacun des types de DRM sur le service de remise de clé Media Services par compte|1 000 000|

<sup>1</sup> La taille maximale prise en charge pour un objet blob est actuellement de 5 To dans Stockage Blob Azure. Des limites supplémentaires sont applicables dans Media Services en fonction des tailles de machine virtuelle utilisées par le service. La limite de taille s’applique aux fichiers que vous chargez et également aux fichiers qui sont générés suite au traitement (encodage ou analyse) par Media Services. Si votre fichier source est supérieur à 260 Go, votre travail échouera probablement. 

Le tableau suivant indique les limites pour les unités réservées Multimédia S1, S2 et S3. Si votre fichier source dépasse la limite définie dans le tableau, votre travail d’encodage échoue. Si vous encodez des sources de résolution 4K de longue durée, vous devez obligatoirement utiliser des unités réservées Multimédia S3 afin d’obtenir les performances nécessaires. Si vous avez un contenu 4K d’une taille supérieure à la limite de 260 Go des unités réservées Multimédia S3, ouvrez un ticket de support.

|Types d’unités réservées Multimédia   |Taille maximale en entrée (Go)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Ce nombre comprend les travaux en file d’attente, terminés, actifs et annulés. Il n’inclut pas les travaux supprimés. 

Les enregistrements de travaux de votre compte qui ont plus de 90 jours sont automatiquement supprimés, même si le nombre total d’enregistrements est inférieur au quota maximal. 

<sup>3</sup> Les sorties en direct démarrent dès leur création et s’arrêtent à leur suppression.

<sup>4</sup> Les comptes de stockage doivent provenir du même abonnement Azure.

<sup>5</sup> Quand vous utilisez une [stratégie de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) personnalisée, vous devez concevoir un ensemble limité de ces stratégies pour votre compte Media Services et les réutiliser pour vos éléments StreamingLocators chaque fois que les mêmes protocoles et options de chiffrement sont nécessaires. Vous ne devez pas créer une stratégie de diffusion en continu pour chaque localisateur de diffusion en continu.

<sup>6</sup> Les localisateurs de streaming ne sont pas conçus pour gérer le contrôle d’accès par utilisateur. Pour accorder différents droits d’accès aux utilisateurs, utilisez les solutions de gestion des droits numériques (DRM).

## <a name="support-ticket"></a>Ticket de support

Pour les ressources qui ne sont pas fixes, vous pouvez demander d’augmenter les quotas en ouvrant un [ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Incluez des informations détaillées dans la requête sur les modifications souhaitées pour les quotas, les scénarios de cas d’usage et les régions requises. <br/>Ne créez **pas** d’autres comptes Azure Media Services pour obtenir des limites supérieures.

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](media-services-overview.md)
