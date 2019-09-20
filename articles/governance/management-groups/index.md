---
title: Organiser vos ressources avec des groupes d’administration Azure - Azure Governance
description: Découvrez les groupes d’administration, le fonctionnement des autorisations et leur utilisation.
author: rthorn17
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: governance
ms.date: 04/22/2019
ms.author: rithorn
ms.topic: overview
ms.openlocfilehash: dba6334815f0c86a800b38ad09487f719857f993
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104230"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organiser vos ressources avec des groupes d’administration Azure

Si votre organisation dispose de plusieurs abonnements, vous pouvez avoir besoin d’un moyen de gérer efficacement l’accès, les stratégies et la conformité de ces abonnements. Les groupes d’administration Azure fournissent un niveau d’étendue au-delà des abonnements. Vous organisez les abonnements en conteneurs appelés « groupes d’administration » et vous appliquez vos conditions de gouvernance aux groupes d’administration. Tous les abonnements d’un groupe d’administration héritent automatiquement des conditions appliquées à ce groupe d’administration. Les groupes d’administration vous permettent une gestion de qualité professionnelle à grande échelle, quel que soit le type de vos abonnements. Tous les abonnements au sein d’un même groupe d’administration doivent approuver le même locataire Azure Active Directory.

Par exemple, vous pouvez appliquer des stratégies à un groupe d’administration afin de limiter les régions disponibles pour la création de machines virtuelles. Une telle stratégie s’appliquerait alors à tous les groupes d’administration, abonnements et ressources sous ce groupe d’administration en autorisant uniquement la création de machines virtuelles dans une région donnée.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hiérarchie des groupes d’administration et des abonnements

Vous pouvez créer une structure flexible de groupes d’administration et d’abonnements pour organiser vos ressources dans une hiérarchie à des fins de stratégie unifiée et de gestion de l’accès. Le diagramme suivant montre un exemple de création d’une hiérarchie pour la gouvernance à l’aide des groupes d’administration.

![Exemple d’une arborescence hiérarchique de groupes de gestion](./media/tree.png)

Vous pouvez créer une hiérarchie qui applique une stratégie, par exemple, qui limite les emplacements de machines virtuelles à la région USA Ouest dans le groupe appelé « Production ». Cette stratégie héritera sur les deux abonnements EA dans ce groupe d’administration et s’applique à toutes les machines virtuelles dans ces abonnements. Cette stratégie de sécurité ne peut pas être modifiée par le propriétaire de ressources ou d’abonnement permettant une gouvernance améliorée.

Un autre scénario où vous pouvez utiliser les groupes d’administration consiste à fournir un accès utilisateur à plusieurs abonnements. En déplaçant plusieurs abonnements dans ce groupe d’administration, vous pouvez créer une affectation de [contrôle d’accès en fonction du rôle](../../role-based-access-control/overview.md) (RBAC) dans le groupe d’administration, qui héritera de l’accès à tous les abonnements.
Une affectation sur le groupe d’administration peut autoriser les utilisateurs à accéder à tout ce que dont ils ont besoin, au lieu de créer un script RBAC sur différents abonnements.

### <a name="important-facts-about-management-groups"></a>Faits importants sur les groupes d’administration

- 10 000 groupes d’administration peuvent être pris en charge dans un seul annuaire.
- Une arborescence de groupes d’administration peut prendre en charge jusqu’à six niveaux de profondeur.
  - Cette limite n’inclut ni le niveau racine ni le niveau d’abonnement.
