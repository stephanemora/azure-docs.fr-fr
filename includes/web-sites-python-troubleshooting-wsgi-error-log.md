---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270192"
---
Si Python rencontre une erreur lors du démarrage de votre application, seule une page d’erreur simple est retournée (par exemple « La page ne peut pas être affichée, car une erreur de serveur interne s’est produite. »).

Pour capturer les erreurs d’application Python :

1. Dans le portail Azure, dans votre application web, sélectionnez **Paramètres**.
2. Sous l’onglet **Paramètres**, sélectionnez **Paramètres de l’application**.
3. Sous **Paramètres de l’application**, entrez la paire clé/valeur suivante :
    * Clé : WSGI_LOG
    * Valeur : D:\home\site\wwwroot\logs.txt (entrez le nom de fichier choisi)

Les erreurs doivent maintenant être journalisées dans le fichier logs.txt du dossier wwwroot.
