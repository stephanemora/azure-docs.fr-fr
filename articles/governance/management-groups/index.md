---
title: Organiser vos ressources avec des groupes d’administration Azure
description: Découvrez les groupes d’administration et comment les utiliser.
author: rthorn17
manager: rithorn
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/18/2018
ms.author: rithorn
ms.openlocfilehash: d031059f9811cedb703fec4920e00fd1b2e3f877
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47045343"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organiser vos ressources avec des groupes d’administration Azure

Si votre organisation dispose de plusieurs abonnements, vous pouvez avoir besoin d’un moyen de gérer efficacement l’accès, les stratégies et la conformité de ces abonnements. Les groupes d’administration Azure fournissent un niveau d’étendue au-delà des abonnements. Vous organisez les abonnements en conteneurs appelés « groupes d’administration » et vous appliquez vos conditions de gouvernance aux groupes d’administration. Tous les abonnements d’un groupe d’administration héritent automatiquement des conditions appliquées à ce groupe d’administration. Les groupes d’administration vous permettent une gestion de qualité professionnelle à grande échelle, quel que soit le type de vos abonnements.

Par exemple, vous pouvez appliquer des stratégies à un groupe d’administration afin de limiter les régions disponibles pour la création de machines virtuelles. Une telle stratégie s’appliquerait alors à tous les groupes d’administration, abonnements et ressources sous ce groupe d’administration en autorisant uniquement la création de machines virtuelles dans une région donnée.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hiérarchie des groupes d’administration et des abonnements

Vous pouvez créer une structure flexible de groupes d’administration et d’abonnements pour organiser vos ressources dans une hiérarchie à des fins de stratégie unifiée et de gestion de l’accès. Le diagramme suivant montre un exemple de création d’une hiérarchie pour la gouvernance à l’aide des groupes d’administration.

![arborescence](./media/MG_overview.png)

En créant une hiérarchie, comme dans cet exemple, vous pouvez appliquer une stratégie, par exemple les emplacements de machine virtuelle limités à la région USA Ouest sur le groupe « Groupe d’administration de l’équipe d’infrastructure » pour les stratégies de sécurité et de conformité aux réglementations internes. Cette stratégie héritera sur les deux abonnements EA dans ce groupe d’administration et s’applique à toutes les machines virtuelles dans ces abonnements. Comme cette stratégie hérite le groupe d’administration pour les abonnements, cette stratégie de sécurité ne peut pas être modifiée par le propriétaire de ressources ou d’abonnement permettant une gouvernance améliorée.

Un autre scénario où vous pouvez utiliser les groupes d’administration consiste à fournir un accès utilisateur à plusieurs abonnements. En déplaçant plusieurs abonnements dans ce groupe d’administration, vous avez la possibilité de créer une affectation [RBAC](../../role-based-access-control/overview.md) sur le groupe d’administration, qui héritera de l’accès à tous les abonnements.
Sans devoir scripter les affectations RBAC sur plusieurs abonnements, une assignation sur le groupe d’administration peut autoriser les utilisateurs à accéder à tout ce que dont ils ont besoin.

### <a name="important-facts-about-management-groups"></a>Faits importants sur les groupes d’administration

- 10 000 groupes d’administration peuvent être pris en charge dans un seul annuaire.
- Une arborescence de groupes d’administration peut prendre en charge jusqu’à six niveaux de profondeur.
  - Cette limite n’inclut ni le niveau racine ni le niveau d’abonnement.
