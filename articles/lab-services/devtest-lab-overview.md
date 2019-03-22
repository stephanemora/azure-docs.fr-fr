---
title: À propos d’Azure DevTest Labs | Microsoft Docs
description: Découvrez comment DevTest Labs peut faciliter la création, la gestion et la surveillance des machines virtuelles Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: 599b4325e0eb9aa7f6ccca2626c6d4b14f38149d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339327"
---
# <a name="about-azure-devtest-labs"></a>À propos d’Azure DevTest Labs
Azure DevTest Labs est un service qui permet aux développeurs d’une équipe libre-service efficacement des machines virtuelles et/ou des ressources PaaS dont ils ont besoin pour développer, tester, des formations et démonstrations etc. sans attendre que les approbations constante sur les outils dont ils ont besoin. 

Le laboratoire comprend déjà bases préconfigurés ou de modèles Resource Manager avec tous les outils nécessaires et les logiciels qui permet aux développeurs de créer des environnements. Les développeurs peuvent créer leurs environnements en quelques minutes, par opposition à des heures ou jours. 

À l’aide de DevTest Labs, vous pouvez tester la dernière version de votre application en effectuant les tâches suivantes :

- Approvisionnant rapidement des environnements Windows et Linux à l’aide des artefacts et des modèles réutilisables
- Intégrez facilement votre pipeline de déploiement avec DevTest Labs pour approvisionner des environnements à la demande
- Monter en puissance votre test en approvisionnant plusieurs agents de test de charge et créez des environnements préprovisionnés pour la formation et de démonstrations.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>Fonctionnalités
Dev/test fournit les fonctionnalités suivantes pour les développeurs qui travaillent avec des machines virtuelles :

- Créer une machine virtuelle rapidement en suivant le moins de cinq étapes simples.
- Choisissez parmi une liste des bases de machines virtuelles qui sont configurées, approuvé et autorisé par le responsable d’équipe ou le centre informatique.
- Créer des ordinateurs à partir d’images personnalisées créées au préalable que vous disposez de tous les logiciels et outils sont installés dans l’image. 
- Créer des machines à partir de formules qui sont essentiellement des images personnalisées et une build la plus récente du logiciel qui est installé au moment de la création de la machine virtuelle.
- Installer les artefacts qui sont des extensions déployées sur la machine virtuelle une fois qu’elle est configurée.
- Configurer arrêt automatique et auto des planifications de démarrage sur les machines pour disposer d’arrêt à la fin de la journée et puis jusqu'à et en cours d’exécution lendemain matin.
- Revendication simplement un ordinateur virtuel créé au préalable sans avoir à passer par le processus de création de la machine. 

Dev/test fournit les fonctionnalités suivantes pour les développeurs qui travaillent avec les environnements PaaS :

- Créez des environnements PaaS Azure Resource Manager suivant rapidement moins de trois étapes simples.
- Choisissez parmi une liste organisée des modèles Resource Manager, qui sont configurés, approuvé et autorisé par le responsable d’équipe ou le centre informatique.
- Créez un groupe de ressources vide (Sandbox) à l’aide d’un modèle Resource Manager pour Explorer l’ensemble d’Azure tout en restant toujours dans le contexte du laboratoire.
- DevTest Labs offre les avantages suivants lors de la création, de la configuration et de la gestion des environnements de test et de développement dans le cloud :

En dehors d’un modèle en libre-service pour les développeurs d’une équipe, le service permet centrale informatique pour contrôler des déchets, d’optimiser les coûts sur les ressources et de respecter les budgets en effectuant les tâches suivantes : 

- Automatique et arrêt automatique de paramètre démarrent les planifications sur les machines virtuelles.
- Définition des stratégies sur le nombre d’utilisateurs de machines virtuelles peut créer.
- Définition des stratégies sur les tailles de machines virtuelles et de la galerie aux utilisateurs d’images peut être sélectionnés.
- Suivi des coûts et en définissant les cibles sur le laboratoire.
- Recevoir une notification sur des coûts projetés élevés pour le laboratoire afin que vous puissiez prendre les mesures nécessaires. 

DevTest Labs offre les avantages suivants dans la création, la configuration et la gestion des environnements dans le cloud :

## <a name="cost-control-and-governance"></a>Gouvernance et contrôle des coûts
DevTest Labs facilite pour contrôler les coûts en vous permettant d’effectuer les tâches suivantes :

- Définir des stratégies sur votre laboratoire - telles que le nombre de machines virtuelles (VM) par utilisateur et le nombre de machines virtuelles par laboratoire. 
- Créer des stratégies pour arrêter et démarrer des machines virtuelles automatiquement.
- Vous permet de suivre les coûts sur les machines virtuelles et les ressources PaaS opérationnelle à l’intérieur de votre laboratoire afin que vous pouvez rester au sein de votre budget prédéfini. 
- Permet aux développeurs de rester dans le contexte du laboratoire afin qu’ils ne finissent pas mise en place toutes les ressources en dehors de celle-ci dans le groupe de ressources sous-jacent ou un abonnement.

## <a name="quickly-get-to-ready-to-test"></a>Vos applications sont prêtes pour le test en un clin d’œil
DevTest Labs vous permet de créer des environnements pré-approvisionnés avec tous les éléments dont votre équipe a besoin pour commencer à développer et à tester des applications. Revendication simplement les environnements où la dernière génération correcte de votre application est installée et commencer à travailler immédiatement. Vous pouvez également utiliser des conteneurs pour créer plus rapidement et facilement des environnements.

## <a name="create-once-use-everywhere"></a>Créez, réutilisez
Capturez et partagez des modèles d’environnement PaaS et des artefacts au sein de votre équipe ou organisation, tout cela dans le contrôle de code source - pour créer des développeurs et facilement des environnements de test.

## <a name="worry-free-self-service"></a>Libre-service convivial
Dev/test permet à vos développeurs et testeurs rapidement et facilite la création de machines virtuelles (IaaS) et les ressources PaaS quelques clics à l’aide d’un ensemble de ressources préconfigurées pour eux.

## <a name="use-iaas-and-paas-resources"></a>Utiliser des ressources IaaS et PaaS 
DevTest Labs permet également aux développeurs d’étendre les ressources PaaS telles que des applications Web, les clusters Service Fabric, les batteries de serveurs SharePoint et ainsi de suite. dans le laboratoire à l’aide un modèle Resource Manager. Utiliser les modèles Resource Manager à partir de notre référentiel public d’environnement ou connecter votre propre référentiel Git pour prendre en main sur PaaS laboratoires le laboratoire. Vous pouvez également suivre les coûts sur ces ressources pour respecter vos budgets prédéfinis. 

## <a name="integrate-with-your-existing-toolchain"></a>Intégrez votre chaîne d’outils existante
Utiliser des plug-ins prédéfinis ou notre API pour approvisionner des environnements de développement/Test directement à partir de votre outil préféré l’intégration continue (CI), l’environnement de développement (IDE) intégré, ou d’un pipeline de mise en production automatisé. Vous pouvez également utiliser notre outil en ligne de commande complet.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 

- Pour en savoir plus sur DevTest Labs, consultez [concepts de DevTest Labs](devtest-lab-concepts.md)
- Pour une procédure pas à pas des instructions détaillées, consultez [didacticiel : Configurer un laboratoire à l’aide d’Azure DevTest Labs](tutorial-create-custom-lab.md)


