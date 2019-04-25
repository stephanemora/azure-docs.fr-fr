---
title: Événement en direct Azure Media Services et magnétoscope numérique cloud | Microsoft Docs
description: Cet article explique en quoi consiste une sortie en direct et comment utiliser un magnétoscope numérique cloud.
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
ms.openlocfilehash: 4dd14587ec7e1473953981c1ef8c32c59eb9a1d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322333"
---
# <a name="using-a-cloud-dvr"></a>Utiliser un magnétoscope numérique cloud

Avec une [sortie en direct](https://docs.microsoft.com/rest/api/media/liveoutputs), vous pouvez contrôler les propriétés du flux temps réel sortant, notamment la quantité du flux enregistrée (par exemple, la capacité du magnétoscope numérique cloud) et le fait que les destinataires sont ou non autorisés à démarrer la lecture du flux. La relation entre un **événement en direct** et sa **sortie en direct** est comparable à la diffusion télévisuelle traditionnelle, où un canal (**événement en direct**) représente un flux vidéo constant et un enregistrement (**sortie en direct**) est limité à une plage horaire spécifique (par exemple, un journal télévisé de 18h30 à 19h). Vous pouvez enregistrer une émission de télévision à l’aide d’un magnétoscope numérique (DVR). La fonctionnalité équivalente dans les événements en direct est gérée par la propriété ArchiveWindowLength. Il s’agit d’une période ISO-8601 (par exemple, PTHH:MM:SS), qui spécifie la capacité du magnétoscope numérique. Sa valeur est comprise entre 3 minutes et 25 heures.

## <a name="live-output"></a>Sortie en direct

La valeur **ArchiveWindowLength** détermine dans quelle mesure l’utilisateur peut revenir en arrière à partir de la position actuelle.  **ArchiveWindowLength** spécifie également la longueur maximale que peuvent atteindre les manifestes du client.

Prenons l’exemple d’un match de football en streaming, dont la valeur **ArchiveWindowLength** n’est que de 30 minutes. Un utilisateur qui commencerait à regarder l’événement 45 minutes après le début de la rencontre pourrait revenir au maximum à la marque de 15 minutes. Les **sorties en direct** du match se poursuivent jusqu’à l’arrêt de l’**événement en direct**, mais le contenu situé en dehors de la propriété **ArchiveWindowLength** est systématiquement ignoré par le stockage et n’est pas récupérable. Dans cet exemple, la vidéo qui se trouve entre le début de l’événement et la marque de 15 minutes aurait été éliminée de votre magnétoscope numérique et du conteneur dans le Stockage Blob de l’élément multimédia [Asset](https://docs.microsoft.com/rest/api/media/assets). L’archive, non récupérable, est supprimée du conteneur dans le Stockage Blob Azure.

Chaque **sortie en direct** est associée à un **actif multimédia**, qu’elle utilise pour enregistrer la vidéo dans le conteneur de stockage d’objets blob Azure associé. Pour publier la sortie en direct, vous devez créer un **localisateur de streaming** pour cet **actif multimédia**. Une fois le [localisateur de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) créé, vous pouvez générer une URL de streaming que vous donnerez à vos utilisateurs.

Un **événement en direct** prend en charge jusqu’à trois **sorties en direct** exécutées simultanément, ce qui rend possible la création de trois enregistrements/archives au maximum à partir d’un même flux temps réel. Cela vous permet de publier et d’archiver différentes parties d’un événement en fonction des besoins. Supposons que vous vouliez diffuser un flux linéaire temps réel 24 h/24, 7 j/7 et créer tout au long de la journée des « enregistrements » des différents programmes, qui seront proposés à la demande aux utilisateurs dans le cadre d’un service de replay. Dans ce scénario, vous commencez par créer une sortie en direct principale, avec une fenêtre d’archive courte d’une heure ou moins : ce sera le flux temps réel principal que visionneront les utilisateurs. Vous créez ensuite un **localisateur de streaming** pour cette **sortie en direct** et le publiez sur votre application ou votre site web comme flux « temps réel ». Pendant que l’**événement en direct** est en cours d’exécution, vous pouvez créer par programmation une deuxième **sortie en direct** simultanée au début d’un programme (ou 5 minutes avant pour fournir des descripteurs à supprimer ultérieurement). Cette deuxième **sortie en direct** peut être supprimée 5 minutes après la fin du programme. Avec ce second **actif multimédia**, vous pouvez créer un **localisateur de streaming** pour publier ce programme sous forme d’actif multimédia à la demande dans le catalogue de votre application. Vous pouvez répéter plusieurs fois ce processus pour les autres limites ou points forts du programme que vous souhaitez partager sous forme de vidéos à la demande, tandis que le flux « temps réel » de la première **sortie en direct** continue de diffuser le flux linéaire. 

> [!NOTE]
> Les **sorties en direct** démarrent dès leur création et s’arrêtent à leur suppression. Quand vous supprimez la **sortie en direct**, vous ne supprimez pas l’**actif multimédia** sous-jacent ni le contenu de celui-ci. 
>
> Si vous avez publié l’actif multimédia de la **sortie en direct** à l’aide d’un **localisateur de streaming**, l’**événement en direct** (jusqu’à la longueur de la fenêtre DVR) restera visible jusqu’à l’expiration ou la suppression du **localisateur de streaming**, en fonction de ce qui se produit en premier.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du streaming en direct](live-streaming-overview.md)
- [Didacticiel sur le streaming en direct](stream-live-tutorial-with-api.md)

