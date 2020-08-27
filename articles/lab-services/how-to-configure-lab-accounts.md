---
title: Configurer l’arrêt automatique des machines virtuelles dans Azure Lab Services
description: Cet article explique comment configurer l’arrêt automatique des machines virtuelles dans le compte lab.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8dbc54ad14530006b56bf336d9d78e7d59843485
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88603873"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Configurer l’arrêt automatique des machines virtuelles pour un compte lab

Vous pouvez activer plusieurs fonctionnalités de contrôle des coûts d’arrêt automatique afin d’éviter de manière proactive les coûts supplémentaires lorsque les machines virtuelles ne sont pas utilisées activement. La combinaison des trois fonctionnalités suivantes d’arrêt et de déconnexion automatiques permet d’intercepter la plupart des cas où les utilisateurs laissent accidentellement leurs machines virtuelles en marche :
 
- Déconnecter automatiquement les utilisateurs des machines virtuelles que le système d’exploitation juge inactives (Windows uniquement).
- Arrêter automatiquement les machines virtuelles quand les utilisateurs se déconnectent (Windows et Linux).
- Arrêter automatiquement les machines virtuelles démarrées, mais auxquelles les utilisateurs ne se connectent pas.

Pour plus d’informations sur les fonctionnalités d’arrêt automatique, consultez la section [Optimiser le contrôle des coûts à l’aide des paramètres d’arrêt automatique](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings).

## <a name="enable-automatic-shutdown"></a>Activer l’arrêt automatique

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page **Compte lab**.
1. Sélectionnez **Paramètres des labs** dans le menu de gauche.
1. Sélectionnez le ou les paramètres d’arrêt automatique appropriés pour votre scénario.  

    > [!div class="mx-imgBorder"]
    > ![Paramètre d’arrêt automatique au niveau du compte Lab](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    Ces paramètres s’appliquent à tous les labs créés dans le compte lab. Un créateur de laboratoire (enseignant) peut remplacer ce paramètre au niveau du laboratoire. La modification apportée à ce paramètre au niveau du compte Lab n’affecte que les laboratoires créés après la modification.

    Pour désactiver les paramètres, décochez-en les cases sur cette page. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la façon dont un propriétaire de lab peut configurer ou remplacer ce paramètre au niveau du lab, consultez [Configurer l’arrêt automatique des machines virtuelles pour un lab](how-to-enable-shutdown-disconnect.md).
