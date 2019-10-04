---
title: Comment utiliser des vignettes dans le tableau de bord de l’application d’Azure IoT Central | Microsoft Docs
description: En tant que générateur, découvrez comment utiliser les vignettes dans les tableaux de bord des applications, appareils et ensembles d’appareils.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 26322461e4f53bffff2fb182df6c45ed3b83c4bf
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503770"
---
# <a name="how-to-use-tiles"></a>Comment utiliser des vignettes
Vous pouvez utiliser les vignettes pour personnaliser les tableaux de bord des applications, appareils et ensembles d’appareils. Vous pouvez ajouter plusieurs vignettes au tableau de bord et personnaliser ces vignettes pour afficher des informations pertinentes relatives à votre application. Vous pouvez également redimensionner les vignettes et personnaliser leur disposition sur n’importe quel tableau de bord. La capture d’écran ci-dessous montre le tableau de bord d’application avec différentes vignettes.

![Tableau de bord de l’application](media/howto-use-tiles/image1a.png)


Le tableau suivant résume l’utilisation des vignettes dans Azure IoT Central :

 
| Vignette | tableau de bord | Description
| ----------- | ------- | ------- |
| Lien | Tableaux de bord d’ensemble d’appareils et d’applications |Les vignettes de lien sont des vignettes interactives qui affichent l’en-tête et le texte de description. Utilisez une vignette de lien pour permettre aux utilisateurs d’accéder à une URL associée à votre application. |
| Image | Tableaux de bord d’ensemble d’appareils et d’applications |Les vignettes d’image affichent une image personnalisée et peuvent être interactives. Utilisez une vignette d’image pour ajouter des graphiques à un tableau de bord et éventuellement permettre aux utilisateurs d’accéder à une URL pertinente pour votre application.|
| Étiquette | Tableaux de bord d’applications |Les vignettes d’étiquette affichent un texte personnalisé sur un tableau de bord. Vous pouvez choisir la taille du texte. Utilisez une vignette d’étiquette pour ajouter des informations pertinentes au tableau de bord, telles que des descriptions, des informations de contact ou une assistance|
| Mappage | Tableaux de bord d’ensemble d’appareils et d’applications |Les vignettes de carte affichent l’emplacement et l’état d’un appareil sur une carte. Par exemple, vous pouvez afficher l’endroit où se trouve un appareil et si le ventilateur est allumé.|
| Graphique en courbes | Tableaux de bord des appareils et applications |Les vignettes de graphique en courbes affichent un graphique des mesures agrégées d’un appareil pour une période de temps. Par exemple, vous pouvez afficher un graphique en courbes qui affiche la température moyenne et la pression d’un appareil au cours de la dernière heure|
| Graphique à barres | Tableaux de bord des appareils et applications |Les vignettes de graphique à barres affichent un graphique des mesures agrégées d’un appareil pour une période de temps. Par exemple, vous pouvez afficher un graphique à barres qui affiche la température moyenne et la pression d’un appareil au cours de la dernière heure |
| Historique des événements | Tableaux de bord des appareils et applications |La vignette de l’historique des événements affiche les événements d’un appareil sur une période de temps. Par exemple, vous pouvez l’utiliser pour afficher que tous les changements de température qui se sont produits pour un appareil au cours de la dernière heure. |
| Historique de l'état | Tableaux de bord des appareils et applications |La vignette de l’historique de l’état affiche les valeurs de métriques d’une période de temps. Par exemple, vous pouvez l’utiliser pour afficher que les valeurs de température d’un appareil durant la dernière heure.|
| KPI | Tableaux de bord des appareils et applications | La vignette de KPI affiche une mesure de télémétrie ou d’événement agrégée pour une période de temps. Par exemple, vous pouvez l’utiliser pour afficher la température maximale atteinte par un appareil durant la dernière heure.|
| Dernière valeur connue | Tableaux de bord des appareils et applications |La vignette Dernière valeur connue affiche la dernière valeur d’une mesure de télémétrie ou d’état. Par exemple, vous pouvez utiliser cette vignette pour afficher les mesures de température, de pression et d’humidité les plus récentes d’un appareil.|
| Grille d’ensemble d’appareils | Tableaux de bord d’ensemble d’appareils et d’applications | La grille d’ensemble d’appareils affiche des informations sur l’ensemble d’appareils. Utilisez une vignette de grille d’ensemble d’appareils pour afficher des informations telles que le nom, l’emplacement et le modèle de tous les appareils compris dans l’ensemble d’appareils.|


Pour en savoir plus sur la configuration du tableau de bord de votre application Azure IoT Central, consultez [Configurer le tableau de bord de l’application](howto-configure-homepage.md).
