---
title: Utiliser les services de Platform-as-a-Service (PaaS) dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment utiliser les services de Platform-as-a-Service (Pass) dans Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 865ae0b3f7a7965698a67183a4c820ba71f49cd8
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833908"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Utiliser les services de Platform-as-a-Service (PaaS) dans Azure DevTest Labs
PaaS est pris en charge dans DevTest Labs via la fonctionnalité d’environnements. Les environnements dev/test sont pris en charge par les modèles Azure Resource Manager préconfigurés dans un référentiel Git. Environnements peuvent contenir des ressources PaaS et IaaS. Ils permettent de créer des systèmes complexes qui peuvent inclure des ressources Azure telles que les machines virtuelles, les bases de données, les réseaux virtuels et des applications Web, qui sont personnalisées pour fonctionner ensemble. Ces modèles permettent un déploiement cohérent et améliore la gestion des environnements à l’aide du contrôle de code source. 

A correctement configuré système permet les scénarios suivants : 

- Développeurs indépendants et de plusieurs environnements
- Tests sur différentes configurations de façon asynchrone
- Intégration avec les pipelines intermédiaire et de production sans aucune modification de modèle
- Avoir des ordinateurs de développement et environnements au sein du même environnement améliore la facilité de gestion et les rapports de coût.  

Le fournisseur de ressources DevTest Labs crée des ressources sur le nom de l’utilisateur de laboratoire, donc aucune autorisation supplémentaire à donner à l’utilisateur de laboratoire pour permettre l’utilisation des ressources PaaS. L’illustration suivante montre un cluster Service Fabric en tant qu’un environnement dans le laboratoire.

![Cluster service Fabric en tant qu’un environnement](./media/create-environment-service-fabric-cluster/cluster-created.png)

