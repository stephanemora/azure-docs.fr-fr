---
title: Concepts de laboratoires de classe - Azure Lab Services | Microsoft Docs
description: Découvrez les concepts de base de Lab Services et comment cet outil facilite la création et la gestion des laboratoires.
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
ms.openlocfilehash: 348340516f9332f5492c7ce60c3d164da44a008c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120883"
---
# <a name="classroom-labs-concepts"></a>Concepts des laboratoires de salle de classe

La liste suivante présente les définitions et concepts principaux de Lab Services :

## <a name="quota"></a>Quota

Le quota est la limite d’utilisation (en heures) d’une machine virtuelle de laboratoire qu’un enseignant peut accorder à un étudiant. Il peut être défini sur 0 ou un nombre d’heures spécifique. Si le quota est défini sur 0, un étudiant ne peut utiliser la machine virtuelle que lorsqu’une planification est en cours d’exécution ou lorsqu’un enseignant active manuellement la machine virtuelle de l’étudiant.  

Les heures de quota sont comptabilisées quand l’étudiant démarre lui-même la machine virtuelle lab.  Si un enseignant démarre manuellement la machine virtuelle lab d’un étudiant, les heures de quota ne sont pas utilisées pour cet étudiant.

## <a name="schedules"></a>Planifications

Les planifications sont les créneaux (uniques ou récurrents) qu’un enseignant peut créer pour le cours. Toutes les machines virtuelles du laboratoire démarrent automatiquement au début de la planification, et s’arrêtent automatiquement à la fin de celle-ci. Les quotas horaires ne sont pas utilisés lorsqu’une planification est cours d’exécution.

## <a name="template-virtual-machine"></a>Modèle de machine virtuelle

Un modèle de machine virtuelle de laboratoire est une image de machine virtuelle de base, à partir de laquelle toutes les machines virtuelles des utilisateurs sont créées. Les créateurs/formateurs de laboratoire définissent le modèle de machine virtuelle et le configurent avec le logiciel qu’ils souhaitent proposer aux participants pour réaliser les laboratoires. Lorsque vous publiez un modèle de machine virtuelle, Azure Lab Services crée ou met à jour les machines virtuelles de laboratoire en fonction du modèle de machine virtuelle.

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
