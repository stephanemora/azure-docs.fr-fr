---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/07/2021
ms.author: juliako
ms.openlocfilehash: 2c0a264e087f611551e5f9d1ea52aa85c97dd9e4
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371835"
---
Dans le cadre des prérequis, vous avez téléchargé l’exemple de code dans un dossier. Effectuez les étapes suivantes pour examiner et modifier les exemples de fichiers.

1. Dans Visual Studio Code, accédez à *src/edge*. Votre fichier  *.env* et quelques fichiers de modèle de déploiement s’affichent.
1. Le fichier *deployment.grpcyolov3icpu.template.json* référence le manifeste de déploiement pour l’appareil de périphérie. Il inclut des valeurs d’espace réservé. Le fichier  *.env* inclut les valeurs de ces variables.
1. Accédez au dossier *src/cloud-to-device-console-app*. Ce dernier contient votre fichier *appsettings.json* et quelques autres fichiers :
    
    * c2d-console-app.csproj : fichier projet pour Visual Studio Code.
    * operations.json : liste des opérations que vous voulez que le programme exécute.
    * Program.cs : exemple de code de programme. Ce code :
    
        * Il charge les paramètres de l’application.
        * Appelle les méthodes directes exposées par le module Azure Video Analyzer. Vous pouvez utiliser le module pour analyser des flux vidéo en direct en invoquant ses méthodes directes.
        * S’interrompt pour vous permettre d’examiner la sortie du programme dans la fenêtre **TERMINAL** et d’examiner les événements qui ont été générés par le module dans la fenêtre **SORTIE**.
        * Invoque des méthodes directes pour nettoyer des ressources.
1. Modifiez le fichier operations.json :
    
    * Changez le lien vers le pipeline :<br/>` "topologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/motion-with-grpcExtension/topology.json"`
    * Sous livePipelineSet, modifiez le nom de la topologies de pipeline pour le faire correspondre à la valeur du lien précédent :<br/>`"topologyName" : "EVROnMotionPlusGrpcExtension"`
    * Sous topologyDelete, modifiez le nom :<br/>`"name" : "EVROnMotionPlusGrpcExtension"`
