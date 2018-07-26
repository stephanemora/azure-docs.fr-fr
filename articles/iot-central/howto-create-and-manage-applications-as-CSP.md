---
title: Créer et gérer des applications Azure IoT Central en tant que fournisseur de solutions cloud | Microsoft Docs
description: En tant que fournisseur de solutions cloud, comment créer une application Azure IoT Central pour le compte de votre client.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/09/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: d32e05e99543b30ee92ea455ae2f800b09d83661
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39012107"
---
# <a name="as-a-csp-create-and-manage-an-azure-iot-central-application-on-behalf-of-your-customer"></a>En tant que fournisseur de solutions cloud, créer et gérer une application Azure IoT Central pour le compte de votre client 

Le programme Fournisseur de solutions cloud Microsoft est un programme Revendeur Microsoft. Son objectif est de fournir à nos partenaires un programme clé en main pour revendre tous les services en ligne commerciaux de Microsoft. Découvrez plus d’informations sur le [programme Fournisseur de solutions cloud](https://partner.microsoft.com/cloud-solution-provider).

En tant que fournisseur de solutions cloud, vous pouvez créer et gérer des applications Microsoft Azure IoT Central pour le compte de vos clients via le [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). Quand des applications Azure IoT Central sont créées pour le compte de clients par des fournisseurs de solutions cloud, tout comme avec d’autres services gérés par ceux-ci, les fournisseurs de solutions cloud gèrent la facturation des clients. Un coût pour Azure IoT Central apparaît sur le total de votre facture dans le Microsoft Partner Center.

Pour commencer, connectez-vous à votre compte sur le portail des partenaires Microsoft et sélectionnez un client pour lequel vous voulez créer une application Azure IoT Central. Accédez à Gestion des services pour le client à partir de la barre de navigation gauche.

![Microsoft Partner Center, vue Client](media\howto-create-application-asCSP\image1.png)

Azure IoT Central apparaît en tant que service pouvant être administré. Cliquez sur le lien Azure IoT Central sur la page pour créer de nouvelles applications ou pour gérer des applications existantes pour ce client.

![Azure IoT Central disponible pour la gestion](media\howto-create-application-asCSP\image2.png)

Vous êtes dirigé vers la page Gestionnaire d’applications d’Azure IoT Central. Azure IoT Central conserve le contexte indiquant que vous venez du Microsoft Partner Center et que vous voulez gérer ce client particulier. Vous pouvez le constater d’après l’en-tête de la page Gestionnaire d’applications. À partir de là, vous pouvez soit accéder à une application existante que vous avez créée précédemment pour ce client pour la gérer ou bien lui en créer une nouvelle.

![Gestionnaire de création pour les fournisseurs de services cloud](media\howto-create-application-asCSP\image3.png)

Pour créer une application Azure IoT Central, cliquez sur la vignette **Nouvelle application**. Ceci charge la page de création d’application. Vous devez renseigner tous les champs de cette page, puis choisir **Créer**. Vous pouvez trouver plus d’informations sur chacun des champs ci-dessous.

![Page Créer une application pour les fournisseurs de solutions cloud](media\howto-create-application-asCSP\image4-1.png)

![Page Créer une application pour les fournisseurs de solutions cloud](media\howto-create-application-asCSP\image4-2.png)

## <a name="payment-plan"></a>Plan de paiement

En tant que fournisseur de solutions cloud, vous pouvez créer seulement des applications payantes. Pour faire une démonstration d’Azure IoT Central à votre client, vous pouvez créer une application d’essai à part. Découvrez plus d’informations sur les applications d’essai et payantes sur la [page des tarifs d’Azure IoT Central ](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nom de l’application

Le nom de votre application est affiché sur la page **Gestionnaire d’application** et dans chaque application Azure IoT Central. Vous pouvez choisir n’importe quel nom pour votre application Azure IoT Central. Choisissez un nom évocateur pour vous et les autres personnes de votre organisation.

## <a name="application-url"></a>URL de l’application

L’URL de l’application est le lien vers votre application. Vous pouvez enregistrer un signet vers cette URL dans votre navigateur ou le partager avec d’autres utilisateurs.

Quand vous entrez le nom de votre application, l’URL de votre application est générée automatiquement. Si vous préférez, vous pouvez choisir une autre URL pour votre application. Chaque URL d’Azure IoT Central doit y être unique. Vous voyez un message d’erreur si l’URL que vous choisissez a déjà été utilisée.

## <a name="directory"></a>Répertoire

Dans la mesure où Azure IoT Central dispose du contexte indiquant que vous êtes là pour gérer le client sélectionné dans le portail des partenaires Microsoft, vous voyez seulement le locataire Azure Active Directory pour ce client dans le champ Annuaire. 

Un locataire Azure Active Directory contient les identités des utilisateurs, les informations d’identification et d’autres informations de l’organisation. Vous pouvez associer plusieurs abonnements Azure à un même locataire Azure Active Directory.

Pour plus d’informations, consultez [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Abonnement Azure

Un abonnement Azure vous permet de créer des instances de services Azure. Azure IoT Central recherche automatiquement tous les abonnements Azure du client auquel vous avez accès et les affiche dans une liste déroulante dans la page **Créer une application**. Choisissez un abonnement Azure pour créer une application Azure IoT Central.

Si vous n’avez pas d’abonnement Azure, vous pouvez en créer un dans le Microsoft Partner Center. Après avoir créé l’abonnement Azure, revenez à la page **Créer une application**. Votre nouvel abonnement apparaît dans la liste déroulante **Abonnement Azure**.

Pour plus d’informations, consultez [Abonnements Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Région

Choisissez la région où vous voulez créer votre application Azure IoT Central. D’une façon générale, il est recommandé de choisir la région qui est physiquement la plus proche de vos appareils, de façon à obtenir des performances optimales.

Pour plus d’informations, consultez [Régions Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions).

Vous pouvez voir les régions dans lesquelles Azure IoT Central est disponible dans la page [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).

> [!Note]
> Une fois que vous avez choisi une région, vous ne pouvez plus déplacer votre application dans une autre région.

## <a name="application-template"></a>Modèle d’application

Vous pouvez choisir un des modèles d’application disponibles pour votre nouvelle application Azure IoT Central. Un modèle d’application peut contenir des éléments prédéfinis, comme des modèles d’appareil et des tableaux de bord pour vous aider à démarrer.

| Modèle d’application | Description |
| -------------------- | ----------- |
| Application personnalisée   | Crée une application vide, que vous pouvez remplir avec vos propres modèles d’appareil et vos propres appareils. |
| Exemple Contoso       | Crée une application qui inclut un modèle d’appareil pour un appareil connecté simple. Utilisez ce modèle pour commencer à explorer Azure IoT Central. |
| Exemples de Devkits       | Crée une application avec des modèles d’appareil tout prêts qui vous permettent de connecter un appareil MXChip ou Raspberry Pi. Utilisez ce modèle si vous êtes développeur d’appareils testant du code sur un de ces appareils. |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment créer une application Azure IoT Central en tant que fournisseur de solutions cloud, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)