Pour plus d’informations sur la configuration d’environnements, consultez [créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec des modèles Azure Resource Manager](devtest-lab-create-environment-from-arm.md). DevTest Labs a un référentiel public de modèles Azure Resource Manager que vous pouvez utiliser pour créer des environnements sans avoir à se connecter à une source externe de GitHub par vous-même. Pour plus d’informations sur les environnements publics, consultez [configurer et utiliser des environnements publics dans Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

Dans les grandes organisations, les équipes de développement fournissent généralement des environnements tels que les environnements de test personnalisé/isolé. Il peut exister des environnements qui doivent être utilisées par toutes les équipes au sein d’une unité commerciale ou une division. Le groupe IT souhaiterez peut-être fournir leurs environnements peuvent être utilisées par toutes les équipes dans l’organisation.  

## <a name="customizations"></a>Personnalisations

#### <a name="sandbox"></a>Bac à sable (sandbox) 
Le propriétaire de laboratoire permettre personnaliser des environnements lab pour modifier le rôle de l’utilisateur à partir de **lecteur** à **contributeur** au sein du groupe de ressources. Cette fonctionnalité est dans le **les paramètres de Lab** page sous le **Configuration et stratégies** du laboratoire. Ce changement de rôle permet à l’utilisateur Ajouter ou supprimer des ressources au sein de cet environnement. Si vous souhaitez restreindre davantage l’accès, utilisez les stratégies Azure. Cette fonctionnalité vous permet de personnaliser les ressources ou la configuration sans l’accès au niveau de l’abonnement.

#### <a name="custom-tokens"></a>Jetons personnalisés
Il existe des informations de laboratoire personnalisé qui sont trouve en dehors du groupe de ressources et qui est spécifique aux environnements qui le modèle peut accéder. Voici certains d'entre eux : 

- Identification de réseau de laboratoire
- Lieu
- Compte de stockage dans lequel sont stockés les fichiers de modèles Resource Manager. 
 
#### <a name="lab-virtual-network"></a>Réseau virtuel de laboratoire
Le [connexion d’environnements de réseau virtuel du laboratoire](connect-environment-lab-virtual-network.md) article décrit comment modifier votre modèle Resource Manager à utiliser le `$(LabSubnetId)` jeton. Lorsqu’un environnement est créé, le `$(LabSubnetId)` jeton est remplacé par la première marque de sous-réseau dans lequel le **créer d’utilisation dans une machine virtuelle** option est définie sur **true**. Il permet à notre environnement à utiliser précédemment créé des réseaux. Si vous souhaitez utiliser les mêmes modèles Resource Manager dans les environnements de test en tant qu’intermédiaire et de production, utilisez `$(LabSubnetId)` comme valeur par défaut dans un paramètre de modèle Resource Manager. 

#### <a name="environment-storage-account"></a>Compte de stockage d’environnement
DevTest Labs prend en charge l’utilisation de [imbriqués de modèles Resource Manager](../azure-resource-manager/resource-group-linked-templates.md). Le [[déployer des modèles Azure Resource Manager imbriqués pour les environnements de test](deploy-nested-template-environments.md) article explique comment utiliser `_artifactsLocation` et `_artifactsLocationSasToken` jetons pour créer un URI vers un modèle Resource Manager dans le même dossier qu’ou dans une liste imbriquée dossier du modèle principal. Pour plus d’informations sur ces deux jetons, consultez le **artefacts de déploiement** section de [Azure Resource Manager – Guide des meilleures pratiques](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Expérience utilisateur

## <a name="developer"></a>Développeur
Les développeurs utilisent le même flux de travail pour la création d’une machine virtuelle pour créer un environnement spécifique. Ils sélectionnent l’environnement et l’image de machine et que vous entrez les informations nécessaires par le modèle. Chaque développeur n’ait un environnement permet le déploiement des modifications et le débogage de la boucle interne améliorée. L’environnement peut être créé à tout moment à l’aide du dernier modèle.  Cette fonctionnalité permet les environnements d’être détruit et recréé pour aider à réduire le temps d’arrêt de devoir manuellement de création du système ou de récupération de test d’erreur.  

### <a name="testing"></a>Test
Les environnements dev/test permettent un test indépendant des configurations et de code spécifique en mode asynchrone. La pratique courante consiste à configurer un environnement avec le code à partir de la demande de tirage individuels et démarrer les tests automatisés. Une fois le test automatisé exécuté jusqu'à la fin, n’importe quel test manuel peut être exécutée par rapport à l’environnement spécifique. Ce processus est généralement effectué dans le cadre du pipeline CI/CD. 

## <a name="management-experience"></a>Expérience de gestion

### <a name="cost-tracking"></a>Suivi des coûts
La fonctionnalité de suivi du coût inclut des ressources Azure dans différents environnements dans le cadre de l’ensemble des tendances de coût. Le coût en ressources ne s’interrompt pas les ressources différents au sein de l’environnement, mais affiche l’environnement sous la forme d’un coût unique.

### <a name="security"></a>Sécurité
Un abonnement Azure correctement configuré avec DevTest Labs peut [limiter l’accès aux ressources Azure uniquement par le biais de l’atelier](devtest-lab-add-devtest-user.md). Avec les environnements, un propriétaire de laboratoire peut permettre aux utilisateurs d’accéder aux ressources PaaS avec des configurations approuvées sans autoriser l’accès à d’autres ressources Azure. Dans le scénario où les utilisateurs de laboratoire personnaliser des environnements, le propriétaire de laboratoire peut autoriser un accès collaborateur. Un accès collaborateur permet à l’utilisateur de laboratoire ajouter ou supprimer des ressources Azure uniquement dans le groupe de ressources managé. Elle permet facilite le suivi et de gestion et autoriser un accès utilisateur collaborateur à l’abonnement.

### <a name="automation"></a>Automatisation
Automation est un composant clé de grande échelle, écosystème efficace. Automation est nécessaire pour gérer la gestion ou de plusieurs environnements de suivi entre les abonnements et les laboratoires.

### <a name="cicd-pipeline"></a>Pipeline CI/CD
Services PaaS dans DevTest Labs peuvent améliorer le pipeline CI/CD par avoir concentré déploiements où l’accès est contrôlé par le laboratoire.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants pour plus d’informations sur les environnements : 

- 
- [Créer des environnements de plusieurs machines virtuelles et des ressources PaaS avec les modèles Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurer et utiliser des environnements publics dans Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Créer un environnement avec un cluster autonome Service Fabric dans Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Connecter un environnement de réseau virtuel de votre laboratoire Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Intégrer des environnements à vos pipelines Azure DevOps CI/CD](integrate-environments-devops-pipeline.md)
 





