---
title: Scénarios courants pour l’utilisation d’Azure DevTest Labs
description: Cet article fournit les principaux scénarios pour l’utilisation d’Azure DevTest Labs et deux chemins d’accès générales pour commencer à utiliser le service dans votre organisation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272378"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Scénarios courants pour l’utilisation d’Azure DevTest Labs
Selon les besoins d’une entreprise, dev/test peut être configuré pour répondre aux différents besoins.  Cet article décrit les scénarios les plus courants. Chaque scénario présente des avantages mis à l’aide de DevTest Labs et les ressources à utiliser pour implémenter ces scénarios.  

- Postes de travail de développeur
- Environnements de test
- Sessions de formation, ateliers et hackathons
- Enquêtes sandbox
- Laboratoires de salle de classe

## <a name="developer-desktops"></a>Postes de travail de développeur
Les développeurs ont souvent des exigences distinctes concernant les ordinateurs de développement pour différents projets. Avec DevTest Labs, les développeurs peuvent avoir accès aux ordinateurs virtuels à la demande qui sont configurés pour répondre à leurs scénarios les plus courants. DevTest Labs permet de bénéficier des avantages suivants :

- Les organisations peuvent fournir des ordinateurs de développement courants garantir la cohérence entre les équipes.
- Les développeurs peuvent configurer rapidement leurs ordinateurs de développement à la demande ou [revendiquer une machine existante préconfigurée](devtest-lab-add-claimable-vm.md).
- Les développeurs peuvent approvisionner des ressources d’une manière libre service sans avoir besoin d’autorisations de niveau d’abonnement.
- INFORMATIQUE ou les administrateurs peuvent [prédéfinir la topologie de réseau](devtest-lab-configure-vnet.md) et les développeurs peuvent utiliser directement il de manière simple et intuitive sans nécessiter un accès spécial.
- Les développeurs peuvent facilement [personnaliser](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) leur développement machines en fonction des besoins.
- Les administrateurs peuvent contrôler les coûts en s’assurant que :
    - Les développeurs [Impossible d’obtenir davantage de machines virtuelles](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) que nécessaire pour le développement
    - [Les machines virtuelles sont arrêtées](devtest-lab-set-lab-policy.md#set-auto-shutdown) inutilisée
    - Uniquement [autorisant un sous-ensemble des tailles d’instance de machine virtuelle](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) pour les laboratoires spécifiques
    - [Gestion des coûts cibles et des notifications](devtest-lab-configure-cost-management.md) de chacun d'entre eux.

Pour plus d’informations, consultez [utiliser Azure DevTest Labs pour les développeurs](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Environnements de test
La création et la gestion des environnements de test dans une entreprise peuvent nécessiter un effort considérable. Avec DevTest Labs, environnements de test peuvent être facilement créés, mis à jour ou dupliqués. Il permet aux équipes pour accéder à un environnement entièrement configuré lorsque cela est nécessaire. Dans ce scénario, DevTest Labs offre les avantages suivants :

- Les organisations peuvent fournir des environnements de tests courants garantir la cohérence entre les équipes.
- Les testeurs peuvent tester la dernière version de leur application en approvisionnant rapidement des environnements Windows et Linux à l’aide des modèles réutilisables.
- Administrateurs puissent se connecter à l’atelier pour Azure DevOps pour activer des scénarios DevOps
- Les propriétaires de laboratoire peuvent contrôler les coûts en s’assurant que :
    - [Les machines virtuelles dans des environnements sont arrêtées](devtest-lab-set-lab-policy.md#set-auto-shutdown) inutilisée
    - Uniquement [autorisant un sous-ensemble des tailles d’instance de machine virtuelle pour](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) les laboratoires spécifiques
    - [Gestion des coûts cibles et des notifications](devtest-lab-configure-cost-management.md) de chacun d'entre eux.

Pour plus d’informations, consultez [utiliser Azure DevTest Labs pour la machine virtuelle et PaaS des environnements de test](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Enquêtes sandbox
Souvent, les développeurs examiner différentes technologies ou la conception d’infrastructure. Par défaut, tous les environnements créés avec dev/test sont créés dans leur propre groupe de ressources. L’utilisateur de DevTest Labs obtient uniquement les accès en lecture à ces ressources. Toutefois, pour les développeurs qui ont besoin de davantage de contrôle, un paramètre de laboratoire peut être mis à jour pour donner [droits de contributeur](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) à l’utilisateur d’origine dev/test pour chaque environnement qu’ils créent.  Avec DevTest Labs, les développeurs peuvent recevoir l’autorisation contributeur automatiquement pour les environnements qu’ils créent dans le laboratoire.  Ce scénario permet aux développeurs d’ajouter et/ou modifier les ressources Azure dont ils ont besoin pour leurs environnements de développement ou de test. Le [coût par ressource](devtest-lab-configure-cost-management.md#view-cost-by-resource) page permet aux propriétaires de laboratoire de suivre le coût de chaque environnement utilisé pour les enquêtes.

Pour plus d’informations, consultez [ensemble les droits d’accès à un groupe de ressources d’environnement](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Formations, des laboratoires pratiques et des hackathons 
Un laboratoire dans Azure DevTest Labs agit comme un excellent conteneur pour les activités telles que des ateliers, des ateliers pratiques, des formations ou des hackathons temporaires.  Ce service vous permet de créer un laboratoire où vous pouvez fournir des modèles personnalisés utilisables par chaque participant pour créer des environnements de formation identiques et isolés. Dans ce scénario, DevTest Labs offre les avantages suivants :

- [Stratégies](devtest-lab-set-lab-policy.md) participants uniquement d’obtenir le nombre de ressources, comme les machines virtuelles, dont ils ont besoin.
- Préconfiguré et créés les machines sont [demandées](devtest-lab-add-claimable-vm.md) avec une action unique à partir de participant.
- Laboratoires sont partagées avec les participants en accédant à [URL pour le laboratoire](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Dates d’expiration](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) sur des machines virtuelles, assurez-vous que les machines sont supprimées une fois qu’ils ne sont plus nécessaires.
- Il est facile de [supprimer un laboratoire](devtest-lab-delete-lab-vm.md#delete-a-lab) et tous les [ressources associées](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) lorsque la formation terminée.

Pour plus d’informations, consultez [utiliser Azure DevTest Labs pour l’apprentissage](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Preuve de concept et déploiement à l’échelle
Une fois que vous décidez de découvrir DevTest Labs, il existe deux générales chemins d’accès vers l’avant : Preuve de Concept et déploiement de mise à l’échelle.  

Un **déploiement à l’échelle** consiste en des semaines/mois d’examen et de planification dans le but de déployer DevTest Labs dans toute l’entreprise pour des centaines voire des milliers de développeurs.

Un **preuve de concept** déploiement se concentre sur un effort concentré à partir d’une seule équipe pour établir la valeur d’organisation. Si un déploiement à l’échelle semble tentant, l’approche a tendance à échouer plus souvent que l’option de preuve de concept. Par conséquent, nous vous recommandons de commencer doucement. Apprenez de la première équipe, répétez la même approche avec deux ou trois équipes supplémentaires, puis planifiez un déploiement à l’échelle en vous appuyant sur les connaissances acquises. Pour que l’option de preuve de concept réussisse, nous vous recommandons de choisir une ou deux équipes et d’identifier leurs scénarios (environnement de test ou environnement de développement), de documenter leurs cas d’utilisation actuels et de déployer DevTest Labs.

## <a name="next-steps"></a>Étapes suivantes
Lisez les articles suivants :

- [Concepts de DevTest Labs](devtest-lab-concepts.md)
- [Forum Aux Questions DevTest Labs](devtest-lab-faq.md)

