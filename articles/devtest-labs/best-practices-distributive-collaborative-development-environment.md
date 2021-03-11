---
title: Développement distribué et collaboratif de ressources Azure DevTest Labs
description: Présente les meilleures pratiques à adopter pour configurer un environnement de développement distribué et collaboratif afin de développer des ressources DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: caf4bd13f2ec9c45db392a027db269b492cbd802
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550074"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Meilleures pratiques pour le développement distribué et collaboratif de ressources Azure DevTest Labs
Un développement distribué et collaboratif permet à différentes équipes ou personnes de développer et de gérer une base de code. Pour aboutir, le processus de développement repose sur la possibilité de créer, de partager et d'intégrer des informations. Ce principe de développement clé peut être utilisé dans Azure DevTest Labs. Plusieurs types de ressources sont couramment distribués entre différents laboratoires au sein d’une entreprise. Les différents types de ressources portent sur les deux zones suivantes :

- Ressources stockées en interne dans le laboratoire (basées sur des laboratoires)
- Ressources stockées dans des [référentiels externes connectés au laboratoire](devtest-lab-add-artifact-repo.md) (basées sur des référentiels de code). 

Ce document décrit quelques-unes des meilleures pratiques de collaboration et de distribution entre équipes, tout en assurant la personnalisation et la qualité à tous les niveaux.

## <a name="lab-based-resources"></a>Ressources basées sur des laboratoires

### <a name="custom-virtual-machine-images"></a>Images de machines virtuelles personnalisées
Vous pouvez disposer d'une source commune d’images personnalisées déployées toutes les nuits dans des laboratoires. Pour plus d’informations, consultez [Fabrique d'images](image-factory-create.md).    

### <a name="formulas"></a>Formules
Les [formules](devtest-lab-manage-formulas.md) sont spécifiques au laboratoire et ne n'ont pas de mécanisme de distribution. Les membres du laboratoire se chargent du développement des formules. 

## <a name="code-repository-based-resources"></a>Ressources basées sur des référentiels de code
Il existe deux fonctionnalités différentes basées sur les référentiels de code, artefacts et environnements. Cet article passe en revue les fonctionnalités et explique comment configurer des référentiels et un workflow pour personnaliser les artefacts et environnements disponibles au niveau de l'organisation ou de l'équipe.  Ce workflow est basé sur la [stratégie de création de branche de contrôle de code source](/azure/devops/repos/tfvc/branching-strategies-with-tfvc) standard. 

### <a name="key-concepts"></a>Concepts clés
Les informations sources des artefacts comprennent des métadonnées et des scripts. Les informations sources des les environnements comprennent des métadonnées et des modèles Resource Manager avec fichiers de prise en charge, tels que les scripts PowerShell, les scripts DSC, les fichiers Zip, etc.  

### <a name="repository-structure"></a>Structure des référentiels  
En matière de contrôle de code source, la configuration la plus courante consiste à configurer une structure multiniveau pour stocker les fichiers de code (modèles, Resource Manager, métadonnées et scripts) utilisés dans les laboratoires. Plus précisément, créez différents référentiels pour stocker les ressources gérées par les différents niveaux de l'entreprise :   

- Ressources d’entreprise.
- Ressources d'unité commerciale/de division.
- Ressources spécifiques à l'équipe.

Chacun de ces niveaux est lié à un dépôt distinct au sein duquel la branche primaire doit être de qualité production. Les [branches](/azure/devops/repos/git/git-branching-guidance) de chaque référentiel servent au développement de ces ressources spécifiques (artefacts ou modèles). Cette structure est parfaitement adaptée à DevTest Labs, car vous pouvez facilement connecter simultanément plusieurs référentiels et branches aux laboratoires de l’entreprise. Le nom du référentiel est inclus dans l'interface utilisateur pour éviter toute confusion en présence de noms, descriptions et éditeurs identiques.
     
Le diagramme suivant illustre deux référentiels : un référentiel d'entreprise géré par la division informatique et un autre géré par la division R&D.

![Exemple d'environnement de développement distribué et collaboratif](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Cette structure multiniveau permet un développement tout en maintenant une qualité élevée au niveau de la branche primaire et en disposant de plusieurs dépôts connectés à un laboratoire pour une plus grande flexibilité.

## <a name="next-steps"></a>Étapes suivantes    
Voir les articles suivants :

- Ajouter un référentiel à un laboratoire à l’aide du [portail Azure](devtest-lab-add-artifact-repo.md) ou par le biais d'un [modèle de gestion des ressources Azure](add-artifact-repository.md)
- [Artefacts DevTest Labs](devtest-lab-artifact-author.md)
- [Environnements DevTest Labs](devtest-lab-create-environment-from-arm.md).
