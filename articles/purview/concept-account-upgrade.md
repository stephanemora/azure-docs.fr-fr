---
title: Informations de mise à niveau du compte Azure Purview
description: Les comptes Azure Purview créés avant le 18 août 2021 seront automatiquement mis à niveau vers la dernière version de Purview. Cet article décrit ce qui va changer et les étapes suivantes nécessaires.
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: conceptual
ms.date: 08/27/2021
ms.custom: template-concept
ms.openlocfilehash: b528e4a4b96e0dbe5f8b2eca748420a23f3c2950
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208892"
---
# <a name="azure-purview-account-upgrade-information"></a>Informations de mise à niveau du compte Azure Purview

Azure Purview a publié de nouvelles fonctionnalités le 18 août 2021, y compris le mappage de données élastique, les collections mises à jour et le contrôle d’accès mis à jour. Les comptes Azure Purview créés **après** le 18 août 2021 sont créés avec toutes les nouvelles fonctionnalités déjà disponibles.

Les comptes créés **avant** le 18 août 2021 (comptes hérités) sont automatiquement mis à niveau pour inclure ces fonctionnalités.

Si vous avez un compte Purview créé **avant** le 18 août 2021, vous recevrez une notification par e-mail lorsque votre compte Purview aura été mis à niveau.

Ce document décrit les modifications que vous verrez lorsque votre compte est mis à niveau, ainsi que les étapes à suivre pour utiliser les nouvelles fonctionnalités.

## <a name="elastic-data-map"></a>Mappage de données élastique

Les comptes Azure Purview mis à niveau utilisent un nouveau système de mappage de données qui est mis à l’échelle dynamiquement en fonction de l’utilisation : le mappage de données élastique. Les mappages de données élastiques commencent à une unité de capacité et ajoutent et réduisent les unités de capacité en fonction des besoins.
Cette nouvelle fonctionnalité n’affecte pas votre manière d’interagir avec Purview directement, mais elle affecte la facturation. Elle s’adapte à la taille dont vous avez besoin pour gérer votre paysage de données et vous êtes facturé uniquement pour ce que vous utilisez.

Pour plus d’informations sur le nouveau mappage de données élastiques et sur la façon dont il est facturé, consultez notre [page Mappage de données élastique](concept-elastic-data-map.md).