- Chaque groupe d’administration et chaque abonnement ne prennent en charge qu’un seul parent.
- Chaque groupe d’administration peut avoir de nombreux enfants.
- Dans chaque annuaire, tous les abonnements et groupes d’administration sont dans une même hiérarchie. Consultez [Faits importants sur le groupe d’administration racine](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Groupe d’administration racine pour chaque annuaire

Chaque annuaire reçoit un groupe d’administration de niveau supérieur unique appelé groupe d’administration « racine ».
Ce groupe d’administration racine est intégré à la hiérarchie et contient tous les groupes d’administration et abonnements. Il permet d’appliquer des stratégies globales et des affectations RBAC au niveau de l’annuaire. L’[administrateur général d’Azure AD doit élever ses privilèges](../../role-based-access-control/elevate-access-global-admin.md) au rôle Administrateur d’accès utilisateur de ce groupe racine au départ. Une fois l’accès obtenu, l’administrateur peut attribuer un rôle RBAC à d’autres utilisateurs ou groupes de l’annuaire pour gérer la hiérarchie. En tant qu’administrateur, vous pouvez attribuer votre propre compte comme propriétaire du groupe d’administration racine.

### <a name="important-facts-about-the-root-management-group"></a>Faits importants sur le groupe d’administration racine

- Par défaut, le nom d’affichage du groupe d’administration racine est **Groupe racine de locataire**. L’ID est l’ID Azure Active Directory.
- Pour changer le nom d’affichage, votre compte doit avoir le rôle Propriétaire ou Contributeur sur le groupe d’administration racine. Pour connaître les étapes permettant de changer le nom, consultez [Changer le nom d’un groupe d’administration](manage.md#change-the-name-of-a-management-group).
- Le groupe d’administration racine ne peut pas être déplacé ni supprimé, contrairement aux autres groupes d’administration.  
- Tous les abonnements et groupes d’administration sont contenus dans le groupe d’administration racine de l’annuaire.
  - Toutes les ressources de l’annuaire sont contenues dans le groupe d’administration racine à des fins de gestion globale.
  - Lors de leur création, les nouveaux abonnements sont attribués par défaut au groupe d’administration racine.
- Tous les clients Azure peuvent voir le groupe d’administration racine, mais tous ne peuvent pas le gérer.
  - Toute personne ayant accès à un abonnement peut voir où celui-ci se trouve dans la hiérarchie.  
  - Personne ne reçoit par défaut l’accès au groupe d’administration racine. Les administrateurs généraux Azure AD sont les seuls utilisateurs à pouvoir élever leurs privilèges pour obtenir l’accès.  Une fois qu’ils ont accès au groupe d’administration racine, les administrateurs généraux peuvent attribuer un rôle RBAC aux autres utilisateurs pour le gérer.  

> [!IMPORTANT]
> Les attributions d’accès utilisateur et de stratégies effectuées au niveau du groupe d’administration racine **s’appliquent à toutes les ressources de l’annuaire**.
> Pour cette raison, tous les utilisateurs doivent évaluer la nécessité de définir des ressources dans cette étendue.
> Les attributions d’accès utilisateur et de stratégies ne doivent être obligatoires que pour cette étendue.  

## <a name="initial-setup-of-management-groups"></a>Configuration initiale des groupes d’administration

Lorsqu’un utilisateur commence à utiliser les groupes d’administration, un processus de configuration initiale est lancé. La première étape est la création du groupe d’administration racine dans l’annuaire. Une fois le groupe créé, tous les abonnements existants de l’annuaire deviennent des enfants du groupe d’administration racine. Ce processus permet de faire en sorte que l’annuaire ne contienne qu’une seule hiérarchie de groupes gestion. Le fait de n’avoir qu’une seule hiérarchie par annuaire permet aux administrateurs d’appliquer un accès global et des stratégies que les autres utilisateurs de l’annuaire ne peuvent pas contourner. Tout élément attribué au niveau racine est appliqué à toute la hiérarchie, qui comprend tous les groupes d’administration, les abonnements, les groupes de ressources et les ressources de ce locataire Azure AD.

## <a name="trouble-seeing-all-subscriptions"></a>Difficultés pour afficher tous les abonnements

Quelques annuaires ayant commencé à utiliser des groupes d’administration de manière anticipée dans la préversion antérieure au 25 juin 2018 ont rencontré un problème où tous les abonnements n’étaient pas dans la hiérarchie. Le processus permettant d’avoir tous les abonnements dans la hiérarchie a été mis en place après l’attribution d’un rôle ou d’une stratégie au groupe d’administration racine de l’annuaire. 

### <a name="how-to-resolve-the-issue"></a>Que pouvez-vous faire pour résoudre le problème ?

Pour résoudre ce problème, deux options s’offrent à vous.

1. Supprimer toutes les affectations de rôle et de stratégie du groupe d’administration racine
   1. En supprimant toutes les affectations de stratégie et de rôle du groupe d’administration racine, le service renverra tous les abonnements dans la hiérarchie du prochain cycle pendant la nuit.  Ce processus permet de garantir qu’aucun accès n’est donné et qu’aucune stratégie n’est affectée accidentellement à tous les abonnements de locataires.
   1. La meilleure méthode pour effectuer ce processus sans affecter vos services consiste à appliquer les affectations de rôle ou de stratégie à un niveau en dessous du groupe d’administration racine. Vous pouvez ensuite supprimer toutes les affectations au niveau de la racine.
1. Appeler l’API directement afin de démarrer le processus de renvoi
   1. Tous les clients de l’annuaire peuvent appeler les API *TenantBackfillStatusRequest* ou *StartTenantBackfillRequest*. Lorsque l’API StartTenantBackfillRequest est appelée, elle lance le processus de configuration initiale de déplacement de tous les abonnements dans la hiérarchie. Ce processus démarre également l’application de tous les nouveaux abonnements en tant qu’enfants du groupe d’administration racine. Ce processus peut être effectué sans changer d’affectation au niveau racine. En appelant l’API, vous dites qu’une affectation de stratégie ou d’accès à la racine peut être appliquée à tous les abonnements.

Si vous avez des questions sur ce processus de renvoi, contactez : managementgroups@microsoft.com  
  
## <a name="management-group-access"></a>Accès aux groupes d’administration

Les groupes d’administration Azure prennent en charge le [contrôle d’accès en fonction du rôle (RBAC) Azure](../../role-based-access-control/overview.md) pour tous les accès aux ressources et toutes les définitions de rôles.
Les ressources enfants qui existent dans la hiérarchie héritent de ces autorisations. Vous pouvez attribuer n’importe quel rôle RBAC intégré à un groupe d’administration, qui héritera ensuite de la hiérarchie des ressources.
Par exemple, un contributeur de machine virtuelle avec rôle RBAC peut être affecté à un groupe d’administration. Ce rôle n’a aucun effet sur le groupe d’administration, mais il hérite de toutes les machines virtuelles situées sous ce groupe d’administration.

Le graphique suivant montre la liste des rôles, ainsi que les actions prises en charge par les groupes d’administration.

| Nom du rôle RBAC             | Créer | Renommer | Déplacer** | Supprimer | Attribuer l’accès | Attribuer la stratégie | Lire  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|Propriétaire                       | X      | X      | X      | X      | X             | X             | X     |
|Contributeur                 | X      | X      | X      | X      |               |               | X     |
|Contributeur MG*             | X      | X      | X      | X      |               |               | X     |
|Lecteur                      |        |        |        |        |               |               | X     |
|Lecteur MG*                  |        |        |        |        |               |               | X     |
|Contributeur de la stratégie de ressource |        |        |        |        |               | X             |       |
|Administrateur de l'accès utilisateur   |        |        |        |        | X             | X             |       |

* : Contributeur MG et lecteur MG autorisent uniquement les utilisateurs à effectuer ces actions sur l’étendue du groupe d’administration.  
** : Les attributions de rôles sur le groupe d’administration racine ne sont pas nécessaires pour déplacer un abonnement ou un groupe d’administration.  Consultez [Gérer vos ressources avec des groupes d’administration](manage.md) pour des détails sur le déplacement d’éléments dans la hiérarchie.

### <a name="custom-rbac-role-definition-and-assignment"></a>Définition et attribution d’un rôle RBAC personnalisé

Les rôles RBAC personnalisés ne sont pas pris en charge par les groupes d’administration. Pour connaître le statut de cette prise en charge, consultez le [forum de commentaires des groupes d’administration](https://aka.ms/mgfeedback).

## <a name="audit-management-groups-using-activity-logs"></a>Auditer les groupes d’administration à l’aide des journaux d’activité

Les groupes d’administration sont pris en charge dans le [journal d’activité Azure](../../azure-monitor/platform/activity-logs-overview.md). Vous pouvez rechercher dans tous les événements qui se produisent dans un groupe d’administration au même emplacement central, tout comme d’autres ressources Azure.  Par exemple, vous pouvez voir tous les changements d’attributions de rôles ou de stratégie apportés à un groupe d’administration spécifique.

![Journaux d’activité avec les groupes d’administration](media/al-mg.png)

Quand vous cherchez à interroger les groupes d’administration en dehors du portail Azure, l’étendue cible pour les groupes d’administration ressemble à **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les groupes d’administration, consultez :

- [Créer des groupes d’administration pour organiser les ressources Azure](create.md)
- [Guide pratique pour modifier, supprimer ou gérer vos groupes d’administration](manage.md)
- [Consulter les groupes d’administration dans le module Azure PowerShell Resources](/powershell/module/az.resources#resources)
- [Consulter les groupes d’administration dans l’API REST](/rest/api/resources/managementgroups)
- [Consulter les groupes d’administration dans Azure CLI](/cli/azure/account/management-group)
