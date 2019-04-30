---
title: Concepts de laboratoires de salle de classe - Azure Lab Services | Microsoft Docs
description: Découvrez les concepts de base de laboratoire de Services et comment elle peut faciliter créer et gérer les laboratoires.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 82f9d7090d7283e0b00b36e0928fffb3395ca4c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679083"
---
# <a name="classroom-labs-concepts"></a>Concepts des laboratoires de salle de classe
La liste suivante contient les définitions et concepts clés de Lab Services :

## <a name="quota"></a>Quota
Quota est la limite de temps (en heures), un enseignant peut être définie pour un étudiant à utiliser une machine virtuelle de laboratoire. Elle peut être définie à 0, un nombre illimité, ou un nombre spécifique d’heures. Si le quota est défini sur 0, un étudiant peut uniquement utiliser la machine virtuelle lors de l’exécution d’une planification ou quand un enseignant active manuellement la machine virtuelle de l’étudiant.
 
## <a name="schedules"></a>Planifications
Les planifications sont les emplacements de temps (une seule fois ou périodique) un enseignant peut créer pour la classe. Toutes les machines virtuelles dans le laboratoire sont démarrés automatiquement au début de la planification et ils sont arrêtés à la fin de la planification. Quotas horaires ne sont pas utilisés lors de l’exécution d’une planification.

## <a name="template-virtual-machine"></a>Machine virtuelle
Une machine virtuelle dans un laboratoire est une image de machine virtuelle de base à partir de laquelle les machines virtuelles de tous les utilisateurs sont créés. Créateurs de formateurs de lab configurer la machine virtuelle de modèle et configurez-le avec le logiciel qu’ils souhaitent fournir aux participants de formation pour effectuer des laboratoires. Lorsque vous publiez un modèle de machine virtuelle, Azure Lab Services crée ou met à jour de machines virtuelles de laboratoire basées sur le modèle de machine virtuelle. 


## <a name="user-profiles"></a>Profils utilisateur
Cet article décrit les profils utilisateur disponibles dans Azure Lab Services. 

### <a name="lab-account-owner"></a>Propriétaire du compte du laboratoire
En règle générale, l’administrateur informatique des ressources de cloud de l’organisation, qui est propriétaire de l’abonnement Azure, se comporte comme un propriétaire de compte de laboratoire et effectue les tâches suivantes :   

- Définit un compte de laboratoire pour votre organisation.
- Gère et configure les stratégies sur l’ensemble des laboratoires.
- Attribue des autorisations aux personnes de l’organisation pour créer un laboratoire sous le compte de laboratoire.

### <a name="professor"></a>Professeur
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
