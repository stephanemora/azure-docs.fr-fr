---
title: Déplacer des ressources Azure Notification Hubs d’une région à une autre
description: Découvrez comment déplacer des ressources Azure Notification Hubs vers une autre région Azure.
author: sethmanheim
ms.author: sethm
ms.service: notification-hubs
ms.topic: how-to
ms.date: 09/07/2021
ms.custom: template-how-to
ms.openlocfilehash: 0de15ab1f16ab5bc234ad6430eae5f65e608753e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602548"
---
# <a name="move-resources-between-azure-regions"></a>Déplacer des ressources entre les régions Azure

Cet article décrit comment déplacer des ressources Azure Notification Hubs vers une autre région Azure. De manière générale, le processus est le suivant :

1. Créez un espace de noms de destination avec un nom différent.
1. Exportez les inscriptions de l’espace de noms précédent.
1. Importez les inscriptions dans le nouvel espace de noms dans la région souhaitée.

## <a name="overview"></a>Vue d’ensemble

Dans certains scénarios, vous devrez peut-être déplacer les ressources de service entre les régions Azure pour différentes raisons professionnelles. Elles peuvent être déplacées vers une région nouvellement disponible, vous pouvez vouloir déployer des fonctionnalités ou des services disponibles uniquement dans une région spécifique, vous pouvez les déplacer en raison d’une stratégie interne ou d’exigences de conformité ou pour résoudre des problèmes de capacité.

Les noms d’espaces de noms Azure Notification Hubs sont uniques, et les inscriptions sont effectuées par hub. Pour effectuer un tel déplacement, vous devez donc créer un nouveau hub dans la région souhaitée, puis déplacer les inscriptions avec toutes les autres données pertinentes vers l’espace de noms nouvellement créé.

## <a name="create-a-notification-hubs-namespace-with-a-different-name"></a>Créer un espace de noms Notification Hubs avec un nom différent

Pour créer un espace de noms Notification Hubs, procédez comme suit. Renseignez toutes les informations requises dans l’onglet **Informations de base**, y compris la région de destination souhaitée pour l’espace de noms.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

Une fois le nouvel espace de noms créé, veillez à définir les informations d’identification PNS et à créer des stratégies équivalentes dans le nouvel espace de noms.

## <a name="exportimport-registrations"></a>Exporter/importer des inscriptions

Une fois que le nouvel espace de noms a été créé dans la région vers laquelle vous souhaitez déplacer la ressource, exportez toutes les inscriptions en bloc et importez-les dans le nouvel espace de noms. Pour ce faire, consultez [Exporter et importer en bloc des inscriptions Azure Notification Hubs](export-modify-registrations-bulk.md).

## <a name="delete-the-previous-namespace-optional"></a>Supprimer l’espace de noms précédent (facultatif)

Après avoir terminé l’exportation de l’inscription de votre ancien espace de noms vers le nouvel espace de noms, vous pouvez, si vous le souhaitez, supprimer l’ancien espace de noms.

1. Accédez à l’espace de noms existant dans la région précédente.

2. Cliquez sur **Supprimer**, puis entrez à nouveau le nom de l’espace de noms dans le volet **Supprimer l’espace de noms**.

3. Cliquez sur **Supprimer** en bas du volet **Supprimer l’espace de noms**.

## <a name="next-steps"></a>Étapes suivantes

Les articles suivants présentent des exemples d’autres services qui ont mis en place un déplacement entre régions.

- [Déplacer des groupes de sécurité réseau vers une autre région](/azure/virtual-network/move-across-regions-nsg-portal)
- [Déplacer des IP publiques vers une autre région](/azure/virtual-network/move-across-regions-publicip-portal)
- [Déplacer un compte de stockage vers une autre région](/azure/storage/common/storage-account-move?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)
- [Déplacer des ressources entre régions (à partir d’un groupe de ressources)](/azure/resource-mover/move-region-within-resource-group#:~:text=1%20In%20the%20Azure%20portal%2C%20open%20the%20relevant,you%20want%20to%20move.%20...%20More%20items...%20)
