---
title: Azure Internet Analyzer | Microsoft Docs
description: Informations de référence sur la résolution des problèmes pour Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 0c9e203d7e8be7b24c711f73e2152a7745a57dac
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745467"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Résolution des problèmes liés à Azure Internet Analyzer

Cet article présente les étapes à suivre pour résoudre les problèmes courants liés à Internet Analyzer.

## <a name="things-to-keep-in-mind"></a>Points à prendre en compte
- Le script client doit être incorporé dans un site web **HTTPS**. Les mesures ne seront pas collectées si le script s’exécute dans un site web en texte clair (**http://** ) ou local (**file://** ).
- Les données de mesure sont collectées seulement si le script client du profil Internet Analyzer est intégré à une application qui reçoit un trafic utilisateur réel. Le trafic synthétique (par exemple, les tests de performances WebApp Azure) n’exécute généralement pas le code JavaScript incorporé et ne génère donc aucune mesure.

## <a name="azure-portal"></a>Portail Azure
**« Aucune carte de performance n’a été générée pour la combinaison de filtres sélectionnée » dans la section Cartes de performance**
- Les cartes de performance sont générées tous les jours (à la fin de chaque jour, heure UTC).
- Les cartes de performance sont générées seulement si plus de 100 mesures sont collectées pour la combinaison de filtres sélectionnée (test, période, pays/région, etc.).

**Le « Nombre total de mesures » est égal à zéro pour l’un ou les deux points de terminaison d’un test**
- Les séries chronologiques et les nombres de mesures étant générés une fois par heure, vous devez patienter au moins ce délai pour que les nouvelles données de mesure s’affichent.
- Internet Analyzer comptabilise uniquement les mesures réussies (par exemple, les réponses HTTP 200) pour son analyse. Si l’un ou les deux points de terminaison d’un test sont inaccessibles ou retournent un code HTTP non-200, ils s’affichent avec des mesures totales égales à zéro.

## <a name="next-steps"></a>Étapes suivantes
Consultez la [FAQ sur Internet Analyzer](internet-analyzer-faq.md)