Pour plus d’informations sur la facturation de tout Purview, consultez la [calculatrice de prix](https://azure.microsoft.com/pricing/details/azure-purview/).

Lorsque votre compte est mis à niveau, vous n’avez pas besoin d’apporter des modifications pour utiliser le mappage de données élastique. Il est automatiquement activé.

## <a name="collections"></a>Collections

Les collections existent dans les comptes Purview hérités, mais ont de nouvelles fonctionnalités et sont managées de manière différente dans les comptes Purview mis à niveau.

Les [collections héritées](how-to-create-and-manage-collections.md#legacy-collection-guide) étaient un moyen d’organiser les sources de données et les artefacts dans votre compte Purview. Les collections sont toujours utilisées pour personnaliser votre mappage de données Purview en fonction de votre environnement professionnel, mais elles incluent désormais également le contrôle d’accès. Plutôt que de contrôler l’accès à un niveau élevé en dehors de votre mappage de données, vous pouvez, par le biais des collections, faire correspondre votre expérience de gestion des accès à votre mappage de données.

Les [collections](how-to-create-and-manage-collections.md) vous permettent de contrôler précisément vos sources de données, mais également par rapport à la détectabilité. Les utilisateurs ne voient que les ressources des collections auxquelles ils ont accès et les informations dont ils ont uniquement besoin s’affichent.

Lorsque votre compte Purview est mis à niveau, vos collections sont également mises à jour. Toutes vos ressources actuelles seront migrées vers ces nouvelles collections. Dans les sections ci-dessous, nous aborderons l’emplacement où vous pouvez trouver vos collections et vos ressources existantes.

### <a name="locate-and-manage-collections"></a>Localiser et gérer des collections

Pour rechercher vos nouvelles collections, vous allons partir de [Purview Studio](https://web.purview.azure.com/resource/). Vous pouvez rechercher le studio en accédant à votre ressource Purview dans le [Portail Azure](https://portal.azure.com), puis sélectionnez la mosaïque **Ouvrir Purview Studio** dans la page de présentation.

Sélectionnez Data Map > Collections dans le volet de gauche pour ouvrir la page de gestion des collections.

:::image type="content" source="./media/concept-account-upgrade/find-collections.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur le mappage de données, avec l’onglet Collections sélectionné." border="true":::

Vous verrez la collection racine, ainsi que toutes vos collections existantes. La collection racine est la première collection de votre liste de collections et elle portera le même nom que votre ressource Purview. Dans l’exemple ci-dessous, elle s’appelle Contoso Purview.

:::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur Data Map, avec la collection racine encadrée." border="true":::

Toutes vos collections existantes ont été ajoutées à cette collection racine et peuvent être managées à partir de cette page.
Pour créer une nouvelle collection, sélectionnez **+ Ajouter une collection**.

:::image type="content" source="./media/concept-account-upgrade/select-add-a-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, montrant la fenêtre de la nouvelle collection, avec les boutons ajouter une collection mis en évidence." border="true":::

Pour modifier une collection, sélectionnez **Modifier** dans la page de détails de la collection ou dans le menu déroulant de la collection.

:::image type="content" source="./media/concept-account-upgrade/edit-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur la fenêtre de la collection, avec le bouton modifier mis en évidence dans la fenêtre de la collection sélectionnée et sous le bouton points de suspension en regard du nom de la collection." border="true":::

Pour modifier les attributions de rôles dans une collection, sélectionnez l’onglet **Attributions de rôles** pour afficher tous les rôles d’une collection. Seul un administrateur de collections peut gérer les attributions de rôles. Pour plus d’informations sur les autorisations dans les comptes mis à niveau, reportez-vous à la [section ci-dessous](#permissions).

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="Capture d’écran de la fenêtre de collection Purview Studio, avec l’onglet Attributions de rôles encadré." border="true":::

Pour plus d’informations sur les collections dans les comptes mis à niveau, consultez notre [guide sur la création et la gestion de collections](how-to-create-and-manage-collections.md).

### <a name="what-happens-to-your-collections-during-upgrade"></a>Qu’advient-il de vos collections pendant la mise à niveau ?

1. Une collection racine est créée. La collection racine est la première collection de votre liste de collections et elle portera le même nom que votre ressource Purview. Dans l’exemple ci-dessous, elle s’appelle Contoso Purview.

    :::image type="content" source="./media/concept-account-upgrade/select-root-collection.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur Data Map, avec la collection racine encadrée." border="true":::

1. Vos collections précédemment existantes seront connectées à la collection racine. Vous les verrez sous la collection racine et pouvez interagir avec eux ici.

### <a name="what-happens-to-your-sources-during-upgrade"></a>Qu’advient-il de vos sources pendant la mise à niveau ?

1. Toutes les sources qui n’ont pas encore été associées à une collection sont automatiquement ajoutées à la collection racine.

1. Toutes les sources ou analyses précédemment associées à une collection sont ajoutées à ces collections respectives dans votre compte mis à niveau.

Les ressources sont également associées à des collections dans le compte mis à niveau, mais elles ne s’affichent pas immédiatement sous votre collection mise à niveau. Cela se produit pour deux raisons :

* La ressource est analysée par une analyse planifiée et la prochaine exécution de l’analyse n’a pas encore été effectuée.
* La ressource n’a été analysée qu’à l’aide d’une analyse ponctuelle.

> [!NOTE]
> Les ressources sont ajoutées à une collection pendant le processus d’analyse. Par conséquent, une autre analyse doit être exécutée sur vos ressources pour les ajouter à ces nouvelles collections.

Pour vos analyses planifiées, vous devez uniquement attendre la prochaine exécution de ces analyses et les ressources seront ajoutées à votre collection.

Pour les analyses ponctuelles, vous devez les réexécuter manuellement pour remplir les ressources de votre collection.

1. Dans [Purview Studio](https://web.purview.azure.com/resource/), ouvrez Data Map, sélectionnez **Sources**. Sélectionnez la source que vous souhaitez analyser.

    :::image type="content" source="./media/concept-account-upgrade/select-sources.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur Data Map, avec les sources mises en évidence." border="true":::

1. Sélectionnez l’onglet **Analyses**, puis sélectionnez l’analyse que vous souhaitez réexécuter.

    :::image type="content" source="./media/concept-account-upgrade/select-scan.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur une source, avec les analyses mises en évidence." border="true":::

1. Sélectionnez **Exécuter l’analyse maintenant** pour réexécuter l’analyse.

    :::image type="content" source="./media/concept-account-upgrade/run-scan-now.png" alt-text="Capture d’écran de la fenêtre Purview Studio, ouverte sur une analyse, avec Exécuter l’analyse maintenant mise en évidence." border="true":::

### <a name="what-happens-when-your-upgraded-account-doesnt-have-a-collection-admin"></a>Que se passe-t-il lorsque votre compte mis à niveau n’a pas d’administrateur de collections ?

Votre compte Purview mis à niveau aura un ou plusieurs administrateurs de collections par défaut si le processus peut identifier au moins un utilisateur ou un groupe dans l’ordre suivant : 

1. Propriétaire (affecté explicitement)

1. Administrateur de l’accès utilisateur (attribué de manière explicite)

1. Administrateur de source de données et organisateur de données

Si votre compte n’a pas d’utilisateur ou de groupe correspondant aux critères ci-dessus, le compte Purview mis à niveau ne disposera d’aucun administrateur de collections. 

Vous pouvez toujours ajouter manuellement un administrateur de collections à l’aide de l’API de gestion. L’utilisateur qui appelle cette API doit disposer de l’autorisation propriétaire ou administrateur d’accès utilisateur sur le compte Purview pour exécuter une action d’écriture. Vous devez connaître le `objectId` du nouvel administrateur de collections à soumettre via l’API.

#### <a name="request"></a>Requête

   ```
    POST https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Purview/accounts/<accountName>/addRootCollectionAdmin?api-version=2021-07-01
   ```    
    
#### <a name="request-body"></a>Corps de la demande

   ```json
    {
        "objectId": "<objectId>"
    }
   ```    

`objectId` est l’objectId du nouvel administrateur de collections à ajouter.

#### <a name="response-body"></a>Response body

En cas de réussite, vous obtiendrez une réponse de corps vide avec le code `200`.

En cas d’échec, le format du corps de la réponse est le suivant.

   ```json
    {
        "error": {
            "code": "19000",
            "message": "The caller does not have Microsoft.Authorization/roleAssignments/write permission on resource: [/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>].",
            "target": null,
            "details": []
        }
    }
   ```

## <a name="permissions"></a>Autorisations

Dans les comptes Purview mis à niveau, les autorisations sont managées par le biais de collections.

> [!NOTE]
> Les autorisations dans les comptes mis à niveau ne sont plus managées via Access Control (IAM). Lorsqu’un compte hérité est mis à niveau, les autorisations IAM préexistantes s’affichent toujours sous Access Control IAM, mais n’affectent plus l’accès dans Purview.

Lorsqu’un compte hérité est mis à niveau, toutes les autorisations affectées dans Access Control IAM sont affectées à la collection racine à l’un des rôles suivants.

* **Administrateurs de collection** : peuvent modifier une collection et ses détails, gérer l’accès dans la collection et ajouter des sous-collections.
* **Administrateurs de source de données** : peuvent gérer les sources de données et les analyses de données.
* **Éditeurs de données** : peuvent créer, lire, modifier et supprimer des actions sur des objets de données de catalogue.
* **Lecteurs de données** : peuvent accéder aux objets de données de catalogue, mais pas les modifier.

Pour accorder à vos utilisateurs l’accès aux sources et aux artefacts dans Purview, vous pouvez leur accorder l’accès aux collections et aux sous-groupes lorsqu’ils peuvent avoir besoin d’accéder aux données.

> [!NOTE]
> Seuls les administrateurs de collections sont autorisés à manager l’accès aux collections.

Pour afficher ou modifier les attributions de rôles dans une collection, sélectionnez l’onglet **Attributions de rôles** pour afficher tous les rôles de votre collection.

:::image type="content" source="./media/concept-account-upgrade/select-role-assignments.png" alt-text="Capture d’écran de la fenêtre de collection Purview Studio, avec l’onglet Attributions de rôles encadré." border="true":::

Pour plus d’informations sur la gestion des autorisations dans un compte Purview mis à niveau, suivez notre [guide des autorisations Purview](catalog-permissions.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les concepts ci-dessus ou pour plus d’informations sur la sécurisation de votre ressource Purview, suivez les liens ci-dessous.

* [Points de terminaison privés avec Azure Purview](catalog-private-link.md)
* [Mappage de données élastique](concept-elastic-data-map.md)
* [Guides des autorisations Purview](catalog-permissions.md)
