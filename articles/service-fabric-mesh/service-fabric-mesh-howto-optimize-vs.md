---
title: Optimiser Visual Studio pour Azure Service Fabric Mesh
description: Cet article vous montre comment optimiser les performances de Visual Studio pour les projets Service Fabric Mesh afin que votre première exécution de débogage (F5) soit beaucoup plus rapide.
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497983"
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