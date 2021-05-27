---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: b5d950aca0090067d426457cc09495fd0b8344b5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110383747"
---
1. Dans Visual Studio Code, accédez à src/edge. Vous verrez le fichier .env que vous avez créé, ainsi que quelques fichiers de modèle de déploiement.

   Le modèle de déploiement fait référence au manifeste de déploiement du périphérique avec des valeurs d’espace réservé. Le fichier .env contient les valeurs de ces variables.
2. Ensuite, accédez au dossier src/cloud-to-device-console-app. Vous y voyez le fichier appsettings.json que vous avez créé, ainsi que quelques autres fichiers :

   - c2d-console-app.csproj : Il s’agit du fichier projet pour Visual Studio Code.
   - operations.json : Ce fichier liste les différentes opérations que doit exécuter le programme.
   - Program.cs : Cet exemple de code de programme :
     - Il charge les paramètres de l’application.
     - Appelle les méthodes directes du module Azure Video Analyzer pour créer la topologie, instancier le pipeline et l’activer.
     - Se met en pause pour vous permettre d’examiner la sortie de pipeline dans la fenêtre **TERMINAL** et les événements envoyés au hub IoT dans la fenêtre **SORTIE**.
     - Désactive et supprime le pipeline en direct, supprime la topologie.
