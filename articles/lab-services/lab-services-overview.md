---
title: À propos d’Azure Lab Services | Microsoft Docs
description: Découvrez comment Lab Services facilite la création, la gestion et la sécurisation de laboratoires grâce à des machines virtuelles pouvant être utilisées par les développeurs, les testeurs, les enseignants et les étudiants, entre autres.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: b42183369b9ad88c77a05a91fdba8fe0efca2a8c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="an-introduction-to-azure-lab-services-formerly-azure-devtest-labs"></a>Présentation d’Azure Lab Services (anciennement Azure DevTest Labs)
Azure Lab Services vous permet de configurer rapidement un environnement pour votre équipe (par exemple : un environnement de développement, un environnement de test, un environnement de laboratoire de salle de classe) dans le cloud. Le propriétaire d’un laboratoire crée un laboratoire, configure Windows ou des machines virtuelles Linux, installe les logiciels et outils nécessaires, puis les rend disponibles aux utilisateurs du laboratoire. Les utilisateurs du laboratoire se connectent à des machines virtuelles (VM) dans le laboratoire puis s’en servent pour leurs tâches quotidiennes, leurs projets à court terme ou les exercices en classe. Lorsque les utilisateurs commencent à exploiter les ressources du laboratoire, un administrateur de laboratoire peut analyser les coûts et l’utilisation entre plusieurs laboratoires et définir des stratégies globales afin d’optimiser les coûts de l’équipe ou de l’organisation.

> [!IMPORTANT]
> Azure DevTest Labs intègre de nouveaux types de laboratoires (Azure Lab Services) 
> 
> Azure Lab Services vous permet de créer des laboratoires gérés, tels que des laboratoires de salle de classe. Le service lui-même gère toute l’infrastructure d’un laboratoire géré, de l’augmentation de la capacité des machines virtuelles à la gestion des erreurs, en passant par la mise à l’échelle de cette infrastructure. Les laboratoires sont actuellement en version préliminaire. Une fois la version préliminaire terminée, les nouveaux types de laboratoires et les DevTest Labs apparaissent sous le nouveau nom global Azure Lab Services, où tous les types de laboratoires continuent d’évoluer. 

## <a name="key-capabilities"></a>Fonctionnalités clés
Azure Lab Services prend en charge les fonctionnalités clés suivantes : 

- **Configuration rapide et flexible d’un laboratoire**. Grâce à Azure Lab Services, les propriétaires de laboratoire peuvent rapidement configurer un laboratoire selon leurs besoins. Le service offre la possibilité d’effectuer tout le travail de l’infrastructure Azure pour les laboratoires gérés, ou permet aux propriétaires de laboratoire de gérer et de personnaliser l’infrastructure dans l’abonnement du propriétaire de laboratoire. Le service intègre une mise à l’échelle et une résilience de l’infrastructure pour les laboratoires que le service gère pour vous. 
- **Expérience simplifiée pour les utilisateurs du laboratoire**. Dans un laboratoire managé, par exemple un laboratoire de salle de classe, les utilisateurs peuvent s’inscrire à un laboratoire avec un code d’inscription et accéder ainsi à tout moment aux ressources de ce laboratoire. Dans un laboratoire personnalisé créé dans DevTest Labs, le propriétaire d’un laboratoire peut autoriser les utilisateurs à créer et à accéder aux machines virtuelles, à gérer et à réutiliser des disques de données, et à configurer des clés secrètes réutilisables.  
- **Optimisation et analyse des coûts**. Un propriétaire du laboratoire peut définir des planifications de laboratoire pour arrêter et démarrer automatiquement des machines virtuelles. Le propriétaire du laboratoire peut définir une planification pour spécifier les plages horaires où les machines virtuelles du laboratoire sont accessibles aux utilisateurs, définir des stratégies d’utilisation par utilisateur ou par un laboratoire afin d’optimiser les coûts, et pour analyser les tendances d’utilisation et d’activité d’un laboratoire. Pour les laboratoires gérés tels que des laboratoires de salle de classe, un sous-ensemble plus petit d’options d’optimisation et d’analyse des coûts est actuellement disponible. 
- **Sécurité intégrée**. Le propriétaire du laboratoire peut configurer des réseaux virtuels privés et un sous-réseau pour un laboratoire, et activer une adresse IP publique partagée. Les utilisateurs du laboratoire peuvent accéder en toute sécurité aux ressources à l’aide du réseau virtuel configuré avec ExpressRoute ou d’un VPN de site à site. (uniquement disponible dans DevTest Labs actuellement)
- **Intégration dans vos flux de travail et outils**. Azure Lab Services vous permet d’intégrer les laboratoires dans le site Web et les systèmes de gestion de votre organisation. Vous pouvez automatiquement mettre en service les environnements à l’aide de vos outils de déploiement/intégration (CI/CD) en continu. (uniquement disponible dans DevTest Labs actuellement)

## <a name="scenarios"></a>Scénarios
Voici quelques scénarios pris en charge par Azure Lab Services : 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Configurer un laboratoire redimensionnable d’ordinateurs dans le cloud pour votre classe  

