---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: 559f4eccbf1eb9bd41471937b3f1d6e846812b40
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110383751"
---
1. Dans Visual Studio Code, accédez à src/edge. Vous verrez le fichier .env que vous avez créé, ainsi que quelques fichiers de modèle de déploiement.

   Le modèle de déploiement fait référence au manifeste de déploiement du périphérique avec des valeurs d’espace réservé. Le fichier .env contient les valeurs de ces variables.
2. Ensuite, accédez au dossier src/cloud-to-device-console-app. Vous y voyez le fichier appsettings.json que vous avez créé, ainsi que quelques autres fichiers :

   - operations.json : répertorie les différentes opérations que vous souhaitez que le programme exécute
   - main.py - Exemple de code de programme, qui effectue les opérations suivantes :
     - Il charge les paramètres de l’application.
     - Appelle les méthodes directes du module Azure Video Analyzer pour créer la topologie, instancier le pipeline et l’activer.
     - Se met en pause pour vous permettre d’examiner la sortie de pipeline dans la fenêtre **TERMINAL** et les événements envoyés au hub IoT dans la fenêtre **SORTIE**.
     - Désactive et supprime le pipeline en direct, puis supprime la topologie.
