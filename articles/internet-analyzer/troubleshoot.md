---
title: Azure Internet Analyzer | Microsoft Docs
description: Informations de référence sur la résolution des problèmes pour Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a7216e697680bcc049d2ceb4caec74adfc1760b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896895"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Résolution des problèmes liés à Azure Internet Analyzer

Cet article présente les étapes à suivre pour résoudre les problèmes courants liés à Internet Analyzer.

## <a name="azure-portal"></a>Portail Azure
**« Des mesures insuffisantes ont été collectées pour cette carte de performance » dans la section Cartes de performance**

Notez les points suivants :
- Les données de mesure sont collectées seulement si le script client du profil Internet Analyzer est intégré à une application qui reçoit un trafic utilisateur réel. Le trafic synthétique (par exemple, les tests de performances WebApp Azure) n’exécute généralement pas le code JavaScript incorporé et ne génère donc aucune mesure.
- Les séries chronologiques étant générées une fois par heure, vous devez patienter au moins ce délai pour que les nouvelles données de mesure s’affichent.
- Les cartes de performance sont générées tous les jours (à la fin de chaque jour, heure UTC).
- Les cartes de performance sont générées seulement si plus de 100 mesures sont collectées pour la combinaison de filtres sélectionnée (test, période, pays, etc.).

## <a name="next-steps"></a>Étapes suivantes
Consultez la [FAQ sur Internet Analyzer](internet-analyzer-faq.md)