- Créer un laboratoire de salle de classe managé. Vous indiquez au service exactement ce dont vous avez besoin, et celui-ci crée et gère l’infrastructure du laboratoire pour vous. Vous pouvez ainsi vous concentrer sur votre classe, sans vous soucier des aspects techniques d’un laboratoire. 
- Offrir aux élèves un laboratoire de machines virtuelles configurées avec exactement ce qui est nécessaire pour une classe. Accorder à chaque élève un nombre limité d’heures afin d’utiliser les machines virtuelles pour le travail en classe.  
- Déplacer vers le cloud le laboratoire d’ordinateurs physiques de votre école. Redimensionner automatiquement le nombre de machines virtuelles uniquement à l’utilisation maximale et au seuil de coûts que vous définissez sur le laboratoire. 
- Supprimer le laboratoire d’un simple clic, une fois que vous avez terminé. 

### <a name="use-devtest-labs-for-development-environments"></a>Utiliser DevTest Labs pour les environnements de développement 
Azure Labs DevTest peut être utilisé pour implémenter de nombreux scénarios clés, dont celui qui implique l’utilisation de DevTest Labs dans l’hébergement d’ordinateurs de développement pour les développeurs. Dans ce scénario, DevTest Labs offre les avantages suivants : 

- Permettre aux développeurs de rapidement mettre en service leurs ordinateurs de développement à la demande.
- Mettre en service les environnements Windows et Linux avec des modèles et des artefacts réutilisables.
- Si besoin, les développeurs peuvent facilement personnaliser leurs ordinateurs de développement.
- Les administrateurs peuvent contrôler les coûts en s’assurant que les développeurs n’obtiennent pas plus de machines virtuelles qu’ils n’en ont besoin pour le développement, et les machines virtuelles sont arrêtées lorsqu’elles ne sont pas utilisées. 

Pour plus d’informations, consultez [Utiliser DevTest Labs pour le développement](devtest-lab-developer-lab.md). 

### <a name="use-devtest-labs-for-test-environments"></a>Utiliser DevTest Labs pour les environnements de test
Vous pouvez utiliser Azure DevTest Labs pour implémenter de nombreux scénarios clés, mais un scénario principal implique l’utilisation de DevTest Labs pour héberger des machines à l’usage des testeurs. Dans ce scénario, DevTest Labs offre les avantages suivants :

- Les testeurs peuvent tester la dernière version de l’application en approvisionnant rapidement des environnements Windows et Linux à l’aide d’artefacts et de modèles réutilisables.
- Ils peuvent faire monter en puissance leur test de charge en approvisionnant plusieurs agents de test.
- Les administrateurs peuvent contrôler les coûts en s’assurant que les testeurs n’obtiennent pas plus de machines virtuelles qu’ils n’en ont besoin pour les tests, et les machines virtuelles sont arrêtées lorsqu’elles ne sont pas utilisées.

Pour plus d’informations, consultez [Utiliser DevTest Labs pour les tests](devtest-lab-test-env.md).

## <a name="user-profiles"></a>Profils utilisateur
Cet article décrit les profils utilisateur disponibles dans Azure Lab Services. 

### <a name="lab-account-owner"></a>Propriétaire du compte du laboratoire
En règle générale, l’administrateur informatique des ressources de cloud de l’organisation, qui est propriétaire de l’abonnement Azure, se comporte comme un propriétaire de compte de laboratoire et effectue les tâches suivantes :   

- Définit un compte de laboratoire pour votre organisation.
- Gère et configure les stratégies sur l’ensemble des laboratoires.
- Attribue des autorisations aux personnes de l’organisation pour créer un laboratoire sous le compte de laboratoire.

### <a name="lab-creator"></a>Créateur de laboratoire 
En règle générale, les utilisateurs comme un responsable du développement, un enseignant, un hôte hackathon ou un formateur en ligne crée des laboratoires sous un compte de laboratoire. Un créateur de laboratoire effectue les tâches suivantes : 

- Crée un laboratoire.
- Crée les machines virtuelles dans le laboratoire. 
- Installe les logiciels appropriés sur les machines virtuelles.
- Désigne qui peut accéder au laboratoire.
- Fournit aux utilisateurs un lien vers le laboratoire.

### <a name="lab-user"></a>Utilisateur du laboratoire
Un utilisateur de laboratoire effectue les tâches suivantes :

- Utilise le lien d’inscription que lui a envoyé le créateur du laboratoire pour s’inscrire à ce laboratoire. 
- Se connecte à une machine virtuelle dans le laboratoire et l’utilise pour le développement, les tests ou le travail en classe. 

## <a name="next-steps"></a>Étapes suivantes
Commencez à configurer un laboratoire à l’aide d’Azure Lab Services :

- [Configurer un laboratoire de salle de classe](tutorial-setup-classroom-lab.md)
- [Configurer un laboratoire personnalisé](tutorial-create-custom-lab.md)
