---
title: Azure Internet Analyzer | Microsoft Docs
description: Informations de référence sur la résolution des problèmes pour Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 74bf0422bbe6c2c1c84365c1e8f9329a01ff9fdd
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75909035"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Résolution des problèmes liés à Azure Internet Analyzer

Cet article présente les étapes à suivre pour résoudre les problèmes courants liés à Internet Analyzer.

## <a name="azure-portal"></a>Portail Azure
**« Des mesures insuffisantes ont été collectées pour cette carte de performance » dans la section Cartes de performance**

Notez les points suivants :
- Le script client doit être incorporé dans un site web **HTTPS**. Les mesures ne seront pas collectées si le script s’exécute dans un site web en texte clair (**http://** ) ou local (**file://** ).
- Les données de mesure sont collectées seulement si le script client du profil Internet Analyzer est intégré à une application qui reçoit un trafic utilisateur réel. Le trafic synthétique (par exemple, les tests de performances WebApp Azure) n’exécute généralement pas le code JavaScript incorporé et ne génère donc aucune mesure.
- Les séries chronologiques étant générées une fois par heure, vous devez patienter au moins ce délai pour que les nouvelles données de mesure s’affichent.
- Les cartes de performance sont générées tous les jours (à la fin de chaque jour, heure UTC).
- Les cartes de performance sont générées seulement si plus de 100 mesures sont collectées pour la combinaison de filtres sélectionnée (test, période, pays, etc.).

## <a name="next-steps"></a>Étapes suivantes
Consultez la [FAQ sur Internet Analyzer](internet-analyzer-faq.md)
