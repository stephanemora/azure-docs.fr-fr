---
title: Utiliser des laboratoires de salle de classe pour formations - Azure Lab Services | Microsoft Docs
description: Découvrez comment utiliser Azure DevTest Labs pour les scénarios de formation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 4d2ba11181977f1976b5ae933e8b93a92424fa96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695277"
---
# <a name="use-classroom-labs-for-trainings"></a>Utiliser des laboratoires de salle de classe pour les formations
Vous pouvez configurer un laboratoire de formations. Laboratoires de salle de classe de Azure Lab Services permettent de créer un laboratoire pour votre formation où chaque participant utilise des environnements identiques et isolés pour l’apprentissage. Vous pouvez appliquer des stratégies pour vous assurer que les environnements de formation sont à la disposition des participants uniquement lorsque ces derniers en ont besoin et qu’ils contiennent suffisamment de ressources (telles que des machines virtuelles) pour la formation. 

![Laboratoire de salle de classe](../media/classroom-labs-scenarios/classroom.png)

La classe Labs remplit les conditions suivantes qui sont nécessaires pour assurer la formation dans n’importe quel environnement virtuel : 

- Les participants peuvent configurer rapidement leur environnement de formation.
- Toutes les machines de formation sont identiques.
- Les participants ne voient pas les machines virtuelles créées par les autres participants.
- Contrôlez les coûts en vous assurant que les participants ne peuvent pas obtenir plus de machines virtuelles que nécessaire et qu’ils arrêtent les machines virtuelles lorsqu’ils ne les utilisent pas.
- Partagez facilement le laboratoire de formation avec chaque participant.
- Réutilisez le laboratoire de formation à l’infini.

Dans cet article, vous découvrez les différentes fonctionnalités d’Azure Lab Services qui peuvent être utilisées pour répondre aux exigences de formation décrites précédemment et les étapes détaillées que vous pouvez suivre pour configurer un laboratoire pour l’apprentissage.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Créer le compte de laboratoire en tant qu’un administrateur de compte de laboratoire
La première étape à l’aide d’Azure Lab Services consiste à créer un compte de laboratoire dans le portail Azure. Après qu’un administrateur de compte de laboratoire a créé le compte de laboratoire, l’administrateur ajoute les utilisateurs qui souhaitent créer des laboratoires à la **créateur Lab** rôle. Les formateurs créer des laboratoires avec des machines virtuelles pour les étudiants effectuer les exercices du cours qu’ils enseignent. Pour plus d’informations, consultez [créer et gérer un compte de laboratoire](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Créer et gérer les laboratoires de salle de classe
Un formateur, qui est membre du rôle créateur Lab dans un compte de laboratoire, peut créer un ou plusieurs laboratoires dans le compte de laboratoire. Vous créez et configurez un modèle de machine virtuelle avec tous les logiciels requis pour effectuer des exercices de votre cours. Vous choisissez une image prêtes à l’emploi à partir des images disponibles pour la création d’un laboratoire de salle de classe et personnalisez en installant le logiciel requis pour le laboratoire. Pour plus d’informations, consultez [créer et gérer les laboratoires de salle de classe](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Configurer des paramètres et des stratégies d’utilisation
Le créateur de laboratoire peut ajouter ou supprimer des utilisateurs au laboratoire, obtenir le lien d’inscription pour envoyer aux utilisateurs de laboratoire, définir des stratégies telles que la définition de quotas individuels par utilisateur, mettre à jour le nombre de machines virtuelles disponibles dans le laboratoire et bien plus encore. Pour plus d’informations, consultez [configurer des stratégies et des paramètres d’utilisation](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Créer et gérer des planifications
Les planifications vous permettent de configurer un laboratoire de classe de sorte que les machines virtuelles utilisées dans le laboratoire démarrent et s’arrêtent automatiquement à une heure précise. Vous pouvez définir une planification ponctuelle ou périodique. Pour plus d’informations, consultez [créer et gérer des planifications pour les laboratoires de salle de classe](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Configurer et publier un modèle de machine virtuelle
Le modèle de laboratoire est une image de machine virtuelle de base, à partir de laquelle toutes les machines virtuelles des utilisateurs sont créées. Définir le modèle de machine virtuelle afin qu’il est configuré avec exactement ce que vous souhaitez fournir aux participants de formation. Vous pouvez indiquer le nom et la description du modèle que voient les utilisateurs du laboratoire. Ensuite, publiez le modèle pour rendre les instances du modèle de machine virtuelle accessibles aux utilisateurs de votre laboratoire. Quand vous publiez un modèle, Azure Lab Services crée les machines virtuelles dans le laboratoire à l’aide du modèle. Le nombre de machines virtuelles créées dans ce processus est identique au nombre maximal d’utilisateurs autorisés dans le laboratoire, que vous pouvez définir dans la politique d’utilisation du laboratoire. Toutes les machines virtuelles ont la même configuration que le modèle. Pour plus d’informations, consultez [définir configurer et publier des machines virtuelles de modèle](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Utilisez des machines virtuelles dans le laboratoire de salle de classe
Un étudiant ou un participant de formation inscrit au laboratoire et se connecte à la machine virtuelle pour effectuer les exercices du cours. Pour plus d’informations, consultez [comment accéder à un laboratoire de salle de classe](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Étapes suivantes
Démarrer avec la création d’un compte de laboratoire dans les laboratoires de salle de classe en suivant les instructions dans l’article : [Tutoriel : Configurer un compte de laboratoire avec Azure Lab Services](tutorial-setup-lab-account.md).