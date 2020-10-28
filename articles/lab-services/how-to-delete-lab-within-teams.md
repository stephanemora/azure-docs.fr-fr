---
title: Supprimer un laboratoire Azure Lab Services à partir de Teams
description: Découvrez comment supprimer un laboratoire Azure Lab Services à partir de Teams.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 1d2fe73d33a88e595f42b47c1e7122dcbdfe1063
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094128"
---
# <a name="delete-labs-within-teams"></a>Supprimer des laboratoires dans Teams

Cet article explique comment supprimer un laboratoire de l’application **Azure Lab Services** .

## <a name="prerequisites"></a>Prérequis

* [Créez un compte Lab Services](tutorial-setup-lab-account.md#create-a-lab-account) dans le portail Azure.
* [Démarrez et créez un laboratoire Lab Services dans Teams](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>Supprimer des labos

Un laboratoire créé au sein de Teams peut être supprimé sur le [site web de Lab Services](https://labs.azure.com) en supprimant le laboratoire directement, comme décrit dans [Gérer des laboratoires de classe dans Azure Lab Services](how-to-manage-classroom-labs.md). 

La suppression du laboratoire est également déclenchée lorsque l’équipe est supprimée. Si l’équipe dans laquelle le laboratoire est créé est supprimée, le laboratoire est automatiquement supprimé 24 heures après le déclenchement de la synchronisation automatique de la liste d’utilisateurs. 

> [!IMPORTANT]
> La suppression de l’onglet ou la désinstallation de l’application n’entraîne pas la suppression du laboratoire. 

Si l’onglet est supprimé, les utilisateurs figurant sur la liste des membres de l’équipe peuvent toujours accéder aux machines virtuelles sur le [site web de Lab Services](https://labs.azure.com), à moins que la suppression du laboratoire soit explicitement déclenchée par la suppression du laboratoire sur le site web ou par la suppression de l’équipe. 

## <a name="next-steps"></a>Étapes suivantes

- [Utilisation d’Azure Lab Services dans Teams](lab-services-within-teams-overview.md)
- [Gestion des listes d’utilisateurs de labo dans Teams](how-to-manage-user-lists-within-teams.md)
- [Gestion du pool de machines virtuelles du labo dans Teams](how-to-manage-vm-pool-within-teams.md)
- [Créer et gérer des planifications de labo dans Teams](how-to-create-schedules-within-teams.md)
- [Accès à une machine virtuelle dans Teams – Affichage étudiant](how-to-access-vm-for-students-within-teams.md)

