---
title: Informations sur les menaces et carte des alertes de sécurité - Azure Security Center
description: Découvrez comment utiliser les fonctionnalités de carte des alertes de sécurité et Threat Intelligence dans Azure Security Center pour identifier les menaces potentielles sur vos machines virtuelles et ordinateurs.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 39835d9d73adcbe474d3b70dfced313e18d1a3b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603417"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Carte des alertes de sécurité et informations sur les menaces
Cet article vous aide à utiliser la carte des alertes de sécurité et la carte Threat Intelligence basée sur les événements de sécurité d’Azure Security Center pour résoudre les problèmes liés à la sécurité.

> [!NOTE]
> Le bouton carte des *événements* de sécurité a été mis hors service le 31 juillet 2019. Pour plus d’informations et connaître les alternatives, consultez [Retrait des fonctionnalités de Security Center (juillet 2019).](security-center-features-retirement-july2019.md#menu_securityeventsmap)


## <a name="how-the-security-alerts-map-works"></a>Fonctionnement de la carte des alertes de sécurité
Security Center vous fournit une carte qui vous aide à identifier les menaces de sécurité qui pèsent sur l’environnement. Par exemple, vous pouvez déterminer si un ordinateur particulier fait partie d’un botnet et connaître l’origine de la menace. Un ordinateur peut devenir un nœud d’un botnet si un attaquant installe de manière illicite un programme malveillant qui interagit secrètement avec les commandes et contrôles qui gèrent le botnet. 

Pour générer cette carte, Security Center utilise des données provenant de nombreuses sources au sein de Microsoft. Security Center utilise ces données afin d’établir la carte des menaces potentielles qui pèsent sur votre environnement. 

Le [processus de réponse aux incidents de sécurité](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) contient une étape consistant à identifier la gravité du ou des systèmes compromis. Pendant cette phase, vous devez effectuer les tâches suivantes :

- Déterminer la nature de l’attaque.
- Déterminer le point d’origine de l’attaque.
- Déterminer le but de l’attaque L’attaque était-elle dirigée contre votre entreprise pour obtenir certaines informations à son sujet ou était-elle aléatoire ?
- Identifier les systèmes qui ont été compromis.
- Identifier les fichiers consultés et déterminer leur sensibilité.

Vous pouvez vous aider de la carte des alertes de sécurité de Security Center pour effectuer ces tâches.

## <a name="access-the-security-alerts-map"></a>Accès à la carte des alertes de sécurité
Pour visualiser les menaces qui pèsent actuellement sur votre environnement, ouvrez la carte des alertes de sécurité :

1. Ouvrez le tableau de bord **Security Center**.
2. Dans le volet gauche, sous **Protection contre les menaces**, sélectionnez **Carte des alertes de sécurité**. La carte s’ouvre.
3. Pour obtenir plus d’informations sur l’alerte et recevoir les étapes de correction à suivre, cliquez sur le point d’alerte sur la carte et suivez les instructions. 
 
La carte des alertes de sécurité est basée sur les alertes. Ces alertes sont basées sur les activités pour lesquelles une communication réseau a été associée à une adresse IP qui a été résolue, afin de déterminer si l’adresse IP est une adresse IP à risque connue (par exemple, un cryptomineur connu) ou une adresse IP qui n’est pas reconnue comme étant risquée. La carte fournit des alertes sur les abonnements que vous avez sélectionnés précédemment dans Azure. 

Les alertes figurant sur la carte sont affichées en fonction de l’emplacement géographique de leur origine détectée ; elles sont également à code de couleurs par niveau de gravité. 
    ![Informations Threat Intelligence](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Voir aussi
Dans cet article, vous avez découvert comment utiliser les informations sur les menaces dans Security Center pour vous aider à identifier une activité suspecte. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Gérer et répondre aux alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Apprenez à gérer les alertes et à répondre aux incidents de sécurité dans Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Comprendre les alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En savoir plus sur les différents types d’alertes de sécurité.
* [Guide de résolution des problèmes d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Apprenez à résoudre les problèmes fréquents dans Azure Security Center.