- Chaque groupe d’administration et chaque abonnement ne prennent en charge qu’un seul parent.
- Chaque groupe d’administration peut avoir plusieurs enfants.
- Dans chaque annuaire, tous les abonnements et groupes d’administration sont contenus dans une même hiérarchie. Pour connaître les exceptions relatives à la préversion, consultez [Faits importants sur le groupe d’administration racine](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Groupe d’administration racine pour chaque annuaire

Chaque annuaire reçoit un groupe d’administration de niveau supérieur unique appelé groupe d’administration « racine ».
Ce groupe d’administration racine est intégré à la hiérarchie et contient tous les groupes d’administration et abonnements. Il permet d’appliquer des stratégies globales et des affectations RBAC au niveau de l’annuaire. L’[administrateur de l’annuaire doit élever ses privilèges](../../role-based-access-control/elevate-access-global-admin.md) pour être propriétaire de ce groupe racine à l’initiale. Une fois que l’administrateur est propriétaire du groupe, il peut affecter un rôle RBAC à d’autres utilisateurs ou groupes de l’annuaire pour qu’ils gèrent la hiérarchie.

### <a name="important-facts-about-the-root-management-group"></a>Faits importants sur le groupe d’administration racine

- Le nom et l’ID du groupe d’administration racine sont fournis par défaut. Le nom d’affichage peut être mis à jour à tout moment au sein du portail Azure.
  - Le nom sera « Groupe racine du locataire ».
  - L’ID sera l’ID Azure Active Directory.
- Le groupe d’administration racine ne peut pas être déplacé ni supprimé, contrairement aux autres groupes d’administration.  
- Tous les abonnements et groupes d’administration sont contenus dans le groupe d’administration racine de l’annuaire.
  - Toutes les ressources de l’annuaire sont contenues dans le groupe d’administration racine à des fins de gestion globale.
  - Lors de leur création, les nouveaux abonnements sont attribués par défaut au groupe d’administration racine.
- Tous les clients Azure peuvent voir le groupe d’administration racine, mais tous ne peuvent pas le gérer.
  - Toute personne ayant accès à un abonnement peut voir où celui-ci se trouve dans la hiérarchie.  
  - Personne ne reçoit par défaut l’accès au groupe d’administration racine. Les administrateurs généraux d’annuaires sont les seuls utilisateurs à pouvoir élever leurs privilèges pour obtenir l’accès.  Une fois l’accès obtenu, les administrateurs d’annuaires peuvent attribuer un rôle RBAC aux autres utilisateurs qu’ils doivent gérer.  

> [!NOTE]
> Si votre annuaire utilisait déjà le service des groupes d’administration au 25/6/2018, il peut ne pas être configuré avec tous les abonnements de la hiérarchie. L’équipe du groupe d’administration va mettre à jour rétroactivement tous les annuaires qui utilisent les groupes d’administration de la préversion publique depuis une date antérieure au mois de juillet/août 2018. Tous les abonnements des annuaires deviendront des abonnements enfants sous le groupe d’administration racine.
>
> Si vous avez des questions sur ce processus rétroactif, contactez : managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>Configuration initiale des groupes d’administration

Lorsqu’un utilisateur commence à utiliser les groupes d’administration, un processus de configuration initiale est lancé. La première étape est la création du groupe d’administration racine dans l’annuaire. Une fois le groupe créé, tous les abonnements existants de l’annuaire deviennent des enfants du groupe d’administration racine. Ce processus permet de faire en sorte que l’annuaire ne contienne qu’une seule hiérarchie de groupes gestion. Le fait de n’avoir qu’une seule hiérarchie par annuaire permet aux administrateurs d’appliquer un accès global et des stratégies que les autres utilisateurs de l’annuaire ne peuvent pas contourner. Avec une seule hiérarchie par annuaire, tout élément attribué au niveau racine est appliqué à l’ensemble des groupes d’administration, abonnements, groupes de ressources et ressources de l’annuaire.

> [!IMPORTANT]
> Les attributions d’accès utilisateur et de stratégies effectuées au niveau du groupe d’administration racine **s’appliquent à toutes les ressources de l’annuaire**.
> Pour cette raison, tous les utilisateurs doivent évaluer la nécessité de définir des ressources dans cette étendue.
> Les attributions d’accès utilisateur et de stratégies ne doivent être obligatoires que pour cette étendue.  
  
## <a name="management-group-access"></a>Accès aux groupes d’administration

Les groupes d’administration Azure prennent en charge le [contrôle d’accès en fonction du rôle (RBAC) Azure](../../role-based-access-control/overview.md) pour tous les accès aux ressources et toutes les définitions de rôles.
Les ressources enfants qui existent dans la hiérarchie héritent de ces autorisations. Vous pouvez attribuer n’importe quel rôle RBAC intégré à un groupe d’administration, qui héritera ensuite de la hiérarchie des ressources.
Par exemple, un contributeur de machine virtuelle avec rôle RBAC peut être affecté à un groupe d’administration. Ce rôle n’a aucun effet sur le groupe d’administration, mais il hérite de toutes les machines virtuelles situées sous ce groupe d’administration.

Le graphique suivant montre la liste des rôles, ainsi que les actions prises en charge par les groupes d’administration.

| Nom du rôle RBAC             | Créer | Renommer | Déplacer | Supprimer | Attribuer l’accès | Attribuer la stratégie | Lire  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Propriétaire                       | X      | X      | X    | X      | X             | X             | X     |
|Contributeur                 | X      | X      | X    | X      |               |               | X     |
|Contributeur MG*             | X      | X      | X    | X      |               |               | X     |
|Lecteur                      |        |        |      |        |               |               | X     |
|Lecteur MG*                  |        |        |      |        |               |               | X     |
|Contributeur de la stratégie de ressource |        |        |      |        |               | X             |       |
|Administrateur de l'accès utilisateur   |        |        |      |        | X             |               |       |

* : Contributeur MG et lecteur MG autorisent uniquement les utilisateurs à effectuer ces actions sur l’étendue du groupe d’administration.  

### <a name="custom-rbac-role-definition-and-assignment"></a>Définition et attribution d’un rôle RBAC personnalisé

Les rôles RBAC personnalisés ne sont pas pris en charge par les groupes d’administration. Pour connaître le statut de cette prise en charge, consultez le [forum de commentaires des groupes d’administration](https://aka.ms/mgfeedback).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les groupes d’administration, consultez :

- [Créer des groupes d’administration pour organiser les ressources Azure](create.md)
- [Guide pratique pour modifier, supprimer ou gérer vos groupes d’administration](manage.md)
- [Installer le module Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Passer en revue les spécifications de l’API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Installer l’extension Azure CLI](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)