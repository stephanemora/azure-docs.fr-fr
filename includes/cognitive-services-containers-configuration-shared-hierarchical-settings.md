---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: e2c8b4e74933df593dbc023dcb886dff80554a1a
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977343"
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
