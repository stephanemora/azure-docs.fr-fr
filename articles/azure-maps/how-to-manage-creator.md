---
title: Gérer Microsoft Azure Maps Creator (préversion)
description: Cet article explique comment gérer Microsoft Azure Maps Creator (préversion).
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 24a462fdb2c68bcccd539c9d18252822e2730bd6
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108326882"
---
# <a name="manage-azure-maps-creator-preview"></a>Gérer Azure Maps Creator (préversion) 

> [!IMPORTANT]
> Les services Azure Maps Creator sont disponibles en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le Créateur Azure Maps vous permet de créer des données cartographiques d’intérieur privées. L’API Azure Maps et le module Cartes d’intérieur vous permettent de développer des applications web de carte d’intérieur interactives et dynamiques. Actuellement, le Créateur n’est disponible qu’aux États-Unis au niveau tarifaire Gen 2 ou Gen 1 (S1).

Cet article vous guide tout au long des étapes de création et de suppression d’une ressource de Créateur dans un compte Azure Maps.

## <a name="create-creator-preview-resource"></a>Créer une ressource Creator (préversion)

1. Connectez-vous au [portail Azure](https://portal.azure.com)

2. Sélectionnez votre compte Azure Maps. Si vous ne voyez pas votre compte Azure Maps sous **Ressources récentes**, accédez au menu du portail Azure. Sélectionnez **Toutes les ressources**. Recherchez et sélectionnez votre compte Azure Maps.

    ![Page d’accueil du portail Azure Maps](./media/how-to-manage-creator/select-maps-account.png)

3. Une fois que vous êtes sur la page du compte Azure Maps, accédez à l’option **Vue d’ensemble** sous **Créateur**. Cliquez sur **Créer** pour créer une ressource Azure Maps Creator.

    ![Page Créer un Créateur Azure Maps](./media/how-to-manage-creator/creator-blade-settings.png)

4. Entrez le nom et l’emplacement de votre ressource de Créateur. Actuellement, le Créateur n’est pris en charge qu’aux États-Unis. Sélectionnez **Revoir + créer**.

   ![Page Entrer les informations du compte du Créateur](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Passez en revue vos paramètres, puis sélectionnez **Créer**.

    ![Page Confirmer les paramètres du compte du créateur](./media/how-to-manage-creator/creator-create-dialog.png)

6. Une fois le déploiement terminé, une page contenant un message de réussite ou d’échec s’affiche.

   ![Page État du déploiement de la ressource](./media/how-to-manage-creator/creator-resource-created.png)

7. Sélectionnez **Accéder à la ressource**. La page d’affichage de la ressource de Créateur montre l’état de votre ressource de Créateur et la région démographique choisie.

    ![Page État du créateur](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >À partir de la page Ressource Creator, vous pouvez revenir au compte Azure Maps auquel elle appartient en cliquant sur Compte Azure Maps.

## <a name="delete-creator-preview-resource"></a>Supprimer une ressource Creator (préversion)

Pour supprimer la ressource de Créateur, accédez à votre compte Azure Maps. Sous **Créateur**, cliquez sur **Vue d’ensemble**. Sélectionnez le bouton **Supprimer**.

>[!WARNING]
>Lorsque vous supprimez la ressource de Créateur de votre compte Azure Maps, vous supprimez également les jeux de données, tilesets et statesets de caractéristiques créés à l’aide des services du Créateur.

![Page Créateur avec le bouton Supprimer](./media/how-to-manage-creator/creator-delete.png)

Cliquez sur le bouton **Supprimer**, puis tapez le nom Creator pour confirmer la suppression. Une fois la ressource supprimée, une page de confirmation s’affiche, comme dans l’image ci-dessous :

![Page Créateur avec confirmation de la suppression](./media/how-to-manage-creator/creator-confirm-delete.png)

## <a name="authentication"></a>Authentification

Creator (préversion) hérite des paramètres de Contrôle d’accès (IAM) d’Azure Maps. Tous les appels d’API pour l’accès aux données doivent être envoyés avec des règles d’authentification et d’autorisation.

Les données d’utilisation du Créateur sont incorporées dans vos graphiques d’utilisation et journal d’activité Azure Maps.  Pour plus d’informations, consultez [Gérer l’authentification dans Azure Maps](./how-to-manage-authentication.md).

## <a name="access-to-creator-services"></a>Accès aux services du Créateur

Les services Creator (préversion) et les services qui utilisent des données hébergées dans Creator (par exemple, le service Render) sont accessibles à une URL géographique. L’URL géographique est déterminée par l’emplacement sélectionné lors de la création. Par exemple, si Creator est créé aux États-Unis, tous les appels au service Conversion doivent être envoyés à `us.atlas.microsoft.com/conversion/convert`.

De même, toutes les données importées dans le Créateur doivent être chargées dans le même emplacement géographique que la ressource de Créateur. Par exemple, si le Créateur est approvisionné au Royaume-Uni, toutes les données brutes doivent être chargées via `us.atlas.microsoft.com/mapData/upload`.

## <a name="next-steps"></a>Étapes suivantes

Présentation du service Creator (préversion) pour les cartes d’intérieur :

> [!div class="nextstepaction"]
> [Chargement de données](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversion de données](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Dataset](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Jeu d’état de caractéristique](creator-indoor-maps.md#feature-statesets)

Découvrez comment utiliser le service Creator (préversion) pour afficher des cartes d’intérieur dans votre application :

> [!div class="nextstepaction"]
> [Tutoriel sur le Créateur Azure Maps](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Application de style dynamique de carte d’intérieur](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Utiliser le module Cartes d’intérieur](how-to-use-indoor-module.md)