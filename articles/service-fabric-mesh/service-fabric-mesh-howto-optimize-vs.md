---
title: Optimiser Visual Studio pour Azure Service Fabric Mesh
description: Cet article vous montre comment optimiser les performances de Visual Studio pour les projets Service Fabric Mesh afin que votre première exécution de débogage (F5) soit beaucoup plus rapide.
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: e676286dca852c94f94e8bd8ff2ab73ee92b1412
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625750"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optimiser les performances de Visual Studio pour les projets Service Fabric Mesh

> [!IMPORTANT]
> La préversion d’Azure Service Fabric Mesh a été mise hors service. Les nouveaux déploiements par le biais de l’API Service Fabric Mesh ne seront plus autorisés. La prise en charge des déploiements existants se poursuivra jusqu’au 28 avril 2021.
> 
> Pour plus d’informations, consultez [Mise hors service de la préversion d’Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Cet article vous montre comment optimiser les performances de Visual Studio pour les projets Service Fabric Mesh afin que votre première exécution de débogage (F5) soit beaucoup plus rapide.  

## <a name="change-visual-studio-settings"></a>Modifier les paramètres Visual Studio
 
Dans Visual Studio, sous **Outils** > **Options**  > **Outils Service Fabric Mesh** > **Général**, vous pouvez ajuster les paramètres suivants :

- L’utilisation de l’option **Tirer (pull) les images Docker obligatoires à l’ouverture du projet** accélère considérablement votre premier débogage (F5) en démarrant le processus de téléchargement d’image pendant le chargement du projet.  
- L’utilisation de l’option **Déployer l’application à l’ouverture du projet** accélère considérablement votre premier débogage (F5) en démarrant le processus de déploiement à l’ouverture du projet.  
- L’option **Supprimer l’application à la fermeture du projet** libère les ressources (processeur, mémoire RAM) allouées à l’application en supprimant l’application Mesh à la fermeture du projet.  

Si vous voyez des messages du type « pulling images » (tirage des images), « warming up » (mise en route) ou « removing application » (suppression de l’application) concernant Visual Studio dans la fenêtre de sortie Service Fabric Tools, ceux-ci font référence aux paramètres ci-dessus. Vous pouvez désactiver ces paramètres.

## <a name="next-steps"></a>Étapes suivantes

Lisez le [tutoriel Déboguer une application Mesh](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)