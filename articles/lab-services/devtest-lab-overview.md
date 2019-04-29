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
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561453"
---
# <a name="about-azure-devtest-labs"></a>À propos d’Azure DevTest Labs
Azure DevTest Labs permet aux développeurs sur les équipes d’efficacement gérer eux-mêmes les machines virtuelles (VM) et des ressources PaaS sans attendre pour les approbations.

Dev/test crée des laboratoires consistant en bases préconfigurés ou des modèles Azure Resource Manager. Ces messages ont tous les outils nécessaires et les logiciels que vous pouvez utiliser pour créer des environnements. Vous pouvez créer des environnements en quelques minutes, par opposition à des heures ou jours.

À l’aide de DevTest Labs, vous pouvez tester les dernières versions de vos applications en effectuant les tâches suivantes :

- Configurer rapidement des environnements Windows et Linux à l’aide des artefacts et des modèles réutilisables.
- DevTest Labs facilite l’intégration de votre pipeline de déploiement pour approvisionner des environnements à la demande.
- Monter en puissance votre test en approvisionnant plusieurs agents de test de charge et créez des environnements préprovisionnés pour la formation et de démonstrations.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Fonctionnalités
Dev/test fournit les fonctionnalités suivantes pour les développeurs qui travaillent avec des machines virtuelles :

- Créer rapidement des machines virtuelles en suivant le moins de cinq étapes simples.
- Choisissez parmi une liste des bases de machines virtuelles qui sont configurées, approuvé et autorisé par le responsable d’équipe ou le centre informatique.
- Créer des machines virtuelles à partir d’images personnalisées créées au préalable que vous disposez de tous les logiciels et outils déjà installés. 
- Créer des machines virtuelles à partir de formules qui sont des images personnalisées essentiellement combinées avec les versions les plus récentes du logiciel qui est installé lorsque les machines virtuelles sont créées. 
- Installer les artefacts qui sont des extensions déployées sur des machines virtuelles après leur configuration.
- Configurer l’arrêt automatique et les planifications sur les machines virtuelles à démarrage automatique.
- Revendication d’une machine virtuelle créée au préalable sans passer par le processus de création.

Dev/test fournit les fonctionnalités suivantes pour les développeurs qui travaillent avec les environnements PaaS :

- Utilisez le Gestionnaire de ressources pour créer rapidement des environnements de PaaS en suivant le moins de trois étapes simples.
- Choisissez parmi une liste organisée des modèles Resource Manager, qui sont configurés et autorisés par le responsable d’équipe ou le centre informatique.
- Créez un groupe de ressources vide (sandbox) à l’aide d’un modèle Resource Manager pour Explorer Azure dans le contexte d’un laboratoire.

DevTest Labs permet également de centrale informatique pour contrôler des déchets, d’optimiser les coûts sur les ressources et de rester au sein de budgets en effectuant les tâches suivantes :  

- Définition de planifications de l’arrêt automatique et le démarrage automatique sur des machines virtuelles.
- Définition du nombre de machines virtuelles que les utilisateurs peuvent créer des stratégies.
- Définition des stratégies sur les tailles et les images que les utilisateurs choisir à partir de la galerie de machines virtuelles.
- Suivi des coûts et la définition des cibles à des ateliers pratiques.
- Recevoir une notification sur des coûts projetés élevés pour les laboratoires afin que vous puissiez les actions nécessaires.

DevTest Labs offre les avantages suivants dans la création, la configuration et la gestion des environnements dans le cloud.

## <a name="cost-control-and-governance"></a>Gouvernance et contrôle des coûts
DevTest Labs facilite pour contrôler les coûts en vous permettant d’effectuer les tâches suivantes :

- [Définir des stratégies sur vos laboratoires](devtest-lab-get-started-with-lab-policies.md), comme le nombre de machines virtuelles par utilisateur ou par laboratoire. 
- Créer [stratégies pour arrêter automatiquement](devtest-lab-set-lab-policy.md) et démarrer des machines virtuelles.
- Suivre les coûts sur les ressources des machines virtuelles et de PaaS filé inscrire à l’intérieur des laboratoires pour respecter [votre budget](devtest-lab-configure-cost-management.md).
- Restez dans le contexte de vos laboratoires, donc vous ne perdez pas les ressources en dehors de leur.

## <a name="quickly-get-to-ready-to-test"></a>Vos applications sont prêtes pour le test en un clin d’œil
DevTest Labs vous permet de créer des environnements préprovisionnés équipés de tout ce dont votre équipe a besoin pour développer et tester des applications. Venez [revendiquer les environnements](devtest-lab-add-claimable-vm.md) où est installée la dernière version appropriée de votre application et l’utilisation de début. Ou utiliser des conteneurs pour la création d’environnement encore plus rapide et plus légers.

## <a name="create-once-use-everywhere"></a>Créez, réutilisez
Capturez et partagez les PaaS [modèles d’environnement](devtest-lab-create-environment-from-arm.md) et [artefacts](add-artifact-repository.md) au sein de votre équipe ou organisation — toutes dans le contrôle de code source, aux développeurs de créer facilement et d’environnements de test.

## <a name="worry-free-self-service"></a>Libre-service convivial
DevTest Labs permet à vos développeurs et testeurs pour rapidement et facilement [créer des machines virtuelles IaaS](devtest-lab-add-vm.md) et [ressources PaaS](devtest-lab-create-environment-from-arm.md) à l’aide d’un ensemble de ressources préconfigurées.

## <a name="use-iaas-and-paas-resources"></a>Utiliser des ressources IaaS et PaaS 
Les développeurs peuvent également faire tourner les ressources PaaS, tels que les clusters Azure Service Fabric, la fonctionnalité Web Apps d’Azure App Service et les batteries de serveurs SharePoint, à l’aide de modèles Resource Manager. Pour prendre en main sur PaaS labs, utiliser les modèles à partir de la [référentiel de l’environnement public](devtest-lab-configure-use-public-environments.md) ou [connecter le laboratoire dans votre propre référentiel Git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Vous pouvez également suivre les coûts sur ces ressources à respecter votre budget.

## <a name="integrate-with-your-existing-toolchain"></a>Intégrez votre chaîne d’outils existante
Utilisation des plug-ins ou l’API pour approvisionner des environnements de développement/test directement à partir de votre choix [outil d’intégration continue (CI)](devtest-lab-integrate-ci-cd-vsts.md), environnement de développement (IDE) intégré ou un pipeline de mise en production automatisé. Vous pouvez également utiliser l’outil de ligne de commande complète.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- Pour en savoir plus sur DevTest Labs, consultez [concepts de DevTest Labs](devtest-lab-concepts.md).
- Pour une procédure pas à pas des instructions détaillées, consultez [didacticiel : Configurer un laboratoire à l’aide d’Azure DevTest Labs](tutorial-create-custom-lab.md).