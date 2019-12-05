---
title: Voir et gérer les fournisseurs de services
description: Les clients peuvent utiliser la page Fournisseurs de services du portail Azure pour afficher des informations sur les fournisseurs de services, les offres de fournisseurs de services et les ressources déléguées.
ms.date: 11/15/2019
ms.topic: conceptual
ms.openlocfilehash: 453f5d3ba4ed5c49b5e71c7530298bc0a03c73ee
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463817"
---
# <a name="view-and-manage-service-providers"></a>Voir et gérer les fournisseurs de services

Les clients peuvent utiliser la page **Fournisseurs de services** du [portail Azure](https://portal.azure.com) pour afficher des informations sur les fournisseurs de services et les offres de fournisseurs de services, déléguer des ressources spécifiques via la [gestion des ressources déléguées Azure](../concepts/azure-delegated-resource-management.md) et acheter des offres de fournisseurs de services supplémentaires. Si nous faisons référence ici aux fournisseurs de services et aux clients, des entreprises gérant plusieurs locataires peuvent suivre le même processus pour consolider leur expérience de gestion.

Pour accéder à la page **Fournisseurs de services** sur le portail Azure, le client peut sélectionner **Tous les services**, puis rechercher et sélectionner **Fournisseurs de services**. Il peut également la trouver en tapant « Fournisseurs de services » dans la zone de recherche dans la partie supérieure du portail Azure.

N’oubliez pas que la page **Fournisseurs de services** affiche des informations uniquement sur les fournisseurs de services qui ont accès aux abonnements ou aux groupes de ressources du client via la gestion des ressources déléguées Azure. Si un client travaille avec des fournisseurs de services supplémentaires qui n’utilisent pas la gestion des ressources déléguées Azure pour accéder aux ressources du client, les informations sur ces fournisseurs de services n’apparaissent pas ici.

> [!NOTE]
> Les fournisseurs de services peuvent afficher des informations sur leurs clients en accédant **Mes clients** sur le portail Azure. Pour plus d’informations, consultez [Voir et gérer les clients et les ressources déléguées](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Afficher les détails du fournisseur de services

Pour afficher des informations sur les fournisseurs de services avec lesquels un client travaille, le client peut sélectionner **Offres de fournisseur** sur le côté gauche de la page **Fournisseurs de services**.

Pour chaque offre de fournisseur de services, le client voit le nom du fournisseur de services et l’offre qui y est associée, ainsi que le nom que le client a entré pendant le processus d’intégration.

Dans la colonne **Délégations**, le client voit combien d’abonnements ou groupes de ressources ont été délégués au fournisseur de services pour cette offre. Le fournisseur de services est en mesure d’accéder à ces abonnements ou groupes de ressources, ainsi que de les gérer, en fonction des niveaux d’accès spécifiés dans l’offre.

## <a name="delegate-resources"></a>Déléguer des ressources

Pour qu’un fournisseur de services puisse accéder aux ressources d’un client et les gérer, celles-ci doivent être déléguées. Si un client a accepté une offre mais n’a pas encore délégué de ressources, il voit une note s’afficher en haut de la section des **Offres de fournisseur**. Cela lui permet de savoir qu’il doit intervenir pour que le fournisseur de services ne puisse accéder à ses ressources.

Pour déléguer des abonnements ou des groupes de ressources :

1. Cochez la case correspondant à la ligne contenant le fournisseur de services, l’offre et le nom. Sélectionnez ensuite **Déléguer des ressources** en haut de l’écran.
1. Dans la section **Détails de l’offre** de la page **Déléguer des ressources**, examinez les détails relatifs au fournisseur de services et à l’offre. Pour réviser les attributions de rôles pour l’offre, sélectionnez **Cliquer ici pour voir les détails de l’offre sélectionnée**.
1. Dans la section **Déléguer**, sélectionnez **Déléguer des abonnements** ou **Déléguer des groupes de ressources**.
1. Choisissez les abonnements ou groupes de ressources que aimeriez déléguer pour cette offre, puis sélectionnez **Ajouter**.
1. Activez la case à cocher en bas de la page pour confirmer que vous souhaitez accorder à ce fournisseur de services l’accès aux ressources que vous avez sélectionnées, puis sélectionnez **Déléguer**.

## <a name="add-or-remove-service-provider-offers"></a>Ajouter ou supprimer des offres de fournisseurs de services

Un client peut ajouter une nouvelle offre de fournisseur de services à partir de la page **Offres de fournisseur** en sélectionnant **Ajouter une offre**. Le fournisseur de services doit avoir publié une offre pour ce client. Le client peut ensuite sélectionner cette offre dans l’écran **Offres privées**, puis choisir **Créer**.

Si le client souhaite supprimer une offre de fournisseur de services, il peut sélectionner l’icône Corbeille sur la ligne de cette offre. Après confirmation de la suppression, ce fournisseur de services n’a plus accès aux ressources du client qui étaient précédemment déléguées pour cette offre.

## <a name="update-service-provider-offers"></a>Mettre à jour les offres de fournisseur de services

Une fois qu’un client a ajouté une offre, un fournisseur de services peut publier une version mise à jour de cette même offre sur la place de marché Microsoft Azure. Par exemple, ils souhaiteront peut-être ajouter une nouvelle définition de rôle. Si une nouvelle version de l’offre a été publiée, la page **Offres du fournisseur** comprend une icône de « mise à jour » dans la ligne de l'offre en question. Le client peut alors sélectionner cette icône pour voir les différences entre la version actuelle de l'offre et la nouvelle.

 ![Icône Mettre à jour une offre](../media/update-offer.jpg)

Après examen des les modifications, le client peut décider de mettre à jour vers la nouvelle version. Dès qu’il le fait, les autorisations et autres paramètres spécifiés dans la nouvelle version s’appliquent à tous les abonnements et/ou groupes de ressources qui ont été délégués pour cette offre.

## <a name="view-delegations"></a>Afficher les délégations

Les délégations représentent les attributions de rôles qui accordent des autorisations au fournisseur de services pour les ressources qu’un client a déléguées. Pour afficher ces informations, sélectionnez **Délégations** sur le côté gauche de la page **Fournisseurs de services**.

Les filtres en haut de la page vous permettent de trier et regrouper vos informations de délégation, ou de filtrer sur des clients, offres ou mots clés spécifiques.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur [Azure Lighthouse](../overview.md).
- Découvrez comment les fournisseurs de services peuvent [voir et gérer les clients](view-manage-customers.md) en accédant à **Mes clients** sur le portail Azure.