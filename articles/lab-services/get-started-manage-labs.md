---
title: Prise en main d’Azure Lab Services
description: Cet article explique comment prendre en main Azure Lab Services.
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: d260ace7d7819c3ca2db96d6c4984bf834e170da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98165040"
---
# <a name="get-started-with-lab-services"></a>Prise en main de Lab Services 

Azure Lab Services permet aux étudiants et aux enseignants d'accéder à des laboratoires informatiques virtuels à partir de leur ordinateur.

Les enseignants doivent être en mesure d'apprendre aux élèves/parents à utiliser Lab Services dans le cadre de leur enseignement par le biais d'un matériel individuel fourni par l'étudiant. Ainsi, les étudiants pourront accéder aux logiciels standard nécessaires à leurs programmes d'études par le biais de machines virtuelles. 

Une machine virtuelle est un environnement virtuel qui agit comme un ordinateur virtuel. Les machines virtuelles ont leur propre processeur, mémoire et stockage. Les machines virtuelles remplacent une machine réelle et peuvent donner aux utilisateurs un accès aux systèmes d’exploitation et aux logiciels sans qu’ils aient besoin de les avoir sur leur propre appareil. Azure Lab Services fournit un outil permettant aux étudiants d’accéder à des machines virtuelles et de les parcourir et permettant au personnel de gérer ses labos d’ordinateurs virtuels. 

Cet article fournit des informations au personnel enseignant sur la manière d'accéder à Azure Lab Services, de gérer le service et d'apprendre aux étudiants/parents à l'utiliser.

## <a name="key-concepts"></a>Concepts clés

### <a name="quota-hours"></a>Heures de quota

Les étudiants peuvent accéder à leurs machines virtuelles à tout moment pendant les heures de cours planifiées sans que cela n’ait d’incidence sur leur heures de quota. Les heures de quota sont définies pour l’ensemble du semestre et déterminent le nombre d’heures pendant lesquelles un étudiant peut utiliser sa machine virtuelle en dehors des heures de cours normales.

Huit heures par semaine, réinitialisées le dimanche, non cumulables.

