---
title: Créer une fabrique d’images dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment créer une fabrique d’images personnalisées dans Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: e51c56f54a4e30b5e9094388ed92aa0a62ba0840
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879146"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Créez une fabrique d’image personnalisée dans Azure DevTest Labs
Cet article vous montre comment configurer une fabrique d’images personnalisées à l’aide d’exemples de scripts disponibles dans le [référentiel Git](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Qu’est une fabrique d’images ?
Une fabrique d’images est une solution en tant que code de configuration qui crée et distribue les images automatiquement à intervalles réguliers avec toutes les configurations souhaitées. Les images dans la fabrique d’images sont toujours à jour, et la maintenance en cours est très proche de zéro une fois que l’ensemble du processus est automatisé. Et, comme toutes les configurations requises sont déjà dans l’image, elle enregistre l’heure à partir de la configuration manuellement du système après la création d’une machine virtuelle avec le système d’exploitation de base.

L’accélérateur significatif pour obtenir un bureau de développeur à un état prêt dans DevTest Labs est à l’aide des images personnalisées. L’inconvénient d’images personnalisées est qu’il existe un élément supplémentaire à conserver dans le laboratoire. Par exemple, expirent des versions d’évaluation de produits au fil du temps (ou) mises à jour de sécurité qui vient d’être publiées ne sont pas appliquées, ce qui nous obliger à actualiser régulièrement de l’image personnalisée. Avec une fabrique d’images, vous avez une définition de l’image archivé dans le contrôle de code source et disposer d’un processus automatisé pour produire des images personnalisées en fonction de la définition.

La solution permet à la vitesse de création des machines virtuelles à partir d’images personnalisées tout en éliminant les frais de maintenance supplémentaires. Avec cette solution, vous pouvez automatiquement créer des images personnalisées, les distribuer à d’autres dev/test et mettre hors service les anciennes images. Dans la vidéo suivante, vous en savoir plus sur la fabrique d’images, et comment il est implémenté avec DevTest Labs.  Tous les scripts Azure Powershell sont librement disponible et situé ici : [ https://aka.ms/dtlimagefactory ](https://aka.ms/dtlimagefactory).

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Vue d’ensemble de la solution
La solution permet à la vitesse de création des machines virtuelles à partir d’images personnalisées tout en éliminant les frais de maintenance supplémentaires. Avec cette solution, vous pouvez automatiquement créer des images personnalisées et les distribuer à d’autres dev/test. Vous utilisez Azure DevOps (anciennement Visual Studio Team Services) comme le moteur d’orchestration pour l’automatisation de toutes les opérations dans les DevTest Labs.

![Vue d’ensemble de la solution](./media/create-image-factory/high-level-view-of-solution.png)

Il existe un [Extension VSTS pour dev/test](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) qui vous permet d’exécuter ces étapes individuelles : 

- Créer une image personnalisée
- Créer une machine virtuelle
- Supprimer la machine virtuelle
- Créer l’environnement
- Supprimer l’environnement
- Remplir l’environnement

À l’aide de l’extension de DevTest Labs est un moyen simple de commencer à créer automatiquement des images personnalisées dans DevTest Labs.

Il existe une autre implémentation à l’aide du script PowerShell pour un scénario plus complexe. À l’aide de PowerShell, vous pouvez automatiser entièrement une fabrique d’images en fonction de dev/test qui peut être utilisé dans votre chaîne d’outils de livraison continue (CI/CD) et d’intégration continue. Les principes de suivi dans cette solution de remplacement sont :

- Mises à jour courantes ne doivent nécessitent aucune modification de la fabrique d’images. (par exemple, en ajoutant un nouveau type d’image personnalisée, le retrait automatique anciennes images, en ajoutant un « point de terminaison « dev/test pour recevoir des images personnalisées et ainsi de suite.)
- Les modifications courantes sont soutenues par le contrôle de code source (infrastructure en tant que code)
- Dev/réception des images personnalisées peut ne pas être dans le même abonnement Azure (laboratoires couvrir plusieurs abonnements)
- Scripts PowerShell doivent être réutilisables, donc nous pouvons tourner fabriques supplémentaires en fonction des besoins

## <a name="next-steps"></a>Étapes suivantes
Passer à l’article suivant dans cette section : [Exécuter une fabrique d’images à partir d’Azure DevOps](image-factory-set-up-devops-lab.md)
