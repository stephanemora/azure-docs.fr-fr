---
title: Gérer des zones DNS dans Azure DNS - Portail Azure | Microsoft Docs
description: Vous pouvez gérer des zones DNS à l’aide du portail Azure. Cet article décrit comment mettre à jour, supprimer et créer des zones DNS sur Azure DNS.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/28/2021
ms.author: rohink
ms.openlocfilehash: 58118480c16f2e318bab7435a79e27629880acc5
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108203193"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Gérer des zones DNS à l’aide du portail Azure

> [!div class="op_single_selector"]
> * [Portail](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure Classic CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Cet article vous montre comment gérer vos zones DNS avec le portail Azure. Vous pouvez également gérer vos zones DNS à l’aide de l’[interface de ligne de commande Azure](dns-operations-dnszones-cli.md) multiplateforme ou d’Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. En haut à gauche de l’écran, sélectionnez **Créer une ressource** et recherchez **Zone DNS**. Sélectionnez ensuite **Créer**.

    :::image type="content" source="./media/dns-operations-dnszones-portal/search-dns-zone.png" alt-text="Capture d’écran de la création d’une recherche de ressource pour une zone DNS.":::

1. Dans la page **Créer une zone DNS**, entrez les valeurs suivantes, puis sélectionnez **Créer**.

    | Paramètre | Détails |
    | --- | --- |
    | **Abonnement** | Sélectionnez un abonnement pour y créer la zone DNS.|
    | **Groupe de ressources** | Sélectionnez ou créez un groupe de ressources. Pour plus d’informations sur les groupes de ressources, consultez l’article [Présentation de Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
    | **Nom** | Entrez un nom pour la zone DNS. Par exemple : **contoso.com**. |
    | **Lieu** | Sélectionnez l’emplacement du groupe de ressources. L’emplacement est déjà sélectionné si vous utilisez un groupe de ressources créé précédemment.  |

> [!NOTE]
> Le groupe de ressources fait référence à l’emplacement du groupe de ressources et n’a aucun impact sur la zone DNS. L’emplacement de la zone DNS est toujours « global » et n’est pas affiché.

## <a name="list-dns-zones"></a>Création de la liste des zones DNS

Dans la zone de recherche de ressources en haut du portail Azure, recherchez **Zones DNS**. Chaque zone DNS est indépendante. Des informations, telles que le nombre de jeux d’enregistrements et les serveurs de noms, peuvent être consultées depuis cette page. La colonne **Serveurs de noms** ne figure pas dans la vue par défaut. Pour l’ajouter, sélectionnez **Gérer la vue > Modifier les colonnes > + Ajouter une colonne**, puis dans la liste déroulante, sélectionnez **Serveurs de noms**. Sélectionnez **Enregistrer** pour appliquer la nouvelle colonne.

:::image type="content" source="./media/dns-operations-dnszones-portal/list-zones.png" alt-text="Capture d’écran de la page affichant la liste des zones DNS.":::

## <a name="delete-a-dns-zone"></a>Suppression d’une zone DNS

Accédez à une zone DNS dans le portail. Dans la page Vue d’ensemble de la **zone DNS** sélectionnée, sélectionnez **Supprimer la zone**. Vous êtes ensuite invité à confirmer la suppression de la zone DNS. La suppression d’une zone DNS entraîne la suppression de tous les enregistrements présents dans la zone.

:::image type="content" source="./media/dns-operations-dnszones-portal/delete-zone.png" alt-text="Capture d’écran du bouton de suppression de la zone DNS dans la page Vue d’ensemble.":::

## <a name="next-steps"></a>Étapes suivantes

Apprenez à travailler avec votre zone DNS et vos enregistrements en vous rendant sur [Prise en main d’Azure DNS à l’aide du portail Azure](dns-getstarted-portal.md).
