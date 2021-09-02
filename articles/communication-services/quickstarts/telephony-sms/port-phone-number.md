---
title: 'Démarrage rapide : Porter un numéro de téléphone dans Azure Communication Services'
description: Découvrez comment porter un numéro de téléphone dans votre ressource Communication Services
author: probableprime
manager: mikben
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: 4bc923d2d6c9c0fc88f40ed6dd3d6290723a7a1e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969522"
---
# <a name="quickstart-port-a-phone-number"></a>Démarrage rapide : Porter un numéro de téléphone

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Commencez à utiliser Azure Communication Services en portant un numéro de téléphone dans votre ressource Azure Communication Services. Les numéros gratuits et géographiques basés aux États-Unis sont éligibles au portage. Pour plus d’informations sur les types de numéro de téléphone, consultez la [documentation conceptuelle sur les numéros de téléphone](../../concepts/telephony-sms/plan-solution.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Une ressource Communication Services active.](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Rassembler les détails de vos ressources Azure

Avant de lancer une demande de portage, accédez au portail Azure et sélectionnez votre ressource Communication Services. Une fois le volet `Overview` sélectionné, cliquez sur le lien `JSON View` dans le coin supérieur droit :

:::image type="content" source="./media/number-port/json-view.png" alt-text="Capture d’écran montrant la sélection de la vue JSON.":::

Enregistrez l’**ID Azure** et l’**ID de ressource immuable** de votre ressource :

:::image type="content" source="./media/number-port/json-properties.png" alt-text="Capture d’écran montrant la sélection des propriétés JSON.":::

## <a name="initiate-the-port-request"></a>Lancer la demande de portage

Les numéros gratuits et géographiques basés aux États-Unis sont éligibles au portage. Utilisez l’un des formulaires suivants pour envoyer votre demande de portage :

- Pour les numéros gratuits : [Demande de portage pour numéro gratuit](https://aka.ms/acs-port-form-tollfree)
- Pour les numéros géographiques basés aux États-Unis : [Demande de portage pour numéro géographique](https://aka.ms/acs-port-form-geographic)

Lorsque vous avez terminé, envoyez votre formulaire de demande de portage complété à acsporting@microsoft.com. Vérifiez que la ligne d’objet de votre e-mail commence par « ACS Port-in Request » (Demande de portage ACS).

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert comment :

> [!div class="checklist"]
> * Acquérir vos métadonnées de ressources Communication Services
> * Envoyer une demande pour porter votre numéro de téléphone

> [!div class="nextstepaction"]
> [Envoyer un SMS](../telephony-sms/send.md)
> [Bien démarrer avec les appels](../voice-video-calling/getting-started-with-calling.md)
