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
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: e9c3cae7c7129cc489ddd38b5b2de18dd6f52e58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660527"
---
# <a name="introduction-to-classroom-labs"></a>Introduction aux laboratoires de salle de classe
Azure Lab Services vous permet de configurer rapidement un environnement pour votre laboratoire de salle de classe dans le cloud. Un formateur crée un laboratoire de salle de classe, configure Windows ou des machines virtuelles Linux, installe les logiciels et laboratoires d’outils nécessaires dans la classe, puis les rend disponibles aux étudiants. Les étudiants de la classe se connectent à des machines virtuelles (VM) dans le laboratoire puis s’en servent pour leurs projets, devoirs et exercices en classe. 

Les laboratoires de la salle de classe sont des laboratoires gérés qui sont gérés par Azure. Le service lui-même gère toute l’infrastructure d’un laboratoire géré, de l’augmentation de la capacité des machines virtuelles à la gestion des erreurs, en passant par la mise à l’échelle de cette infrastructure. Vous spécifiez le type d’infrastructure dont vous avez besoin et installez les outils ou les logiciels requis pour la classe. Les laboratoires sont actuellement en version préliminaire.  

## <a name="scenarios"></a>Scénarios
Voici le scénario principal que les laboratoires de salle de classe Azure Lab Services prennent en charge : 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Configurer un laboratoire redimensionnable d’ordinateurs dans le cloud pour votre classe  

- Créer un laboratoire de salle de classe managé. Vous indiquez au service exactement ce dont vous avez besoin, et celui-ci crée et gère l’infrastructure du laboratoire pour vous. Vous pouvez ainsi vous concentrer sur votre classe, sans vous soucier des aspects techniques d’un laboratoire. 
- Proposer aux étudiants un laboratoire de machines virtuelles configurées précisément pour répondre aux besoins de la classe ; Accorder à chaque élève un nombre limité d’heures afin d’utiliser les machines virtuelles pour le travail en classe.  
- Déplacer vers le cloud le laboratoire d’ordinateurs physiques de votre école. Redimensionner automatiquement le nombre de machines virtuelles uniquement à l’utilisation maximale et au seuil de coûts que vous définissez sur le laboratoire. 
- Supprimez le laboratoire d’un simple clic une fois que vous avez terminé. 

## <a name="user-profiles"></a>Profils utilisateur
Cet article décrit les profils utilisateur disponibles dans Azure Lab Services. 

### <a name="lab-account-owner"></a>Propriétaire du compte du laboratoire
En règle générale, l’administrateur informatique des ressources de cloud de l’organisation, qui est propriétaire de l’abonnement Azure, se comporte comme un propriétaire de compte de laboratoire et effectue les tâches suivantes :   

- Définit un compte de laboratoire pour votre organisation.
- Gère et configure les stratégies sur l’ensemble des laboratoires.
- Attribue des autorisations aux personnes de l’organisation pour créer un laboratoire sous le compte de laboratoire.

### <a name="educator"></a>Formateur
En général, les utilisateurs comme les enseignants ou les formateurs en ligne créent des laboratoires de salle de classe sous un compte de laboratoire. Un formateur effectue les tâches suivantes : 

- Crée un laboratoire de salle de classe.
- Crée les machines virtuelles dans le laboratoire. 
- Installe les logiciels appropriés sur les machines virtuelles.
- Désigne qui peut accéder au laboratoire.
- Fournit le lien d’inscription au laboratoire aux étudiants.

### <a name="student"></a>Étudiant
Un étudiant effectue les tâches suivantes :

- Utilise le lien d’inscription que lui a envoyé le créateur du laboratoire pour s’inscrire à ce laboratoire. 
- Se connecte à une machine virtuelle dans le laboratoire et l’utilise pour le travail en classe, les devoirs et d’autres projets. 

## <a name="next-steps"></a>Étapes suivantes
Prendre en main un compte de laboratoire qui est requis pour créer un laboratoire de salle de classe à l’aide de Azure Lab Services :

- [Configurer un compte de laboratoire](tutorial-setup-lab-account.md)
