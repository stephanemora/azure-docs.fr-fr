---
title: Événement LiveEvent Azure Media Services et magnétoscope numérique cloud | Microsoft Docs
description: Cet article explique en quoi consiste une sortie LiveOutput et comment utiliser un magnétoscope numérique cloud.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/14/2019
ms.author: juliako
ms.openlocfilehash: 8543f00ccaecd8fd3f46132b05c2af925e6de10a
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352357"
---
# <a name="using-a-cloud-dvr"></a>Utiliser un magnétoscope numérique cloud

Avec une sortie [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs), vous pouvez contrôler les propriétés du flux temps réel sortant, notamment la quantité à enregistrer (par exemple, la capacité du magnétoscope numérique cloud) et le fait que les destinataires sont ou non autorisés à démarrer la lecture du flux. La relation entre un événement **LiveEvent** et sa sortie **LiveOutput** est comparable à la diffusion télévisuelle traditionnelle, où un canal (**LiveEvent**) représente un flux vidéo constant et un enregistrement (**LiveOutput**) est limité à une plage horaire spécifique (par exemple, un journal télévisé de 18 h 30 à 19 h). Vous pouvez enregistrer une émission de télévision à l’aide d’un magnétoscope numérique (DVR). La fonctionnalité LiveEvent équivalente est gérée par la propriété ArchiveWindowLength. Il s’agit d’une période ISO-8601 (par exemple, PTHH:MM:SS), qui spécifie la capacité du magnétoscope numérique. Sa valeur est comprise entre 3 minutes et 25 heures.

## <a name="liveoutput"></a>Sortie en temps réel

La valeur **ArchiveWindowLength** détermine dans quelle mesure l’utilisateur peut revenir en arrière à partir de la position actuelle.  **ArchiveWindowLength** spécifie également la longueur maximale que peuvent atteindre les manifestes du client.

Prenons l’exemple d’un match de football en streaming, dont la valeur **ArchiveWindowLength** n’est que de 30 minutes. Un utilisateur qui commencerait à regarder l’événement 45 minutes après le début de la rencontre pourrait revenir au maximum à la marque de 15 minutes. Les sorties **LiveOutput** du match se poursuivent jusqu’à l’arrêt de l’événement **LiveEvent**, mais le contenu situé en dehors de **ArchiveWindowLength** est systématiquement ignoré par le stockage et n’est pas récupérable. Dans cet exemple, la vidéo qui se trouve entre le début de l’événement et la marque de 15 minutes aurait été éliminée de votre magnétoscope numérique et du conteneur dans le Stockage Blob de l’élément multimédia [Asset](https://docs.microsoft.com/rest/api/media/assets). L’archive, non récupérable, est supprimée du conteneur dans le Stockage Blob Azure.

Chaque sortie **LiveOutput** est associée à un élément **Asset**, qu’elle utilise pour enregistrer la vidéo sur le conteneur de Stockage Blob Azure associé. Pour pouvoir publier la sortie LiveOutput, vous devez créer un localisateur de streaming **StreamingLocator** pour l’élément **Asset**. Une fois que vous aurez créé un localisateur [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), vous pourrez générer une URL de streaming que vous donnerez à vos utilisateurs.

Un événement **LiveEvent** prend en charge jusqu’à trois sorties **LiveOutput** exécutées simultanément, ce qui rend possible la création de trois enregistrements/archives maximum à partir d’un même flux temps réel. Cela vous permet de publier et d’archiver différentes parties d’un événement en fonction des besoins. Supposons que vous vouliez diffuser un flux linéaire temps réel 24 h/24, 7 j/7 et créer tout au long de la journée des « enregistrements » des différents programmes, qui seront proposés à la demande aux utilisateurs dans le cadre d’un service de replay. Dans ce scénario, vous commencez par créer une sortie LiveOutput principale, avec une fenêtre d’archive courte d’une heure ou moins : ce sera le flux temps réel principal que visionneront les utilisateurs. Vous créez ensuite un localisateur **StreamingLocator** pour cette sortie **LiveOutput** et le publiez sur votre application ou votre site web comme flux « temps réel ». Tant l’événement **LiveEvent** est en cours d’exécution, vous pouvez créer programmatiquement une deuxième sortie **LiveOutput** simultanée au début d’un programme (ou cinq minutes avant pour fournir des descripteurs à supprimer ultérieurement). Cette deuxième sortie **LiveOutput** peut être supprimée cinq minutes après la fin du programme. Avec ce second élément **Asset**, vous pouvez créer un nouveau localisateur **StreamingLocator** pour publier ce programme sous forme d’élément multimédia à la demande dans le catalogue de votre application. Vous pouvez répéter plusieurs fois ce processus pour les autres limites ou points forts du programme que vous souhaitez partager sous forme de vidéos à la demande, tandis que le flux « temps réel » de la première sortie **LiveOutput** continue de diffuser le flux linéaire. 

> [!NOTE]
> Les sorties **LiveOutput** démarrent dès leur création et s’arrêtent à leur suppression. Quand vous supprimez une sortie **LiveOutput**, vous ne supprimez pas l’élément **Asset** sous-jacent, ni son contenu. 
>
> Si vous avez publié un **StreamingLocator** sur l’élément multimédia pour **LiveOutput**, l’événement (jusqu’à la longueur de fenêtre DVR) continuera à être visible jusqu’à l’heure de fin du **StreamingLocator** ou jusqu’à ce que lorsque vous supprimiez le localisateur, en fonction de ce qui survient en premier.   
  

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du streaming en direct](live-streaming-overview.md)
- [Didacticiel sur le streaming en direct](stream-live-tutorial-with-api.md)

