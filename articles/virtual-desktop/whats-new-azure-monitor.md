---
title: Nouveautés d'Azure Monitor pour Azure Virtual Desktop
description: Nouvelles fonctionnalités et mises à jour produit de l'agent Azure Virtual Desktop.
author: Heidilohr
ms.topic: reference
ms.date: 07/09/2021
ms.author: helohr
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 000fb5a26c34a8fbb9013ad873aa167aa759ae7b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114450874"
---
# <a name="whats-new-in-azure-monitor-for-azure-virtual-desktop"></a>Nouveautés d'Azure Monitor pour Azure Virtual Desktop

Cet article décrit les modifications que nous apportons à chaque nouvelle version d'Azure Monitor pour Azure Virtual Desktop.

Si vous ignorez quelle version d'Azure Monitor vous utilisez actuellement, vous trouverez cette information dans le coin inférieur droit de votre page Insights ou de votre classeur de configuration. Pour accéder à votre classeur, rendez-vous sur [https://aka.ms/azmonwvdi](https://aka.ms/azmonwvdi).

## <a name="how-to-read-version-numbers"></a>Signification des numéros de version

Chaque version d'Azure Monitor pour Azure Virtual Desktop comporte trois chiffres dont la signification est la suivante :

- Le premier chiffre correspond à la version principale. Il est généralement utilisé pour les publications majeures.

- Le deuxième chiffre correspond à la version secondaire. Les versions secondaires sont destinées aux modifications à compatibilité descendante, telles que les avis d'ajout ou de suppression de fonctionnalités.

- Le troisième chiffre correspond à la version du correctif, qui est utilisée pour les petites modifications permettant de corriger un comportement incorrect ou des bogues.

Par exemple, une publication dont le numéro de version est 1.2.31 correspond à la première version principale, à la deuxième version secondaire et au correctif numéro 31.

Lorsque l'un des chiffres augmente, tous les chiffres qui le suivent doivent également changer. Une publication ne possède qu'un seul numéro de version. Cependant, certains numéros de version ne suivent pas les publications. Par exemple, les numéros des correctifs peuvent être quelque peu arbitraires.

## <a name="version-100"></a>Version 1.0.0

Date de publication : 21 mars 2021

Dans cette version, nous avons apporté les modifications suivantes :

- Nous avons introduit sur la page de diagnostic de l'hôte un nouvel indicateur visuel pour les erreurs et avertissements à fort impact provenant du journal des événements de l'agent Azure Virtual Desktop.

- Nous avons supprimé de la configuration par défaut cinq compteurs de performances de processus coûteux. Pour plus d'informations, consultez notre billet de blog sous [Mise à jour des instructions relatives à Azure Monitor pour Azure Virtual Desktop](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/updated-guidance-on-azure-monitor-for-wvd/m-p/2236173).

- Le processus de configuration du journal des événements Windows pour le classeur de configuration est désormais automatisé.

- Le classeur de configuration prend désormais en charge le déploiement automatisé des journaux d'événements Windows recommandés.

- Le classeur de configuration peut désormais installer l'agent Log Analytics et l'espace de travail préféré pour les hôtes de session en dehors de la région du groupe de ressources.

- Le classeur de configuration se présente désormais sous forme d'onglets pour le processus de configuration.

- Nous avons introduit le contrôle de version avec cette mise à jour.

## <a name="next-steps"></a>Étapes suivantes

Pour accéder à la page des nouveautés générales, consultez [Nouveautés d'Azure Virtual Desktop](whats-new.md).

Pour en savoir plus sur Azure Monitor pour Azure Virtual Desktop, consultez [Utiliser Azure Monitor pour Azure Virtual Desktop pour surveiller votre déploiement](azure-monitor.md).
