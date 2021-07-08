---
title: Options de groupe et de filtre dans Azure Cost Management
description: Cet article explique comment utiliser les options de groupe et de filtre dans Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 06/08/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 7422f41b617eb6d28a7a7667aecd129b24ac22a1
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110679459"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>Options de groupe et de filtre dans l’analyse des coûts

L’analyse des coûts dispose de nombreuses options de regroupement et de filtrage. Cet article vous aide à comprendre à quel moment les utiliser.

Pour visionner une vidéo sur les options de regroupement et de filtrage, regardez la vidéo [Cost Management - Génération de rapports en fonction des dimensions et des balises](https://www.youtube.com/watch?v=2Vx7V17zbmk). Pour regarder d’autres vidéos, consultez la [chaîne YouTube relative à Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>Propriétés de groupe et de filtre

Le tableau suivant répertorie quelques options courantes de regroupement et de filtrage disponibles dans l’analyse des coûts, et indique à quel moment les utiliser.

| Propriété | Quand l’utiliser | Notes |
| --- | --- | --- |
| **Zones de disponibilité** | Décompose les coûts AWS par zone de disponibilité. | S’applique uniquement aux étendues et aux groupes d’administration AWS. Les données Azure ne comportent pas de zone de disponibilité. Elles s’affichent avec le libellé **Aucune zone de disponibilité**. |
| **Période de facturation** | Décompose les coûts de paiement à l’utilisation selon le mois où ils ont été (ou seront) facturés. | Utilisez **Période de facturation** pour avoir une représentation précise des frais PAYG facturés. Incluez 2 jours supplémentaires avant et après la période de facturation si vous filtrez sur une plage de dates personnalisée. La limitation aux dates exactes de la période de facturation ne correspondra pas à la facture. Affiche les coûts de toutes les factures de la période de facturation. Utilisez **ID de facture** pour filtrer sur une facture spécifique. S’applique uniquement aux abonnements PAYG, car EA et MCA sont facturés par mois calendaires. Les comptes EA/MCA peuvent utiliser les mois du calendrier dans le sélecteur de dates ou la granularité mensuelle pour atteindre le même objectif. |
| **Type de dépense** | Pour décomposer selon le type de dépense : utilisation, achat, remboursement ou réservation non utilisée. | Les achats de réservation et les remboursements sont disponibles seulement pour les coûts réels, mais pas pour les coûts amortis. Les coûts liés aux réservations non utilisées s’affichent uniquement lors e la consultation des coûts amortis. |
| **Département** | Décompose les coûts par département EA. | Disponible uniquement pour EA et les groupes d’administration. Les abonnements PAYG ne comportent pas de département. Ils s’affichent avec le libellé **Aucun département** ou **Non affecté**. |
| **Compte d’inscription** | Pour décomposer les coûts par propriétaire de compte EA. | Disponible seulement pour les comptes, les départements et groupes d’administration EA. Les abonnements PAYG ne comportent pas de comptes d’inscription EA. Ils s’affichent avec le libellé **Aucun compte d’inscription EA** ou **Non affecté**. |
| **Fréquence** | Pour décomposer les coûts selon qu’ils sont uniques, récurrents ou basés sur l’utilisation. | |
| **ID de facture** | Décompose les coûts par facture émise. | Les frais non facturés ne possèdent pas encore d’ID de facture. Par ailleurs, les coûts EA ne comportent pas de détails de facturation. Ils s’affichent avec le libellé **Aucun ID de facture**.  |
| **Lieu** | Décompose les coûts par emplacement des ressources ou par région. | Les achats et l’utilisation de la place de marché peuvent apparaître avec le libellé **Non attribué** ou **Aucun emplacement de ressources**. |
| **Compteur** | Décompose les coûts par compteur d’utilisation. | Les achats et l’utilisation de la Place de marché s’affichent avec le libellé **Aucun compteur**. Reportez-vous à **Type de frais** pour identifier les achats et à **Type d’éditeur** pour identifier les frais de la Place de marché. |
| **opération** | Décompose les coûts AWS par opération. | S’applique uniquement aux étendues et aux groupes d’administration AWS. Les données Azure ne comportent pas d’opérations. Elles s’affichent avec le libellé **Aucune opération**. Utilisez plutôt **Compteur**. |
| **Modèle de tarification** | Décompose les coûts en fonction de l’utilisation à la demande, à la réservation ou ponctuelle. | Les achats apparaissent avec le libellé **OnDemand**. Si vous voyez **Non applicable**, regroupez par **Réservation** pour déterminer si l’utilisation est « Réservation » ou « À la demande », par **Type de frais** pour identifier les achats.
| **Fournisseur** | Pour décomposer les coûts liés à AWS et liés à Azure. | Disponible uniquement pour les groupes d’administration. |
| **Type d’éditeur** | Pour décomposer les coûts selon qu’ils proviennent d’AWS, d’Azure ou de la Place de marché. |  |
| **Réservation** | Pour décomposer les coûts par réservation. | Les utilisations et achats non associés à une réservation s’affichent avec le libellé **Aucune réservation**. Regroupez par **Type d’éditeur** pour identifier les autres achats Azure, AWS ou Place de marché. |
| **Ressource** | Pour décomposer les coûts par ressource. | Les achats effectués sur la place de marché s’affichent avec le libellé **Autres achats sur la place de marché**, et les achats Azure (Réservations, frais de support, etc.) avec le libellé **Autres achats Azure**. Regroupez ou filtrez les données par **Type d’éditeur** pour identifier les autres achats Azure, AWS ou Place de marché. |
| **Groupe de ressources** | Pour décomposer les coûts par groupe de ressources. | Ni les achats, ni les ressources de locataire non associées à des abonnements, ni les ressources d’abonnement non déployées sur un groupe de ressources, ni les ressources classiques ne comportent de groupe de ressources. Ils s’affichent avec le libellé **Autres achats sur la place de marché**, **Autres achats Azure**, **Autres ressources de locataire**, **Autres ressources d’abonnement**, **$system** ou **Autres frais**. |
| **Type de ressource** | Pour décomposer les coûts par type de ressource. | Ni les achats ni les services classiques ne comportent de type de ressource Azure Resource Manager. Ils s’affichent avec le libellé **Autres**, **Services classiques** ou **Aucun type de ressource**. |
| **Nom du service** ou **Catégorie du compteur** | Pour décomposer les coûts par service Azure. | Les achats et l’utilisation de la Place de marché s’affichent avec le libellé **Aucun nom de service** ou **Non affecté**. |
| **Niveau de service** ou **Sous-catégorie de compteur** | Pour décomposer les coûts par sous-classification du compteur d’utilisation Azure. | Les achats et l’utilisation de la Place de marché sont vides ou s’affichent avec le libellé **Non affecté**. |
| **Abonnement** | Décompose les coûts par abonnement Azure et par compte AWS lié. | Les achats et les ressources de locataire peuvent s’afficher avec le libellé **Aucun abonnement**. |
| **Tag** | Pour décomposer les coûts par valeur d’étiquette pour une clé d’étiquette spécifique. | Ni les achats, ni les ressources de locataire non associées à des abonnements, ni les ressources d’abonnement non déployées sur un groupe de ressources, ni les ressources classiques ne peuvent être étiquetés. Ils s’affichent avec le libellé **Étiquettes non disponibles**. Les services qui ne comportent pas d’étiquettes dans les données d’utilisation s’affichent avec le libellé **Étiquettes non prises en charge**. Les cas restants dans lesquels aucune étiquette n’est spécifiée sur une ressource s’affichent avec le libellé **Non étiqueté**. Pour plus d’informations, consultez [Prise en charge des étiquettes pour chaque type de ressource](../../azure-resource-manager/management/tag-support.md). |

Pour plus d’informations sur ces termes, consultez [Comprendre les termes utilisés dans le fichier Utilisation et frais Azure](../understand/understand-usage.md).

## <a name="next-steps"></a>Étapes suivantes

- [Démarrer l’analyse des coûts](./quick-acm-cost-analysis.md)
