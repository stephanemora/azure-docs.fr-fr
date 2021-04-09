---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 359c5f93516ea6f0561865bd86e4f51dedb4c3a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94358236"
---
1. Dans Visual Studio Code, accédez à src/edge. Vous verrez le fichier .env que vous avez créé, ainsi que quelques fichiers de modèle de déploiement.

    Le modèle de déploiement fait référence au manifeste de déploiement du périphérique avec des valeurs d’espace réservé. Le fichier .env contient les valeurs de ces variables.
1. Ensuite, accédez au dossier src/cloud-to-device-console-app. Vous y voyez le fichier appsettings.json que vous avez créé, ainsi que quelques autres fichiers :

    * c2d-console-app.csproj : Il s’agit du fichier projet pour Visual Studio Code.
    * operations.json : Ce fichier liste les différentes opérations que doit exécuter le programme.
    * Program.cs : Cet exemple de code de programme :

        * Il charge les paramètres de l’application.
        * Utilise les méthodes directes du module Live Video Analytics sur IoT Edge pour créer une topologie, ainsi que pour instancier le graphe et l’activer.
        * S’interrompt pour vous permettre d’examiner la sortie du graphe dans la fenêtre **TERMINAL** et les événements envoyés au hub IoT dans la fenêtre **SORTIE**.
        * Désactive puis supprime l’instance de graphe, et supprime la topologie du graphe.
