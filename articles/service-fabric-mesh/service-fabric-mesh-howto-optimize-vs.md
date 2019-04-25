---
title: Optimiser les performances de Visual Studio pour les projets Azure Service Fabric Mesh | Microsoft Docs
description: Optimiser les performances de Visual Studio pour les applications Azure Service Fabric Mesh
services: service-fabric-mesh
keywords: Optimiser les performances de débogage
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: f7a0cb47ad8010bd54a817e9990221b320cde541
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419048"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optimiser les performances de Visual Studio pour les projets Service Fabric Mesh

Cet article vous montre comment optimiser les performances de Visual Studio pour les projets Service Fabric Mesh afin que votre première exécution de débogage (F5) soit beaucoup plus rapide.  

## <a name="change-visual-studio-settings"></a>Modifier les paramètres Visual Studio
 
Dans Visual Studio, sous **Outils** > **Options**  > **Outils Service Fabric Mesh** > **Général**, vous pouvez ajuster les paramètres suivants :

- L’utilisation de l’option **Tirer (pull) les images Docker obligatoires à l’ouverture du projet** accélère considérablement votre premier débogage (F5) en démarrant le processus de téléchargement d’image pendant le chargement du projet.  
- L’utilisation de l’option **Déployer l’application à l’ouverture du projet** accélère considérablement votre premier débogage (F5) en démarrant le processus de déploiement à l’ouverture du projet.  
- L’option **Supprimer l’application à la fermeture du projet** libère les ressources (processeur, mémoire RAM) allouées à l’application en supprimant l’application Mesh à la fermeture du projet.  

Si vous voyez des messages du type « pulling images » (tirage des images), « warming up » (mise en route) ou « removing application » (suppression de l’application) concernant Visual Studio dans la fenêtre de sortie Service Fabric Tools, ceux-ci font référence aux paramètres ci-dessus. Vous pouvez désactiver ces paramètres.

## <a name="next-steps"></a>Étapes suivantes

Lisez le [tutoriel Déboguer une application Mesh](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)