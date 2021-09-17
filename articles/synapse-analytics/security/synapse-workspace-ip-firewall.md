---
title: Configurer des règles de pare-feu IP
description: Article qui explique comment configurer des règles de pare-feu IP dans Azure Synapse Analytics.
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/15/2021
ms.author: seshin
ms.reviewer: wiassaf
ms.openlocfilehash: a375c4c99e3b86706f9fed7bc52f3064995cd21c
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252773"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>Règles de pare-feu IP Azure Synapse Analytics

Cet article explique ce que sont les règles de pare-feu IP et montre comment les configurer dans Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>Règles de pare-feu IP

Les règles de pare-feu IP octroient ou refusent l’accès à votre espace de travail Synapse en fonction de l’adresse IP d’origine de chaque requête. Vous pouvez configurer des règles de pare-feu IP pour votre espace de travail. Les règles de pare-feu IP configurées au niveau de l’espace de travail s’appliquent à tous les points de terminaison publics de l’espace de travail (pools SQL dédiés, pool SQL serverless et développement).

## <a name="create-and-manage-ip-firewall-rules"></a>Créer et gérer des règles de pare-feu IP

Il existe deux façons d’ajouter des règles de pare-feu IP à un espace de travail Azure Synapse. Pour ajouter un pare-feu IP à votre espace de travail, sélectionnez **Mise en réseau** et cochez **Autoriser les connexions de toutes les adresses IP** lors de la création de l’espace de travail.

> [!Important]
> Cette fonctionnalité n’est disponible que pour les espaces de travail Azure Synapse non associés à un réseau virtuel géré.

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-connections-all-ip-addresses.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-connections-all-ip-addresses.png" alt-text="Capture d’écran mettant en évidence l’onglet Sécurité et la case à cocher Autoriser les connexions de toutes les adresses IP.":::


Vous pouvez également ajouter des règles de pare-feu IP à un espace de travail Synapse après la création de l’espace de travail. Sélectionnez **Pare-feu** sous **Sécurité** dans le portail Azure. Pour ajouter une nouvelle règle de pare-feu IP, attribuez-lui un nom, une adresse IP de début et une adresse IP de fin. Sélectionnez **Enregistrer** lorsque vous avez terminé.

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-firewalls-add-client-ip.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-firewalls-add-client-ip.png" alt-text="Capture d’écran de la page Réseau d’un espace de travail Synapse, mettant en évidence le bouton Ajouter l’IP du client et les champs de règles.":::

## <a name="connect-to-synapse-from-your-own-network"></a>Se connecter à Synapse à partir de votre propre réseau

Vous pouvez vous connecter à votre espace de travail Synapse à l’aide de Synapse Studio. Vous pouvez également utiliser SQL Server Management Studio (SSMS) pour vous connecter aux ressources SQL (pools SQL dédiés et pool SQL serverless) dans votre espace de travail.

Vérifiez que le pare-feu sur votre réseau et ordinateur local autorise les communications sortantes sur les ports TCP 80, 443 et 1443 pour Synapse Studio.

Vous devez aussi autoriser les communications sortantes sur le port UDP 53 pour Synapse Studio. Pour vous connecter à l’aide d’outils tels que SSMS et Power BI, vous devez autoriser la communication sortante sur le port TCP 1433.


## <a name="next-steps"></a>Étapes suivantes

Créer un [espace de travail Azure Synapse](../quickstart-create-workspace.md)

Créer un espace de travail Azure Synapse avec un [réseau virtuel d’espace de travail managé](./synapse-workspace-managed-vnet.md)