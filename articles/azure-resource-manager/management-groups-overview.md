---
title: Organiser vos ressources avec des groupes d’administration Azure | Microsoft Docs
description: Découvrez les groupes d’administration et comment les utiliser.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/09/2018
ms.author: rithorn
ms.openlocfilehash: c8152a6c12c776806d9a17c5e434d825d6c91165
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38466641"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organiser vos ressources avec des groupes d’administration Azure

Si votre organisation dispose de plusieurs abonnements, vous pouvez avoir besoin d’un moyen de gérer efficacement l’accès, les stratégies et la conformité de ces abonnements. Les groupes d’administration Azure fournissent un niveau d’étendue au-delà des abonnements. Vous organisez les abonnements en conteneurs appelés « groupes d’administration » et vous appliquez vos conditions de gouvernance aux groupes d’administration. Tous les abonnements d’un groupe d’administration héritent automatiquement des conditions appliquées à ce groupe d’administration. Les groupes d’administration vous permettent une gestion de qualité professionnelle à grande échelle, quel que soit le type de vos abonnements.

La fonctionnalité de groupe d’administration est disponible dans une préversion publique. Pour commencer à utiliser des groupes d’administration, connectez-vous au [portail Azure](https://portal.azure.com), puis recherchez **Groupes d’administration** dans la section **Tous les services**.

Par exemple, vous pouvez appliquer des stratégies à un groupe d’administration afin de limiter les régions disponibles pour la création de machines virtuelles. Une telle stratégie s’appliquerait alors à tous les groupes d’administration, abonnements et ressources sous ce groupe d’administration en autorisant uniquement la création de machines virtuelles dans une région donnée.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hiérarchie des groupes d’administration et des abonnements

Vous pouvez créer une structure flexible de groupes d’administration et d’abonnements pour organiser vos ressources dans une hiérarchie à des fins de stratégie unifiée et de gestion de l’accès.
Le diagramme suivant illustre un exemple de hiérarchie qui comprend des groupes d’administration et des abonnements organisés en services.

![arborescence](media/management-groups/MG_overview.png)

En créant une hiérarchie regroupée par services, vous pouvez attribuer des rôles de [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) dont *héritent* les services de ce groupe d’administration. En utilisant des groupes d’administration, vous pouvez réduire votre charge de travail et le risque d’erreur en n’ayant à attribuer le rôle qu’une seule fois.

### <a name="important-facts-about-management-groups"></a>Faits importants sur les groupes d’administration

- 10 000 groupes d’administration peuvent être pris en charge dans un seul annuaire.
- Une arborescence de groupes d’administration peut prendre en charge jusqu’à six niveaux de profondeur.
  - Cette limite n’inclut ni le niveau racine ni le niveau d’abonnement.
- Chaque groupe d’administration et chaque abonnement ne prennent en charge qu’un seul parent.
- Chaque groupe d’administration peut avoir plusieurs enfants.
- Dans chaque annuaire, tous les abonnements et groupes d’administration sont contenus dans une même hiérarchie. Pour connaître les exceptions relatives à la préversion, consultez [Faits importants sur le groupe d’administration racine](#important-facts-about-the-root-management-group).

### <a name="preview-subscription-visibility-limitation"></a>Limitation de visibilité des abonnements aux préversions

Il existe actuellement une limitation dans la préversion ne vous permettant pas d’afficher les abonnements dont vous avez hérité l’accès. L’accès à l’abonnement est hérité, mais Azure Resource Manager n’est pas encore en mesure d’honorer l’accès hérité.  

L’utilisation de l’API REST pour obtenir des informations sur l’abonnement retourne les détails puisque vous y avez accès, mais les abonnements n’apparaissent ni dans le portail Azure ni dans Azure PowerShell.

Ce point est en cours de traitement et sera résolu avant que les groupes d’administration ne soient annoncés en tant que « Disponibilité générale ».  

### <a name="cloud-solution-provider-csp-limitation-during-preview"></a>Limitation du fournisseur de solutions Cloud pendant la préversion

Les partenaires du fournisseur de solutions Cloud rencontrent actuellement une limitation : ils ne peuvent ni créer ni gérer les groupes d’administration de leur client dans l’annuaire dudit client.  
Ce point est en cours de traitement et sera résolu avant que les groupes d’administration ne soient annoncés en tant que « Disponibilité générale ».

## <a name="root-management-group-for-each-directory"></a>Groupe d’administration racine pour chaque annuaire

Chaque annuaire reçoit un groupe d’administration de niveau supérieur unique appelé groupe d’administration « racine ». Ce groupe d’administration racine est intégré à la hiérarchie et contient tous les groupes d’administration et abonnements. Il permet d’appliquer des stratégies globales et des affectations RBAC au niveau de l’annuaire. L’[administrateur de l’annuaire doit élever ses privilèges](../role-based-access-control/elevate-access-global-admin.md) pour être propriétaire de ce groupe racine à l’initiale. Une fois que l’administrateur est propriétaire du groupe, il peut affecter un rôle RBAC à d’autres utilisateurs ou groupes de l’annuaire pour qu’ils gèrent la hiérarchie.  

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

>[!NOTE]
>Si votre annuaire utilisait déjà le service des groupes d’administration au 25/6/2018, il peut ne pas être configuré avec tous les abonnements de la hiérarchie. Au mois de juillet 2018, l’équipe du groupe d’administration va mettre à jour rétroactivement tous les annuaires qui utilisent les groupes d’administration de la préversion publique depuis une date antérieure au 25/6/2018. Tous les abonnements des annuaires deviendront des abonnements enfants sous le groupe d’administration racine.  
>
>Si vous avez des questions sur ce processus rétroactif, contactez : managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>Configuration initiale des groupes d’administration

Lorsqu’un utilisateur commence à utiliser les groupes d’administration, un processus de configuration initiale est lancé. La première étape est la création du groupe d’administration racine dans l’annuaire. Une fois le groupe créé, tous les abonnements existants de l’annuaire deviennent des enfants du groupe d’administration racine.  Ce processus permet de faire en sorte que l’annuaire ne contienne qu’une seule hiérarchie de groupes gestion.  Le fait de n’avoir qu’une seule hiérarchie par annuaire permet aux administrateurs d’appliquer un accès global et des stratégies que les autres utilisateurs de l’annuaire ne peuvent pas contourner. Avec une seule hiérarchie par annuaire, tout élément attribué au niveau racine est appliqué à l’ensemble des groupes d’administration, abonnements, groupes de ressources et ressources de l’annuaire.  

> [!IMPORTANT]
> Les attributions d’accès utilisateur et de stratégies effectuées au niveau du groupe d’administration racine **s’appliquent à toutes les ressources de l’annuaire**. Pour cette raison, tous les utilisateurs doivent évaluer la nécessité de définir des ressources dans cette étendue.  Les attributions d’accès utilisateur et de stratégies ne doivent être obligatoires que pour cette étendue.  
  
## <a name="management-group-access"></a>Accès aux groupes d’administration

Les groupes d’administration Azure prennent en charge le [contrôle d’accès en fonction du rôle (RBAC) Azure](../role-based-access-control/overview.md) pour tous les accès aux ressources et toutes les définitions de rôles. Les ressources enfants qui existent dans la hiérarchie héritent de ces autorisations. Vous pouvez attribuer n’importe quel rôle RBAC intégré à un groupe d’administration, qui héritera ensuite de la hiérarchie des ressources.  Par exemple, un contributeur de machine virtuelle avec rôle RBAC peut être affecté à un groupe d’administration. Ce rôle n’a aucun effet sur le groupe d’administration, mais il hérite de toutes les machines virtuelles situées sous ce groupe d’administration.  

Le graphique suivant montre la liste des rôles, ainsi que les actions prises en charge par les groupes d’administration.

| Nom du rôle RBAC             | Créer | Renommer | Déplacer | Supprimer | Attribuer l’accès | Attribuer la stratégie | Lire  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Propriétaire                       | X      | X      | X    | X      | X             |               | X     |
|Contributeur                 | X      | X      | X    | X      |               |               | X     |
|Lecteur                      |        |        |      |        |               |               | X     |
|Contributeur de la stratégie de ressource |        |        |      |        |               | X             |       |
|Administrateur de l'accès utilisateur   |        |        |      |        | X             |               |       |

### <a name="custom-rbac-role-definition-and-assignment"></a>Définition et attribution d’un rôle RBAC personnalisé

Les rôles RBAC personnalisés ne sont pas pris en charge par les groupes d’administration.  Pour connaître le statut de cette prise en charge, consultez le [forum de commentaires des groupes d’administration](https://aka.ms/mgfeedback).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les groupes d’administration, consultez :

- [Créer des groupes d’administration pour organiser les ressources Azure](management-groups-create.md)
- [Guide pratique pour modifier, supprimer ou gérer vos groupes d’administration](management-groups-manage.md)
- [Installer le module Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Passer en revue les spécifications de l’API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Installer l’extension Azure CLI](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
