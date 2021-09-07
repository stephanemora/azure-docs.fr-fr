---
title: Utiliser des étiquettes de service
titleSuffix: Azure SignalR Service
description: Utiliser des étiquettes de service pour autoriser le trafic sortant vers votre instance Azure SignalR Service
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: fdb87dab5f2dc105da28ca097f81ff83c0dce3b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524586"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Utiliser des étiquettes de service pour Azure SignalR Service

Vous pouvez utiliser des [étiquettes de service](../virtual-network/network-security-groups-overview.md#service-tags) pour Azure SignalR Service lors de la configuration du [groupe de sécurité réseau](../virtual-network/network-security-groups-overview.md#network-security-groups). Cela vous permet de définir une règle de sécurité réseau entrante/sortante pour les points de terminaison Azure SignalR Service, sans devoir coder en dur des adresses IP.

Azure SignalR Service gère ces étiquettes de service. Vous ne pouvez pas créer votre propre étiquette de service ou modifier une étiquette existante. Microsoft gère ces préfixes d'adresse correspondant à l'étiquette de service, et met automatiquement à jour l'étiquette de service lorsque les adresses changent.

> [!Note]
> À partir du 15 août 2021, le service Azure SignalR prend en charge les balises de service bidirectionnelles pour le trafic entrant et sortant.

## <a name="use-service-tag-on-portal"></a>Utiliser une étiquette de service sur le portail

### <a name="configure-outbound-traffic"></a>Configurer le trafic sortant

Vous pouvez autoriser le trafic sortant vers Azure SignalR Service en ajoutant une nouvelle règle de sécurité réseau sortante :

1. Accédez au groupe de sécurité réseau.

1. Cliquez sur le menu des paramètres appelé **Règles de sécurité de trafic sortant**.

1. Cliquez sur le bouton **+ Ajouter** en haut.

1. Sélectionnez **Étiquette de service** sous **Destination**.

1. Sélectionnez **AzureSignalR** sous **Étiquette de service de destination**.

1. Indiquez **443** dans **Plages de ports de destination**.

    ![Créer une règle de sécurité de trafic sortant](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Ajustez les autres champs selon vos besoins.

1. Cliquez sur **Add**.

### <a name="configure-inbound-traffic"></a>Configurer le trafic entrant

Si vous avez des flux en amont, vous pouvez également autoriser le trafic entrant à partir du service Azure SignalR en ajoutant une nouvelle règle de sécurité de réseau de trafic entrant :

1. Accédez au groupe de sécurité réseau.

1. Cliquez sur le menu des paramètres appelé **Règles de sécurité de trafic entrant**.

1. Cliquez sur le bouton **+ Ajouter** en haut.

1. Sélectionnez **Étiquette de service** sous **Source**.

1. Sélectionnez **AzureSignalR** sous **Étiquette de service source**.

1. Renseignez **\* *_ dans _* Plages de ports source**.

   :::image type="content" alt-text="Créer une règle de sécurité de trafic entrant" source="media/howto-service-tags/portal-add-inbound-security-rule.png" :::


1. Ajustez les autres champs selon vos besoins.

1. Cliquez sur **Add**.

## <a name="next-steps"></a>Étapes suivantes

- [Groupes de sécurité réseau - Étiquettes de service](../virtual-network/network-security-groups-overview.md#security-rules)