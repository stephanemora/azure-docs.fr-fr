---
title: Vue d’ensemble du déploiement d’Azure Firewall Manager Preview
description: Découvrez les étapes de déploiement de haut niveau requises pour Azure Firewall Manager Preview
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488255"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Vue d’ensemble du déploiement d’Azure Firewall Manager Preview

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager Preview peut être déployé de différentes manières, mais la procédure générale suivante est recommandée.

## <a name="prerequisites"></a>Prérequis

> [!IMPORTANT]
> Azure Firewall Manager Preview doit être explicitement activé à l’aide de la commande PowerShell `Register-AzProviderFeature`.
>À partir d’une invite de commande PowerShell, exécutez les commandes suivantes :
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>Il faut compter 30 minutes pour l’inscription de la fonctionnalité. Exécutez la commande suivante pour vérifier l’état de votre inscription :
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>Processus général de déploiement

1. Créer une architecture hub and spoke

   - Créez un hub virtuel sécurisé à l’aide d’Azure Firewall Manager et ajoutez des connexions de réseau virtuel.<br>*or*<br>
   - Créez un hub Virtual WAN et ajoutez des connexions de réseau virtuel.
2. Sélectionner les fournisseurs de sécurité

   - Effectué lors de la création du hub virtuel sécurisé.<br>*or*<br>
   - Convertissez un hub Virtual WAN existant en hub virtuel sécurisé.
3. Créer une stratégie de pare-feu et l’associer à votre hub

   - Applicable uniquement si vous utilisez le Pare-feu Azure.
   - Les stratégies SECaaS (security as a service) tierces sont configurées via l’expérience de gestion des partenaires.
4. Configurer les paramètre de routage pour acheminer le trafic vers votre hub sécurisé

   - Acheminez facilement le trafic vers votre hub sécurisé pour le filtrage et la journalisation sans itinéraires définis par l’utilisateur sur des réseaux virtuels spoke à l’aide de la page Paramètre de routage du hub virtuel sécurisé.

> [!NOTE]
> - Seul un hub par WAN virtuel par région est autorisé. Vous pouvez cependant ajouter plusieurs WAN virtuels dans la région.
> - Les espaces d’adresses IP des hubs ne doivent pas se chevaucher dans un vWAN.
> - Vos connexions de réseau virtuel de hub doivent se trouver dans la même région que le hub.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Sécuriser votre réseau cloud avec Azure Firewall Manager Preview à l’aide du portail Azure](secure-cloud-network.md)