Pour plus d’informations, consultez [Définir le quota](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="automatic-shut-down"></a>Arrêt automatique

Afin de réduire les coûts et de ne pas gaspiller les heures associées au quota des étudiants, des arrêts automatiques sont activés pour les labos. Les arrêts automatiques désactiveront les machines virtuelles après une période d’inactivité (aucune entrée à la souris ou au clavier). Les arrêts automatiques fonctionnent en deux étapes : tout d’abord, un étudiant sera déconnecté de la machine virtuelle après une période d’inactivité. À ce stade, la machine virtuelle est toujours **en cours d’exécution** et les étudiants peuvent s’y connecter. Après une autre période d’inactivité faisant suite à la déconnexion, la machine virtuelle s’éteint d’elle-même.

Les arrêts automatiques sont un outil important qui permet d’économiser sur les coûts. Toutefois, ils représentent un défi pour les étudiants en ce qui concerne la sauvegarde de leur travail et le rendu de fichiers de projets volumineux. Si vos étudiants sont souvent déconnectés ou si les machines virtuelles s'éteignent trop rapidement, contactez votre administrateur CTE. 

Pour plus d’informations, consultez [Configurer l’arrêt automatique des machines virtuelles pour un compte lab](how-to-configure-lab-accounts.md).

### <a name="managing-virtual-machines"></a>Gestion des machines virtuelles

La gestion du labo permet aux enseignants de contrôler des éléments tels que la capacité du labo (le nombre de machines virtuelles disponibles pour les étudiants) et de démarrer, d’arrêter ou de réinitialiser manuellement les machines virtuelles. Les enseignants peuvent également se connecter aux machines virtuelles afin de découvrir l’interface avec les étudiants, d’accéder aux fichiers et de résoudre les problèmes liés aux logiciels ou à la machine virtuelle elle-même.

La chose la plus importante à retenir lors de la gestion des machines virtuelles est que chaque fois qu'une machine est **en cours d'exécution**, des frais vous sont facturés, même si personne n'y est connecté.

## <a name="lab-dashboards"></a>Tableaux de bord Lab

### <a name="overview"></a>Vue d’ensemble

Les tableaux de bord de labos dans Azure Lab Services fournissent un instantané des différents aspects d’un labo particulier, notamment les informations sur les machines virtuelles, le nombre de machines virtuelles attribuées ou non, le nombre d’utilisateurs inscrits ou non et les informations sur les planifications des labos. 

> [!NOTE]
> Bien que la plupart des aspects d’administration du tableau de bord et du [site web d’Azure Lab Services](https://labs.azure.com/) soient visibles aux enseignants, les autorisations spécifiques à votre rôle peuvent avoir un impact sur votre capacité à modifier certains critères du tableau de bord. Si vous rencontrez un problème avec la configuration spécifique de votre labo, contactez votre administrateur CTE.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="Portail Azure Lab Services":::

### <a name="examine-a-dashboard"></a>Examiner un tableau de bord

1. Accédez au [site web d’Azure Lab Services](https://labs.azure.com/) et connectez-vous.
1. Sélectionnez votre labo.
1. Vous verrez un **tableau de bord** sur le côté gauche de la fenêtre. Cliquez sur **Tableau de bord** pour afficher un certain nombre de vignettes dans votre tableau de bord.
1. Sous la vignette **Coûts et facturation**, il existe également des vignettes pour les modèles, les pools de machines virtuelles, les utilisateurs et les planifications, qui vous permettent de modifier certains aspects et d’afficher plus de détails sur le labo de classe.

    * Modèle : décrit la date à laquelle le modèle a été créé et la publication la plus récente. 
    * Pool de machines virtuelles : nombre de machines virtuelles attribuées et non attribuées.
    * Utilisateurs : nombre d’utilisateurs inscrits et d’utilisateurs qui ont été ajoutés au labo, mais qui ne sont pas inscrits.
    * Planifications : affiche les événements planifiés à venir pour le labo et un lien pour afficher plus d’événements.

Pour plus d’informations, consultez [Utiliser le tableau de bord](use-dashboard.md).

### <a name="manually-starting-vms"></a>Démarrage manuel des machines virtuelles

1. À partir de la page **Pool de machines virtuelles**, vous pouvez démarrer toutes les machines virtuelles d’un labo en cliquant sur le bouton **Démarrer tout** en haut de la page.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="Démarrer vos machines virtuelles":::
1. Vous pouvez démarrer des machines virtuelles individuelles en cliquant sur le bouton bascule d’état. 

    Le bouton bascule indique **Démarrage** lorsque la machine virtuelle démarre, puis **En cours d’exécution** une fois que la machine virtuelle a démarré.
1. Vous pouvez également sélectionner un certain nombre de machines virtuelles en utilisant les cases à cocher à gauche de la colonne **Nom**. 

    Une fois que vous avez sélectionné les machines virtuelles souhaitées, cliquez sur le bouton **Démarrer** en haut de l’écran.
1. Une fois qu’elles ont démarré, vous pouvez cliquer sur le bouton **Arrêter tout** pour arrêter toutes les machines virtuelles.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="Arrêter vos machines virtuelles":::

### <a name="stopping-and-resetting-vms"></a>Arrêt et réinitialisation des machines virtuelles

* Vous pouvez arrêter des machines virtuelles individuelles en cliquant sur le bouton bascule d’état.
* Vous pouvez également arrêter plusieurs machines virtuelles en utilisant les cases à cocher et en cliquant sur le bouton « Arrêter » en haut de l’écran.

Si un étudiant rencontre des difficultés à se connecter à la machine virtuelle, ou si la machine virtuelle doit être réinitialisée pour une raison quelconque, vous pouvez utiliser la fonction de réinitialisation.
1. Pour réinitialiser une ou plusieurs machines virtuelles, sélectionnez-les à l’aide des cases à cocher, puis cliquez sur le bouton **Réinitialiser** en haut de la page.
1. Dans la fenêtre contextuelle, cliquez sur **Réinitialiser**.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="Réinitialiser votre machine virtuelle":::

    > [!NOTE]
    > L’activation d’une machine virtuelle d’étudiant n’aura pas d’incidence sur le quota de l’étudiant. Le quota des utilisateurs spécifie le nombre d’heures de labo disponibles pour l’utilisateur en dehors des heures de cours planifiées.

### <a name="connect-to-vms"></a>Se connecter aux machines virtuelles

Les enseignants peuvent se connecter à la machine virtuelle d’un étudiant tant qu’elle est sous tension et que l’étudiant n’est PAS connecté à la machine virtuelle. En vous connectant à la machine virtuelle, vous serez en mesure d’accéder aux fichiers locaux sur la machine virtuelle et d’aider les étudiants à résoudre les problèmes.

1. Pour vous connecter à la machine virtuelle de l’étudiant, pointez la souris sur la machine virtuelle dans la liste et cliquez sur le bouton **Connexion**. 
1. Suivez ensuite le guide de démarrage pour les étudiants pour Chromebook, Mac ou PC.

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="Se connecter au bouton de machines virtuelles d’étudiant":::

## <a name="manage-users-in-a-lab"></a>Gérer les utilisateurs dans un labo

Les enseignants sont en mesure d’ajouter des utilisateurs étudiants à un labo et de surveiller leurs heures de quota. Pour savoir comment ajouter et inscrire des utilisateurs par adresse e-mail ou avec une liste de type feuille de calcul, consultez [Ajout et gestion d’utilisateurs de labo](how-to-configure-student-usage.md).

Après avoir invité des utilisateurs ou partagé le lien, vous pouvez identifier les utilisateurs qui se sont bien inscrits sur la page **Utilisateurs** dans la colonne **État**. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser les ressources que vous avez créées dans ce démarrage rapide, supprimez-les.

## <a name="next-steps"></a>Étapes suivantes

[Configurer un compte de laboratoire](tutorial-setup-lab-account.md)
