---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 8122a9c001ce56edefaa0215fc20fe183d2cd00c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368290"
---
Si vous rencontrez l’un de ces problèmes, suivez les conseils ci-dessous.

|Problème  |Résolution  |
|---------|---------|
|Seuls des jeux de données créés dans des magasins de données d’objets blob peuvent être utilisés.     |  Il s’agit d’une limitation connue de la version actuelle.       |
|Une fois la création effectuée, le projet affiche le message « Initialisation » pendant une longue période.     | Actualisez la page manuellement. L’initialisation doit se faire à environ 20 points de données par seconde. L’absence d’actualisation automatique est un problème connu.         |
|Éléments récemment étiquetés non visibles dans la révision des données.     |   Pour charger tous les éléments étiquetés, choisissez le bouton **Premier**. Le bouton **Premier** vous ramène au début de la liste, mais charge toutes les données étiquetées.      |
|Impossible d’affecter un ensemble de tâches à un étiqueteur spécifique.     |   Il s’agit d’une limitation connue de la version actuelle.  |