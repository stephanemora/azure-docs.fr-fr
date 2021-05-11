---
title: Gérer des jeux d’enregistrements DNS et des enregistrements avec Azure DNS
description: Azure DNS permet de gérer les jeux d’enregistrements DNS et les enregistrements lors de l’hébergement de votre domaine.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 04/28/2021
ms.author: rohink
ms.openlocfilehash: 8ac76671dc16fb51cea35154cd7862ad1dee66f6
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108226976"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Gestion d’enregistrements et de jeux d’enregistrements DNS à l’aide du portail Azure

Cet article explique comment gérer les jeux d’enregistrements et les enregistrements pour votre zone DNS À l’aide du portail Azure.

Il est important de comprendre la différence entre les jeux d’enregistrements DNS et les enregistrements DNS individuels. Un jeu d’enregistrements est une collection d’enregistrements dans une zone ayant le même nom et le même type. Pour plus d’informations, consultez [Création de jeux d’enregistrements et d’enregistrements DNS à l’aide du portail Azure](./dns-getstarted-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Création d’un jeu d’enregistrements et d’un enregistrement

Pour créer un jeu d’enregistrements dans le portail Azure, consultez [Création d’enregistrements DNS à l’aide du portail Azure](./dns-getstarted-portal.md).

## <a name="view-a-record-set"></a>Affichage d’un jeu d’enregistrements

1. Sur le portail Azure, accédez à la page de présentation de la **zone DNS**.

1. Recherchez le jeu d'enregistrements et sélectionnez-le pour ouvrir ses propriétés.

    :::image type="content" source="./media/dns-operations-recordsets-portal/overview.png" alt-text="Capture d'écran de la page de présentation de la zone contosotest.com.":::

## <a name="add-a-new-record-to-a-record-set"></a>Ajouter un nouvel enregistrement à un jeu d’enregistrements

Vous pouvez ajouter jusqu'à 20 enregistrements à n'importe quel jeu d'enregistrements. Un jeu d'enregistrements ne peut pas contenir deux enregistrements identiques. Des jeux d'enregistrements vides (sans aucun enregistrement) peuvent être créés, mais ils n'apparaîtront pas sur les serveurs de noms Azure DNS. Les jeux d’enregistrements du type CNAME peuvent contenir un enregistrement au maximum.

1. Sur la page **Propriétés du jeu d'enregistrements** de votre zone DNS, sélectionnez le jeu d'enregistrements auquel vous souhaitez ajouter un enregistrement.

    :::image type="content" source="./media/dns-operations-recordsets-portal/select-record.png" alt-text="Capture d'écran illustrant la sélection du jeu d'enregistrements www.":::

1. Spécifiez les propriétés du jeu d’enregistrements en remplissant les champs.

    :::image type="content" source="./media/dns-operations-recordsets-portal/record-page.png" alt-text="Capture d'écran de la page Ajouter un enregistrement.":::

1. Sélectionnez **Enregistrer** en haut de la page pour enregistrer vos paramètres. Puis fermez la page.

Une fois l'enregistrement sauvegardé, les valeurs reflètent le nouvel enregistrement sur la page **Zone DNS**.

## <a name="update-a-record"></a>Mise à jour d’un enregistrement

Lors de la mise à jour d’un enregistrement dans un jeu d’enregistrements existant, les champs pouvant être mis à jour varient selon le type d’enregistrement que vous utilisez.

1. Sur la page **Propriétés du jeu d'enregistrements** de votre jeu d'enregistrements, recherchez l'enregistrement.

1. Modifiez l’enregistrement. Lorsque vous modifiez un enregistrement, vous pouvez modifier les paramètres disponibles pour l’enregistrement. Dans l'exemple suivant, le champ **Adresse IP** est sélectionné, et l'adresse IP est modifiée.

    :::image type="content" source="./media/dns-operations-recordsets-portal/update-record-page.png" alt-text="Capture d'écran de la page Mettre à jour un enregistrement.":::

1. Sélectionnez **Enregistrer** en haut de la page pour enregistrer vos paramètres. Une notification s’affiche dans le coin supérieur droit indiquant que l’enregistrement a été sauvegardé.

    :::image type="content" source="./media/dns-operations-recordsets-portal/record-saved.png" alt-text="Capture d'écran d'un enregistrement sauvegardé avec succès.":::

Une fois l'enregistrement sauvegardé, les valeurs du jeu d'enregistrements reflètent la mise à jour sur la page **Zone DNS**.

## <a name="remove-a-record-from-a-record-set"></a>Suppression d’un enregistrement d’un jeu d’enregistrements

Vous pouvez utiliser le portail Azure pour supprimer des enregistrements d’un jeu d’enregistrements. La suppression du dernier enregistrement d'un jeu d'enregistrements ne supprime pas le jeu d'enregistrements.

1. Sur la page **Propriétés du jeu d'enregistrements** de votre jeu d'enregistrements, recherchez l'enregistrement.

1. Sélectionnez **...** en regard de l'enregistrement, puis **Supprimer** pour supprimer l'enregistrement du jeu d'enregistrements.

    :::image type="content" source="./media/dns-operations-recordsets-portal/delete-record.png" alt-text="Capture d'écran illustrant la suppression d'un enregistrement.":::

1. Sélectionnez **Enregistrer** en haut de la page pour enregistrer vos paramètres.

1. Une fois l'enregistrement supprimé, les valeurs du jeu d'enregistrements reflètent la suppression sur la page **Zone DNS**.

## <a name="delete-a-record-set"></a><a name="delete"></a>Supprimer un jeu d’enregistrements

1. Sur la page **Propriétés du jeu d'enregistrements** de votre jeu d'enregistrements, sélectionnez **Supprimer**.

    :::image type="content" source="./media/dns-operations-recordsets-portal/delete-record-set.png" alt-text="Capture d'écran illustrant la suppression d'un jeu d'enregistrements.":::

1. Un message s’affiche vous demandant si vous souhaitez supprimer le jeu d’enregistrements.

1. Vérifiez que le nom correspond au jeu d'enregistrements que vous souhaitez supprimer, puis sélectionnez **Oui**.

1. Sur la page **Zone DNS**, vérifiez que le jeu d'enregistrements n'est plus visible.

## <a name="work-with-ns-and-soa-records"></a>Utilisation d’enregistrements NS et SOA

Les enregistrements NS et SOA qui sont créés automatiquement sont gérés différemment des autres types d’enregistrements.

### <a name="modify-soa-records"></a>Modification d'enregistrements SOA

Vous ne pouvez pas ajouter ou supprimer d'enregistrements dans le jeu d'enregistrements SOA créé automatiquement à l'extrémité de la zone (nom = « \@ »). Vous pouvez cependant modifier n'importe lequel des paramètres de l'enregistrement SOA, à l'exception des paramètres « Host » et « TTL » du jeu d'enregistrements.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modification d’enregistrements NS à l’extrémité de la zone

Le jeu d’enregistrements NS à l’apex de la zone est créé automatiquement avec chaque zone DNS. Il contient les noms des serveurs de noms Azure DNS attribués à la zone.

Vous pouvez ajouter d'autres serveurs de noms à ce jeu d'enregistrements NS pour prendre en charge le co-hébergement de domaines avec plusieurs fournisseurs DNS. Vous pouvez également modifier la durée de vie et les métadonnées pour ce jeu d’enregistrements. Toutefois, vous n'avez pas la possibilité de supprimer ni de modifier les serveurs de noms Azure DNS prérenseignés.

Cette restriction s'applique uniquement au jeu d'enregistrements NS défini à l'extrémité de la zone. Les autres jeux d’enregistrements NS dans votre zone (tels que ceux utilisés pour déléguer des zones enfants) peuvent être modifiés sans contrainte.

### <a name="delete-soa-or-ns-record-sets"></a>Suppression de jeux d’enregistrements SOA ou NS

Vous ne pouvez pas supprimer les jeux d'enregistrements SOA et NS situés à l'extrémité de la zone (nom = « \@ ») qui sont créés automatiquement au moment de la création de la zone. Ceux-ci sont automatiquement supprimés lorsque vous supprimez la zone.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Azure DNS, consultez la [Vue d’ensemble d’Azure DNS](dns-overview.md).
* Pour plus d’informations sur l’automatisation de DNS, consultez la rubrique [Création des zones DNS et de jeux d’enregistrements à l’aide du Kit de développement logiciel (SDK) .NET](dns-sdk.md).
* Pour plus d’informations sur les enregistrements DNS inversés, consultez l’article [Vue d’ensemble des DNS inversés et assistance dans Azure](dns-reverse-dns-overview.md).
* Pour plus d’informations sur les enregistrements d’alias Azure DNS, consultez la [Vue d’ensemble des enregistrements d’alias Azure DNS](dns-alias.md).