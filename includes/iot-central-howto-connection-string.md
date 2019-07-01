---
title: Fichier Include
description: Fichier Include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177189"
---
1. Utilisez l’utilitaire de ligne de commande `dps-keygen` pour générer une chaîne de connexion :

    Pour installer l’[utilitaire de génération de clés](https://github.com/Azure/dps-keygen), exécutez la commande suivante :

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Pour générer une chaîne de connexion, exécutez la commande suivante en utilisant les informations de connexion que vous avez précédemment notées :

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Copiez la chaîne de connexion issue de la sortie `dps-keygen` pour l’utiliser dans votre code d’appareil.