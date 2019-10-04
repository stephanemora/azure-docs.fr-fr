---
title: Affichez votre facture et convertissez votre essai gratuit en offre assortie d’un paiement à l’utilisation dans l’application Azure IoT Central | Microsoft Docs
description: En tant qu’administrateur, apprenez à afficher votre facture et à convertir votre essai gratuit en offre assortie d’un paiement à l’utilisation dans l’application Azure IoT Central.
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 16a58bfc3fa245ed1ede19b0439419ab4590234e
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68729203"
---
# <a name="view-your-bill-in-iot-central-application"></a>Afficher votre facture dans l’application IoT Central

Cet article explique comment, en tant qu’administrateur, vous pouvez afficher votre facture dans la section Administration de l’application Azure IoT Central, ainsi que la façon dont vous pouvez convertir votre version d’évaluation en version assortie d’un paiement à l’utilisation.

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

    ![État de la version d'évaluation](media/howto-administer/freetrialbilling.png)

1. Sélectionnez **Convertir en paiement à l’utilisation**.

    ![Convertir la version d'évaluation](media/howto-administer/convert.png)

1. Sélectionnez le service Azure Active Directory approprié, ainsi que l’abonnement Azure à utiliser pour votre application avec paiement à l'utilisation.

1. Dès que vous sélectionnez **Convertir**, votre application est une application assortie d’un paiement à l’utilisation et vous commencez à être facturé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment afficher votre facture dans l’application Azure IoT Central, l’étape suivante suggérée est d’en apprendre davantage sur la [personnalisation de l’interface utilisateur de l’application](howto-customize-ui.md) dans Azure IoT Central.