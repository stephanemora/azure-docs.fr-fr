---
title: Utiliser des domaines prédéfinis dans les applications LUIS dans Azure | Microsoft Docs
description: Découvrez comment utiliser des domaines prédéfinis dans les applications Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: diberry
ms.openlocfilehash: df57f9adf5bf7f5f652a77ddeafe950463c6a9fc
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224175"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>Utiliser des domaines prédéfinis dans les applications LUIS  

Language Understanding (LUIS) fournit des *domaines prédéfinis*, à savoir des ensembles prédéfinis d’[intentions](luis-how-to-add-intents.md) et d’[entités](luis-concept-entity-types.md) qui fonctionnent conjointement pour des domaines ou des catégories communes d’applications clientes. Les domaines prédéfinis ont fait l’objet d’un apprentissage préalable, et vous pouvez les ajouter tels quels à votre application LUIS. Les intentions et les entités d’un domaine prédéfini sont entièrement personnalisables après leur ajout à application. Vous pouvez réaliser un apprentissage avec des énoncés de votre système pour qu’ils fonctionnent pour vos utilisateurs. Vous pouvez utiliser un domaine prédéfini entier comme point de départ pour la personnalisation, ou simplement emprunter quelques intentions ou entités d’un domaine prédéfini. 

Parcourez l’onglet **Domaines prédéfinis** pour voir les autres domaines prédéfinis que vous pouvez utiliser dans votre application. Cliquez sur la vignette d’un domaine pour l’ajouter à votre application, ou cliquez sur **En savoir plus** dans sa vignette pour en savoir plus sur ses intentions et entités.

> [!TIP]
> Vous trouverez une liste complète des domaines prédéfinis dans la [référence des domaines prédéfinis](./luis-reference-prebuilt-domains.md).

![Ajouter un domaine prédéfini](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>Ajouter un domaine prédéfini
Dans l’onglet **Domaines prédéfinis**, localisez le domaine RestaurantReservation et cliquez sur **Ajouter un domaine**. Une fois que le domaine prédéfini est ajouté à votre application LUIS, ouvrez **Tentatives**, puis cliquez sur l’intentions RestaurantReservation.Reserve. Vous voyez alors que de nombreux exemples d’énoncés ont déjà été fournis et étiquetés avec les entités.

![Intention RestaurantReservation.Reserve](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>Conception d’applications LUIS à partir de domaines prédéfinis
Lorsque vous utilisez des domaines prédéfinis dans votre application LUIS, vous pouvez personnaliser un domaine prédéfini entier, ou commencer avec quelques-unes de ses entités et intentions.

## <a name="customizing-an-entire-prebuilt-domain"></a>Personnalisation d’un domaine prédéfini entier
Les domaines prédéfinis sont conçus pour être généraux. Ils contiennent de nombreuses intentions et entités, que vous pouvez choisir pour personnaliser une application selon vos besoins. Si vous commencez par personnaliser un domaine prédéfini entier, supprimez les intentions et les entités dont votre application n’a pas besoin. Vous pouvez également ajouter des intentions ou des entités à l’ensemble que le domaine prédéfini fournit déjà. Par exemple, si vous utilisez le domaine prédéfini **Événements** pour une application d’événements sportifs, vous pouvez pour ajouter des entités pour les équipes sportives. Lorsque vous commencez à [fournir des énoncés](luis-how-to-add-example-utterances.md) à LUIS, incluez les conditions propres à votre application. LUIS apprend à les reconnaître et adapte les intentions et les entités du domaine prédéfini aux besoins de votre application. 

> [!TIP]
> Les intentions et les entités d’un domaine prédéfini fonctionnent le mieux ensemble. Il est préférable de combiner des intentions et des entités du même domaine lorsque cela est possible.
> * Une bonne pratique consiste à utiliser des intentions connexes du même domaine. Par exemple, si vous personnalisez l’intention `MakeReservation` dans le domaine **Lieux**, sélectionnez l’intention `Cancel` dans le domaine **Lieux** domaine au lieu de l’intention Annuler dans les domaines **Événements** ou **Utilitaires**.

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Modification du comportement d’une intention de domaine prédéfini
Vous constaterez peut-être qu’un domaine prédéfini contient une intention semblable à une intention que vous souhaitez avoir dans votre application LUIS, mais avec un comportement différent. Par exemple, le domaine prédéfini **Lieux** fournit une intention `MakeReservation` pour effectuer une réservation au restaurant, mais vous voulez que votre application utilise cette intention pour faire des réservations d’hôtel. Dans ce cas, vous pouvez modifier le comportement de cette intention en fournissant des énoncés à LUIS sur les réservations d’hôtel et en les étiquetant à l’aide de l’intention `MakeReservation`. LUIS peut alors être formé à reconnaître l’intention `MakeReservation` dans une requête de réservation d’hôtel.

> [!TIP]
> Recherchez dans le domaine Utilitaires des intentions prédéfinies que vous pouvez personnaliser pour une utilisation dans n’importe quel domaine. Par exemple, vous pouvez ajouter `Utilities.Repeat` à votre application et lui apprendre à reconnaître les actions que les utilisateurs souhaitent répéter dans votre application.


## <a name="next-step"></a>Étape suivante

Personnaliser un domaine prédéfini en y ajoutant plusieurs exemples d’énoncés.

> [!div class="nextstepaction"]
> [Ajouter des exemples d’énoncés](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>Ressources supplémentaires

Consultez la [référence des domaines prédéfinis](./luis-reference-prebuilt-domains.md) pour plus d’informations sur les domaines prédéfinis.
