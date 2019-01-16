---
title: Recherche interactive sur une carte avec Azure Maps | Microsoft Docs
description: 'Démarrage rapide Azure : Créer une démonstration de recherche interactive sur une carte avec Azure Maps'
author: walsehgal
ms.author: v-musehg
ms.date: 12/02/2018
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 681a76de02d91b39ee74d1e4fa764c06d79ff3ab
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065135"
---
# <a name="create-an-interactive-search-map-by-using-azure-maps"></a>Créer une carte de recherche interactive avec Azure Maps

Cet article décrit les fonctionnalités d’Azure Maps qui consistent à créer une carte offrant aux utilisateurs une expérience de recherche interactive. Il vous guide pas à pas durant ces étapes de base :
* Créer votre propre compte Azure Maps.
* Obtenir votre clé de compte à utiliser dans l’application web de démonstration.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-an-account-and-get-your-key"></a>Créer un compte et obtenir une clé

1. En haut à gauche du [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.
2. Dans la zone **Rechercher sur la Place de marché**, entrez **Maps**.
3. Dans les **Résultats**, sélectionnez **Maps**. Sélectionnez le bouton **Créer** situé sous la carte.
4. Dans la page **Créer un compte Azure Maps**, entrez les valeurs suivantes :
    - Le **Nom** de votre nouveau compte.
    - **L’Abonnement** à utiliser pour ce compte.
    - Le **Groupe de ressources** de ce compte. Vous pouvez choisir de **créer** ou d’utiliser un groupe de ressources **Existant**.
    - Sélectionnez le **Niveau tarifaire** de votre choix.
    - Prenez connaissance de la **licence** et de la **déclaration de confidentialité**. Cochez la case pour accepter les conditions.
    - Enfin, sélectionnez le bouton **Créer**.

    ![Créer un compte Azure Maps dans le portail](./media/quick-demo-map-app/create-account.png)

5. Après avoir créé votre compte, ouvrez le compte et accédez à la section Paramètres dans le menu Compte. Sélectionnez **Clés** pour afficher les clés primaire et secondaire de votre compte Azure Maps. Copiez la valeur de la **Clé primaire** dans le Presse-papiers local afin de pouvoir l’utiliser dans la section suivante.

## <a name="download-the-application"></a>Télécharger l’application

1. Téléchargez ou copiez le contenu du fichier [interactiveSearch.html](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/interactiveSearch.html).
2. Enregistrez le contenu de ce fichier localement sous le nom **AzureMapDemo.html**. Ouvrez le fichier dans un éditeur de texte.
3. Recherchez la chaîne `<insert-key>`. Remplacez-la par la valeur de **clé primaire** obtenue dans la section précédente.

## <a name="open-the-application"></a>Ouvrir l’application

1. Ouvrez le fichier **AzureMapDemo.html** dans le navigateur de votre choix.
2. Observez la carte de la ville de Los Angeles. Effectuez un zoom avant et arrière : la carte affiche automatiquement plus ou moins d’informations selon le niveau de zoom. 
3. Modifiez le centre par défaut de la carte. Dans le fichier **AzureMapDemo.html**, recherchez la variable nommée **center**. Remplacez la paire de valeurs longitude, latitude de cette variable par les nouvelles valeurs : **[-74.0060, 40.7128]**. Enregistrez le fichier et actualisez votre navigateur.
4. Testez l’expérience de recherche interactive. Dans la zone de recherche en haut à gauche de l’application web de démonstration, recherchez **restaurants**.
5. Déplacez votre souris sur la liste des adresses et emplacements qui s’affichent sous la zone de recherche. Le repère correspondant sur la carte affiche des informations à propos de cet emplacement. Pour des raisons de confidentialité des entreprises privées, des adresses et des noms fictifs sont présentés.

    ![Application web de recherche interactive](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Les tutoriels expliquent en détail comment utiliser et configurer Azure Maps avec votre compte. Si vous souhaitez suivre les tutoriels, ne nettoyez pas les ressources créées dans ce guide de démarrage rapide. Sinon, effectuez ces étapes pour nettoyer les ressources :

1. Fermez le navigateur qui exécute l’application web **AzureMapDemo.html**.
2. Dans le menu de gauche du portail Azure, sélectionnez **Toutes les ressources**. Sélectionnez ensuite votre compte Azure Maps. En haut du panneau **Toutes les ressources**, sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé votre compte Azure Maps ainsi qu’une application de démonstration. Pour découvrir comment créer votre propre application à l’aide des API Azure Maps, passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Rechercher des points d’intérêt de proximité à l’aide d’Azure Maps](./tutorial-search-location.md)

Pour obtenir d’autres exemples de code et bénéficier d’une expérience de codage interactive, consultez ces guides :

> [!div class="nextstepaction"]
> [Rechercher une adresse à l’aide du service de recherche Azure Maps](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Utiliser la bibliothèque Map Control d’Azure Maps](./how-to-use-map-control.md)
