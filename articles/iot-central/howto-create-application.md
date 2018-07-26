---
title: Créer une application Azure IoT Central | Microsoft Docs
description: En tant qu’administrateur, comment créer une application Azure IoT Central.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/09/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 1fbe3ea142e1dd738cd341f57d2b8f48b539ac75
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003764"
---
# <a name="create-your-azure-iot-central-application"></a>Créer votre application Azure IoT Central

Vous créez votre application Microsoft Azure IoT Central à partir de la page [Créer une application](https://apps.microsoftiotcentral.com/create). Pour créer une application Azure IoT Central, vous devez renseigner tous les champs de cette page, puis choisir **Créer**. Vous pouvez trouver plus d’informations sur chacun des champs ci-dessous.

![Page Créer une application](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>Plan de paiement

Vous pouvez créer une application d’essai ou payante. Découvrez plus d’informations sur les applications d’essai et payantes sur la [page de tarification d’Azure IoT Central ](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nom de l’application

Le nom de votre application est affiché sur la page **Gestionnaire d’application** et dans chaque application Azure IoT Central. Vous pouvez choisir n’importe quel nom pour votre application Azure IoT Central. Choisissez un nom évocateur pour vous et les autres personnes de votre organisation.

## <a name="application-url"></a>URL de l’application

L’URL de l’application est le lien vers votre application. Vous pouvez enregistrer un signet vers cette URL dans votre navigateur ou le partager avec d’autres utilisateurs.

Quand vous entrez le nom de votre application, l’URL de votre application est générée automatiquement. Si vous préférez, vous pouvez choisir une autre URL pour votre application. Chaque URL d’Azure IoT Central doit y être unique. Vous voyez un message d’erreur si l’URL que vous choisissez a déjà été utilisée.

## <a name="directory"></a>Répertoire

Seulement dans les applications payantes.

Choisissez un locataire Azure Active Directory pour créer une application Azure IoT Central. Un locataire Azure Active Directory contient les identités des utilisateurs, les informations d’identification et d’autres informations de l’organisation. Vous pouvez associer plusieurs abonnements Azure à un même locataire Azure Active Directory.

Si vous n’avez pas de locataire Azure Active Directory, un locataire est créé pour vous quand vous créez un abonnement Azure.

Pour plus d’informations, consultez [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Abonnement Azure

Un abonnement Azure vous permet de créer des instances de services Azure. Azure IoT Central recherche automatiquement tous les abonnements Azure auxquels vous avez accès et les affiche dans une liste déroulante dans la page **Créer une application**. Choisissez un abonnement Azure pour créer une application Azure IoT Central.

Si vous n’avez pas d’abonnement Azure, vous pouvez en créer un dans la [page d’inscription à Azure](https://aka.ms/createazuresubscription). Après avoir créé l’abonnement Azure, revenez à la page **Créer une application**. Votre nouvel abonnement apparaît dans la liste déroulante **Abonnement Azure**.

Pour plus d’informations, consultez [Abonnements Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Région

Seulement dans les applications payantes.

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

Maintenant que vous avez appris à créer une application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)