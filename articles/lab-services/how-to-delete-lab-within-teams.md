---
title: Supprimer un laboratoire Azure Lab Services à partir de Teams
description: Découvrez comment supprimer un laboratoire Azure Lab Services à partir de Teams.
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f93151e970fc38ca55b43c5322bf27862ad32b7a
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180511"
---
# <a name="delete-labs-within-teams"></a>Supprimer des laboratoires dans Teams

Cet article explique comment supprimer un laboratoire de l’application **Azure Lab Services**.

## <a name="prerequisites"></a>Prérequis

* [Créez un compte Lab Services](tutorial-setup-lab-account.md#create-a-lab-account) dans le portail Azure.
* [Démarrez et créez un laboratoire Lab Services dans Teams](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>Supprimer des labos

Un labo créé au sein de Teams peut être supprimé directement sur le [site web Lab Services](https://labs.azure.com) (cf. [Gestion des labos dans Azure Lab Services](how-to-manage-classroom-labs.md)). 

La suppression du laboratoire est également déclenchée lorsque l’équipe est supprimée. Si l’équipe dans laquelle le laboratoire est créé est supprimée, le laboratoire est automatiquement supprimé 24 heures après le déclenchement de la synchronisation automatique de la liste d’utilisateurs. 

> [!IMPORTANT]
> La suppression de l’onglet ou la désinstallation de l’application n’entraîne pas la suppression du laboratoire. 

Si l’onglet est supprimé, les utilisateurs figurant sur la liste des membres de l’équipe peuvent toujours accéder aux machines virtuelles sur le [site web de Lab Services](https://labs.azure.com), à moins que la suppression du laboratoire soit explicitement déclenchée par la suppression du laboratoire sur le site web ou par la suppression de l’équipe. 

## <a name="next-steps"></a>Étapes suivantes

- [Utilisation d’Azure Lab Services dans Teams](lab-services-within-teams-overview.md)
- [Gestion des listes d’utilisateurs de labo dans Teams](how-to-manage-user-lists-within-teams.md)
- [Gérer le pool de machines virtuelles du labo dans Teams](how-to-manage-vm-pool-within-teams.md)
- [Créer et gérer des planifications de labo dans Teams](how-to-create-schedules-within-teams.md)
- [Accéder à une machine virtuelle dans Teams – Vue étudiant](how-to-access-vm-for-students-within-teams.md)

