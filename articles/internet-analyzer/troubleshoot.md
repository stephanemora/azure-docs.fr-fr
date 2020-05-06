---
title: Azure Internet Analyzer | Microsoft Docs
description: Informations de référence sur la résolution des problèmes pour Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77069215"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Résolution des problèmes liés à Azure Internet Analyzer

Cet article présente les étapes à suivre pour résoudre les problèmes courants liés à Internet Analyzer.

## <a name="things-to-keep-in-mind"></a>Points à prendre en compte
- Le script client doit être incorporé dans un site web **HTTPS**. Les mesures ne seront pas collectées si le script s’exécute dans un site web en texte clair (**http://** ) ou local (**file://** ).
- Les données de mesure sont collectées seulement si le script client du profil Internet Analyzer est intégré à une application qui reçoit un trafic utilisateur réel. Le trafic synthétique (par exemple, les tests de performances WebApp Azure) n’exécute généralement pas le code JavaScript incorporé et ne génère donc aucune mesure.

## <a name="azure-portal"></a>Portail Azure
**« Aucune carte de performance n’a été générée pour la combinaison de filtres sélectionnée » dans la section Cartes de performance**
- Les cartes de performance sont générées tous les jours (à la fin de chaque jour, heure UTC).
- Les cartes de performance sont générées seulement si plus de 100 mesures sont collectées pour la combinaison de filtres sélectionnée (test, période, pays, etc.).

**Le « Nombre total de mesures » est égal à zéro pour l’un ou les deux points de terminaison d’un test**
- Les séries chronologiques et les nombres de mesures étant générés une fois par heure, vous devez patienter au moins ce délai pour que les nouvelles données de mesure s’affichent.
- Internet Analyzer comptabilise uniquement les mesures réussies (par exemple, les réponses HTTP 200) pour son analyse. Si l’un ou les deux points de terminaison d’un test sont inaccessibles ou retournent un code HTTP non-200, ils s’affichent avec des mesures totales égales à zéro.

## <a name="next-steps"></a>Étapes suivantes
Consultez la [FAQ sur Internet Analyzer](internet-analyzer-faq.md)
