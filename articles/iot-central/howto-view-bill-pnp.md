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
ms.openlocfilehash: d27288718b7f224c78c9cf1f15d05198f628fd15
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878949"
---
# <a name="view-your-bill-in-an-iot-central-application"></a>Afficher votre facture dans une application IoT Central

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

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

    ![État de la version d'évaluation](media/howto-view-bill-pnp/freetrialbilling.png)

1. Sélectionnez **Convertir en paiement à l’utilisation**.

    ![Convertir la version d'évaluation](media/howto-view-bill-pnp/convert.png)

1. Sélectionnez le service Azure Active Directory approprié, ainsi que l’abonnement Azure à utiliser pour votre application avec paiement à l'utilisation.

1. Dès que vous sélectionnez **Convertir**, votre application est une application assortie d’un paiement à l’utilisation et vous commencez à être facturé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment afficher votre facture dans l’application Azure IoT Central, l’étape suivante suggérée est d’en apprendre davantage sur la [personnalisation de l’interface utilisateur de l’application](howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) dans Azure IoT Central.