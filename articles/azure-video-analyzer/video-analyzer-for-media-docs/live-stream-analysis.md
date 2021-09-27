---
title: Analyse de stream en direct à l’aide d’Azure Video Analyzer for Media (anciennement Video Indexer)
description: Cet article explique comment effectuer une analyse de stream en direct à l’aide d’Azure Video Analyzer for Media (anciennement Video Indexer).
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 18017bbe4d83d11e360efebd7d69eed8d67ae15a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627441"
---
# <a name="live-stream-analysis-with-video-analyzer-for-media"></a>Analyse de stream en direct avec Video Analyzer for Media

Azure Video Analyzer for Media (anciennement Video Indexer) est un service Azure conçu pour extraire des insights approfondis à partir de fichiers vidéo et audio hors connexion. Cela permet d’analyser un fichier multimédia donné, créé à l’avance. Toutefois, dans certains cas d’utilisation, il est important d’obtenir le plus vite possible les insights médias d’un flux en direct pour débloquer des cas d’usage et opérationnels urgents. Par exemple, les riches métadonnées d’un stream en direct peuvent servir à des producteurs de contenu pour automatiser la production télévisuelle.

Une solution décrite dans cet article permet aux clients d’utiliser Video Analyzer for Media dans des résolutions en quasi-temps réel sur des streams en direct. Le retard d’indexation peut n’être que de quatre minutes avec cette solution, selon les blocs de données indexés, la résolution d’entrée, le type de contenu et la puissance de calcul utilisés pour ce processus.

![Métadonnées Azure Video Analyzer for Media sur le stream en direct](./media/live-stream-analysis/live-stream-analysis01.png)

*Figure 1 – Exemple de lecteur affichant les métadonnées Video Analyzer for Media sur le stream en direct*

La [solution d’analyse de stream](https://aka.ms/livestreamanalysis) proposée utilise Azure Functions et deux applications logiques pour traiter un programme en direct à partir d’un canal live dans Azure Media Services avec Video Analyzer for Media. Le résultat est affiché dans le lecteur multimédia Azure qui montre le stream obtenu en quasi-temps réel.

Au niveau supérieur, deux étapes principales entrent en jeu. La première étape s’exécute toutes les 60 secondes, crée un sous-clip des dernières 60 secondes de lecture, génère une ressource à partir de cela et indexe la ressource avec Video Analyzer for Media. Ensuite, la seconde étape est appelée une fois l’indexation terminée. Les insights capturés sont traités, envoyés à Azure Cosmos DB et le sous-clip indexé est supprimé.

Cet exemple de lecteur lit le stream en direct et obtient les insights à partir d’Azure Cosmos DB, à l’aide d’une fonction Azure dédiée. Il affiche les métadonnées et les miniatures synchronisées avec la vidéo en direct.

![Les deux applications logiques qui traitent le stream en direct chaque minute dans le cloud](./media/live-stream-analysis/live-stream-analysis02.png)

*Figure 2 – Les deux applications logiques qui traitent le stream en direct chaque minute dans le cloud.*

## <a name="step-by-step-guide"></a>Guide pas à pas 

Vous trouverez le code complet et un guide pas à pas pour déployer les résultats dans [Projet GitHub d’analyse de contenu multimédia en direct avec Video Analyzer for Media](https://aka.ms/livestreamanalysis). 

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de Video Analyzer for Media](video-indexer-overview.md)
