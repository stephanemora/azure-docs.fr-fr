---
author: WenJason
ms.author: digimobile
ms.service: cognitive-services
ms.topic: include
origin.date: 01/02/2019
ms.date: 01/28/2019
ms.openlocfilehash: 03ec8740a4cf36bf3d09dade8a24b155c09d1299
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60327422"
---
Les paramètres pour le conteneur sont hiérarchiques et tous les conteneurs sur l’ordinateur hôte utilisent une hiérarchie partagée.

Vous pouvez utiliser l’une des options suivantes pour spécifier les paramètres :

* [Variables d’environnement](#environment-variable-settings)
* [Arguments de ligne de commande](#command-line-argument-settings)

Les valeurs de variable d’environnement remplacent les valeurs d’argument de ligne de commande, qui elles-mêmes remplacent les valeurs par défaut de l’image conteneur. Si vous spécifiez des valeurs différentes dans une variable d’environnement et dans un argument de ligne de commande pour le même paramètre de configuration, la valeur de la variable d’environnement est utilisée par le conteneur instancié.

|Precedence|Emplacement du paramètre|
|--|--|
|1|Variable d’environnement| 
|2|Ligne de commande|
|3|Valeur par défaut d’image conteneur|

### <a name="environment-variable-settings"></a>Paramètres de variables d’environnement

L’utilisation de variables d’environnement offre les avantages suivants :

* Plusieurs paramètres peuvent être configurés.
* Plusieurs conteneurs peuvent utiliser les mêmes paramètres.

### <a name="command-line-argument-settings"></a>Paramètres d’arguments de ligne de commande

L’utilisation d’arguments de ligne de commande présente l’avantage de permettre à chaque conteneur d’utiliser différents paramètres.
