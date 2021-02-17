---
title: Utiliser le tableau de bord de la console de capteur
description: Le tableau de bord vous permet d’afficher rapidement l’état de sécurité de votre réseau. Il fournit une vue d’ensemble des menaces pour l’ensemble de votre système sur une chronologie, ainsi que des informations sur les appareils associés.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: eb37434213dd756ba5d7137b93a1cd37da5bb9ae
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523632"
---
# <a name="the-dashboard"></a>Le tableau de bord

Le tableau de bord vous permet d’afficher rapidement l’état de sécurité de votre réseau. Il fournit une vue d’ensemble des menaces pour l’ensemble de votre système sur une chronologie, ainsi que des informations sur les appareils associés, dont :

- Des alertes à différents niveaux de gravité :

- Critique

- Majeure

- Secondaire

- Avertissements

- Les deux indicateurs au centre de la page affichent les paquets par seconde (PPS) et les alertes sans accusé de réception (UA). **PPS** est le nombre de paquets reconnus par le système par seconde. **UA** est le nombre d’alertes qui n’ont pas encore été acquittées.

- Liste des alertes sans accusé de réception avec leur description.

- Chronologie avec la description de l’alerte.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="Tableau de bord":::

## <a name="viewing-the-latest-alerts"></a>Affichage des alertes les plus récentes

La jauge Alertes non acquittées (UA) au centre de la page indique le nombre de ces alertes. Pour afficher une liste d’alertes, sélectionnez **Plus d’alertes** en bas de la page du tableau de bord, ou **Alertes** dans le menu latéral.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="Alertes sans accusé de réception":::

## <a name="status-boxes"></a>Zones d’état

Chaque zone d’état est décrite dans cette section.

| Zone d’état et jauges | Description |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="Alertes critiques"::: | **Alertes critiques** : la case en haut au milieu de la page indique le nombre d’alertes critiques. Activez cette case pour afficher les descriptions des alertes sur la chronologie et dans la liste sous les jauges, le cas échéant.                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="Alertes majeures"::: | **Alertes majeures** : la zone située en haut à droite de la page indique le nombre d’alertes majeures. Activez cette case pour afficher les descriptions des alertes sur la chronologie et dans la liste sous les jauges, le cas échéant.                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="Alertes mineures"::: | **Alertes mineures** : la zone située en bas à gauche de la page indique le nombre d’alertes mineures. Activez cette case pour afficher les descriptions des alertes sur la chronologie et dans la liste sous les jauges, le cas échéant.                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="Alertes d'avertissement"::: | **Alertes d’avertissement** : la zone en bas au milieu de la page indique le nombre d’alertes d’avertissement. Activez cette case pour afficher les descriptions des alertes sur la chronologie et dans la liste sous les jauges, le cas échéant.                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="Toutes les alertes"::: | **Toutes les alertes** : la zone située en bas à droite de la page indique le nombre total d’alertes critiques, majeures, mineures et d’avertissement. Activez cette case pour afficher les descriptions des alertes sur la chronologie et dans la liste sous les jauges, le cas échéant. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="Paquets par seconde"::: | **Paquets par seconde (PPS)** : la métrique PPS est un indicateur des performances du réseau. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="Événements sans accusé de réception (UA)"::: | **Événements sans accusé de réception** : cette mesure indique le nombre d’événements sans accusé de réception.

## <a name="using-the-timeline"></a>Utilisation de la chronologie

Les alertes s’affichent le long d’une chronologie verticale qui comprend des informations de date et d’heure.

La chronologie affiche graphiquement les éléments suivants :

- Alertes critiques

- Alertes majeures

- Alertes mineures

- Alertes d'avertissement

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="Graphe de chronologie":::

## <a name="viewing-alerts"></a>Affichage des alertes

Sélectionnez la flèche vers le bas **V** en bas d’une zone d’alerte pour afficher les informations relatives à l’entrée d’alerte et aux appareils.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="Entrée d’alerte et informations sur les appareils":::

- Sélectionnez l’appareil pour afficher la carte en mode physique. Les appareils concernés sont mis en surbrillance.

- Cliquez n’importe où dans la zone d’alerte pour afficher des détails supplémentaires sur l’alerte. Une fenêtre contextuelle s’affiche comme celle ci-dessous.

- Sélectionnez :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: pour exporter un fichier CSV à propos de l’alerte.

- Administrateurs et analystes de la sécurité uniquement : sélectionnez :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="Accuser réception de tout"::: pour **accuser réception de toutes les alertes associées**.

- Sélectionnez :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF"::: pour télécharger un rapport d’alerte sous forme de fichier PDF.

- Sélectionnez :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="Épingler"::: pour épingler ou désépingler l’alerte. Si vous sélectionnez l’option pour épingler, elle est ajoutée à la fenêtre **Alertes épinglées** de l’écran **Alertes**.

- Sélectionnez :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="Télécharger"::: pour analyser l’alerte en téléchargeant le fichier PCAP associé contenant une analyse de protocole réseau.

- Sélectionnez :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="Cloud"::: pour télécharger un fichier PCAP associé filtré qui contient uniquement les paquets pertinents pour l’alerte, réduisant ainsi la taille du fichier de sortie et permettant une analyse plus ciblée. Vous pouvez l’afficher à l’aide de [Wireshark](https://www.wireshark.org/).

- Sélectionnez :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="Navigation"::: pour accéder à la chronologie de l’événement au moment de l’alerte demandée. Cela vous permet d’évaluer d’autres événements qui peuvent se produire autour de l’alerte spécifique.

- Administrateurs et analystes de la sécurité uniquement : modifiez l’état de l’alerte de non acquitté à acquitté. Sélectionnez Découvrir pour approuver l’activité détectée.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="Connectivité Internet non autorisée détectée":::

## <a name="next-steps"></a>Étapes suivantes

[Utiliser des alertes sur votre capteur](how-to-work-with-alerts-on-your-sensor.md)
