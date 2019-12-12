---
title: Gérer sa facture et convertir une évaluation gratuite en offre avec paiement à l’utilisation dans l’application Azure IoT Central | Microsoft Docs
description: Découvrez comment, en tant qu’administrateur, gérer votre facture et convertir votre évaluation gratuite en offre avec paiement à l’utilisation dans votre application Azure IoT Central.
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0c1c1643fb8c8394ce3ce50e5926f05fd4d366f6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976415"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Gérer sa facture dans une application IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Cet article destiné aux administrateurs explique comment gérer sa facture dans la section Administration de l’application Azure IoT Central, et comment convertir sa version d’évaluation en offre avec paiement à l’utilisation.

Pour accéder à la section **Administration** et l’utiliser, vous devez avoir le rôle **Administrateur** dans l’application Azure IoT Central. Si vous créez une application Azure IoT Central, le rôle **Administrateur** vous est automatiquement attribué pour cette application.

## <a name="view-your-bill"></a>Consulter votre facture

Pour consulter votre facture, accédez à la page **Facturation** de la section **Administration**. La page de facturation Azure s’ouvre dans un nouvel onglet où figure la facture de chacune de vos applications Azure IoT Central.

## <a name="convert-your-trial-to-pay-as-you-go"></a>Convertir votre version d'évaluation en abonnement avec paiement à l’utilisation

- Les applications **à l’essai** sont gratuites pendant sept jours avant leur expiration. Elles peuvent être passées en paiement à l’utilisation à tout moment avant leur expiration.
- Les applications assorties d’un **paiement à l’utilisation** sont facturées par appareil, les 5 premiers appareils étant gratuits, par abonnement.

Apprenez-en davantage au sujet de la tarification sur la [page de tarification d’Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Dans la section facturation, vous pouvez convertir vos applications à l’essai en applications assorties d’un paiement à l’utilisation.

Pour mener à bien ce processus en libre-service, effectuez ces étapes :

1. Accédez à la page **Facturation** de la section **Administration**.

    ![État de la version d'évaluation](media/howto-view-bill/freetrialbilling.png)

1. Sélectionnez **Convertir en paiement à l’utilisation**.

    ![Convertir la version d'évaluation](media/howto-view-bill/convert.png)

1. Sélectionnez le service Azure Active Directory approprié, ainsi que l’abonnement Azure à utiliser pour votre application avec paiement à l'utilisation.

1. Dès que vous sélectionnez **Convertir**, votre application est une application assortie d’un paiement à l’utilisation et vous commencez à être facturé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment gérer votre facture dans l’application Azure IoT Central, nous vous conseillons de découvrir comment [Personnaliser l’interface utilisateur de l’application](howto-customize-ui.md) dans Azure IoT Central.