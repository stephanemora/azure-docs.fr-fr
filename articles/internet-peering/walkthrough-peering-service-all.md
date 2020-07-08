---
title: Procédure pas à pas pour le partenaire Peering Service
titleSuffix: Azure
description: Procédure pas à pas pour le partenaire Peering Service
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 75ec1a4ededfea4f9b40461d69b6e16d947e6919
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84712477"
---
# <a name="peering-service-partner-walkthrough"></a>Procédure pas à pas pour le partenaire Peering Service

Cette section explique les étapes qu’un fournisseur doit suivre pour activer un Peering direct pour le Peering Service.

## <a name="create-direct-peering-connection-for-peering-service"></a>Créer une connexion de Peering direct pour Peering Service
Les fournisseurs de services peuvent étendre leur portée géographique en créant un nouveau Peering direct qui prend en charge le Peering Service. À cet effet,
1. devenez un partenaire de Peering Service si ce n’est pas déjà le cas
1. Suivez les instructions pour [Créer ou modifier un Peering direct en utilisant le portail](howto-direct-portal.md). Veillez à ce qu’il réponde aux exigences de haute disponibilité.
1. Ensuite, suivez les étapes pour [Activer Peering Service sur un Peering direct en utilisant le portail](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Utiliser une connexion héritée de Peering direct pour Peering Service
Si vous disposez d’un Peering direct hérité que vous souhaitez utiliser pour prendre en charge le Peering Service,
1. devenez un partenaire de Peering Service si ce n’est pas déjà le cas.
1. Suivez les instructions pour [Convertir un Peering direct existant en ressource Azure en utilisant le portail](howto-legacy-direct-portal.md). Si nécessaire, commandez des circuits supplémentaires pour répondre aux exigences de haute disponibilité.
1. Ensuite, suivez les étapes pour [Activer Peering Service sur un Peering direct en utilisant le portail](howto-peering-service-portal.md).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [stratégie de Peering](https://peering.azurewebsites.net/peering).
* Pour plus d’informations sur les étapes de configuration du Peering direct avec Microsoft, suivez la [procédure pas à pas de configuration du Peering direct](walkthrough-direct-all.md).
* Pour plus d’informations sur les étapes de configuration d’un Peering Exchange avec Microsoft, suivez la [procédure pas à pas de configuration d’un Peering Exchange](walkthrough-exchange-all.md).