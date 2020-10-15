---
title: Concepts de laboratoires de classe - Azure Lab Services | Microsoft Docs
description: Découvrez les concepts de base de Lab Services et comment cet outil facilite la création et la gestion des laboratoires.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 38dd77df7a80ad252b553b6afa8b52d7fee753a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85443704"
---
# <a name="classroom-labs-concepts"></a>Concepts des laboratoires de salle de classe

La liste suivante présente les définitions et concepts principaux de Lab Services :

## <a name="quota"></a>Quota

Le quota est la limite d’utilisation (en heures) d’une machine virtuelle de laboratoire qu’un formateur peut accorder à un étudiant. Il peut être défini sur 0 ou un nombre d’heures spécifique. Si le quota est défini sur 0, un étudiant ne peut utiliser la machine virtuelle que quand une planification est en cours d’exécution ou quand un formateur active manuellement la machine virtuelle de l’étudiant.  

Les heures de quota sont comptabilisées quand l’étudiant démarre lui-même la machine virtuelle lab.  Si un formateur démarre manuellement la machine virtuelle de laboratoire d’un étudiant, les heures de quota ne sont pas utilisées pour cet étudiant.

## <a name="schedules"></a>Planifications

Les planifications sont les créneaux horaires qu’un formateur peut créer pour la classe afin que les machines virtuelles des étudiants soient disponibles pour le temps de classe.  Les planifications peuvent être ponctuelles ou récurrentes.  Les heures de quota ne sont pas utilisées lorsqu’une planification est cours d’exécution.

Il existe trois types de planifications : Standard, Démarrage uniquement et Arrêt uniquement.

- **Standard**.  Cette planification démarre toutes les machines virtuelles des étudiants à l’heure de début spécifiée et arrête toutes les machines virtuelles des étudiants à l’heure d’arrêt spécifiée.
- **Démarrage uniquement**.   Cette planification démarre toutes les machines virtuelles des étudiants à l’heure spécifiée.  Les machines virtuelles des étudiants ne s’arrêteront pas tant qu’un étudiant n’aura pas arrêté sa machine virtuelle via le portail Azure Lab Services ou qu’une planification d’arrêt uniquement ne se produira pas.
- **Arrêt uniquement**.  Cette planification arrête toutes les machines virtuelles des étudiants à l’heure spécifiée.  

## <a name="template-virtual-machine"></a>Modèle de machine virtuelle

Un modèle de machine virtuelle de laboratoire est une image de machine virtuelle de base, à partir de laquelle toutes les machines virtuelles des utilisateurs sont créées. Les créateurs/formateurs de laboratoire définissent le modèle de machine virtuelle et le configurent avec le logiciel qu’ils souhaitent proposer aux participants pour réaliser les laboratoires. Lorsque vous publiez un modèle de machine virtuelle, Azure Lab Services crée ou met à jour les machines virtuelles de laboratoire en fonction du modèle de machine virtuelle.

## <a name="user-profiles"></a>Profils utilisateur

Cet article décrit les profils utilisateur disponibles dans Azure Lab Services.

### <a name="lab-account-owner"></a>Propriétaire du compte du laboratoire

En règle générale, un administrateur informatique des ressources cloud de l’organisation, qui est propriétaire de l’abonnement Azure, se comporte comme un propriétaire de compte lab et effectue les tâches suivantes :

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
