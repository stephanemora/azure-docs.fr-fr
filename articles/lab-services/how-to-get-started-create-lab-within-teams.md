---
title: Prise en main et création d’un laboratoire Azure Lab Services à partir de Teams
description: Découvrez comment prendre en main et créer un laboratoire Azure Lab Services à partir de Teams.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 0604e2934ff6b011acfa9dd4a4b25fa58193e69b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044443"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Prise en main et création d’un laboratoire Lab Services à partir de Teams

Cet article explique comment ajouter l’application **Azure Lab Services** à une instance Teams, puis comment créer un laboratoire dans un environnement MS Teams.

## <a name="prerequisites"></a>Prérequis

Dans ce tutoriel, vous allez configurer un laboratoire avec des machines virtuelles pour votre équipe. Pour configurer un laboratoire dans un compte Lab, vous devez être membre de l’un des rôles suivants dans le compte Lab : Propriétaire, Créateur de laboratoire ou Contributeur. Le compte que vous avez utilisé pour créer un compte lab est automatiquement ajouté au rôle Propriétaire. Vous pouvez donc utiliser le compte d’utilisateur que vous avez utilisé lors de la création d’un compte de laboratoire pour créer un laboratoire.

Voici le workflow standard quand vous utilisez Azure Lab Services dans Teams :

1. L’utilisateur [crée un compte de laboratoire](tutorial-setup-lab-account.md#create-a-lab-account) sur le portail Azure.
1. Un [créateur de compte de laboratoire ajoute d’autres utilisateurs](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) au rôle **Créateur de laboratoire**. Par exemple, le créateur/administrateur du compte lab ajoute des formateurs au rôle **Créateur de laboratoire** afin qu’ils puissent créer des laboratoires pour leurs classes.
1. Ensuite, les enseignants créent des laboratoires, préconfigurent le modèle de machine virtuelle et publient le laboratoire pour fournir des machines virtuelles à tous les membres de l’équipe.
1. Une fois le laboratoire publié, une machine virtuelle est attribuée à chaque membre de l’équipe lors de leur première connexion à Azure Lab Services, soit en cliquant sur l’onglet contenant l’application **Azure Lab Services** avec Teams (authentification unique), soit en accédant au [site web du laboratoire](https://labs.azure.com). Les utilisateurs peuvent ensuite utiliser la machine virtuelle pour faire les travaux de classe et leurs devoirs.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Ajouter l’application Azure Lab Services sous forme d’onglet à une équipe

En tant que propriétaire d’équipe, vous pouvez ajouter l’application **Azure Lab Services** directement dans les canaux Teams. L’application est alors disponible pour tous les membres de l’équipe. Suivez les trois étapes ci-dessous :

1. Accédez au canal Teams où vous souhaitez ajouter l’application et sélectionnez **+** pour ajouter un onglet. 
1. Recherchez **Azure Lab Services** dans les options de l’onglet et ajoutez cette application. 

    > [!NOTE]
    > Seuls les **propriétaires** d’équipe pourront créer des laboratoires pour l’équipe.
1. Sélectionnez un compte Lab Services que vous souhaitez utiliser pour créer des laboratoires de salle de classe dans cette équipe. 

    Azure Lab Services utilise l’authentification unique sur le [site web d’Azure Lab Services](https://labs.azure.com) et extrait tous les comptes de laboratoire auxquels vous avez accès. 

    Les comptes qui se trouvent dans le même locataire que Teams et pour lesquels vous avez un accès **Propriétaire**, **Contributeur**ou **Créateur** sont affichés. 

   ![Bienvenue sur ALS](./media/integrate-with-teams/welcome.png) 
1. Appuyez sur **Enregistrer** et l’onglet est ajouté au canal.

    Vous pouvez maintenant sélectionner l’onglet **Azure Lab Services** à partir de votre canal et commencer à gérer les laboratoires comme décrit à l’étape suivante.

Une fois le compte de laboratoire sélectionné, les propriétaires d’équipe peuvent créer des laboratoires pour l’équipe. L’ensemble du processus de création de laboratoire et toutes les tâches au niveau du laboratoire peuvent être effectués au sein de Teams. Les utilisateurs ont la possibilité de créer plusieurs laboratoires au sein d’une même équipe et le propriétaire d’équipe, avec un accès approprié au niveau du compte de laboratoire, ne voit que les laboratoires associés à l’équipe spécifique.

## <a name="deleting-classroom-labs"></a>Suppression des laboratoires de salle de classe

Un laboratoire créé au sein de Teams peut être supprimé sur le [site web de Lab Services](https://labs.azure.com) en supprimant le laboratoire directement, comme décrit dans [Gérer des laboratoires de classe dans Azure Lab Services](how-to-manage-classroom-labs.md). 

La suppression du laboratoire est également déclenchée lorsque l’équipe est supprimée. Si l’équipe dans laquelle le laboratoire est créé est supprimée, le laboratoire est automatiquement supprimé 24 heures après le déclenchement de la synchronisation automatique de la liste d’utilisateurs. 

La suppression de l’onglet ou la désinstallation de l’application n’entraîne pas la suppression du laboratoire. Si l’onglet est supprimé, les utilisateurs figurant sur la liste des membres de l’équipe peuvent toujours accéder aux machines virtuelles sur le [site web de Lab Services](https://labs.azure.com), à moins que la suppression du laboratoire soit explicitement déclenchée par la suppression du laboratoire sur le site web ou par la suppression de l’équipe. 

## <a name="next-steps"></a>Étapes suivantes

Quand un laboratoire est créé dans Teams, la liste d’utilisateurs du laboratoire est automatiquement renseignée et synchronisée avec l’appartenance à l’équipe. Chaque personne de l’équipe, y compris les propriétaires, les membres et les invités, est automatiquement ajoutée à la liste d’utilisateurs du laboratoire. Azure Lab Services assure la synchronisation de l’appartenance à l’équipe, et une synchronisation automatique est déclenchée toutes les 24 heures. Pour plus d’informations, consultez :

[Gérer les listes d’utilisateurs de Lab Services à partir de Teams](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Voir également

Consultez également les articles suivants :

- [Utilisation d’Azure Lab Services dans Teams](lab-services-within-teams-overview.md)
- [Gestion des listes d’utilisateurs de labo dans Teams](how-to-manage-user-lists-within-teams.md)
- [Gestion du pool de machines virtuelles du labo dans Teams](how-to-manage-vm-pool-within-teams.md)
- [Créer et gérer des planifications de labo dans Teams](how-to-create-schedules-within-teams.md)
- [Accès à une machine virtuelle dans Teams – Affichage étudiant](how-to-access-vm-for-students-within-teams.md)

