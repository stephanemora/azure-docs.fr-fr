---
title: Se connecter à une machine virtuelle Windows avec RDP
titleSuffix: Azure Bastion
description: Découvrez comment utiliser Azure Bastion pour vous connecter à une machine virtuelle Windows avec RDP.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: 1a1423a0587a64452671e74a210baa9d2ad16ea8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700574"
---
# <a name="create-an-rdp-connection-to-a-windows-vm-using-azure-bastion"></a>Créer une connexion RDP à une machine virtuelle Windows avec Azure Bastion

Cet article vous montre comment créer de manière sécurisée et fluide une connexion RDP à des machines virtuelles Windows situées dans un réseau virtuel Azure, directement au moyen du portail Azure. Quand vous utilisez Azure Bastion, vos machines virtuelles n’ont pas besoin de client, d’agent ni de logiciel supplémentaire. Vous pouvez également vous connecter à une machine virtuelle Windows avec SSH. Pour plus d’informations, consultez [Créer une connexion SSH à une machine virtuelle Windows](bastion-connect-vm-ssh-windows.md).

Azure Bastion fournit une connectivité sécurisée à toutes les machines virtuelles du réseau virtuel dans lequel il est provisionné. Azure Bastion protège vos machines virtuelles contre l’exposition des ports RDP/SSH au monde extérieur, tout en fournissant un accès sécurisé à l’aide de RDP/SSH. Pour plus d’informations, consultez la page [Présentation d’Azure Bastion](bastion-overview.md).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous respectez les critères suivants :

* Un réseau virtuel sur lequel l’hôte Bastion est déjà installé.

  * Assurez-vous d’avoir configuré un hôte Azure Bastion pour le réseau virtuel dans lequel se trouve la machine virtuelle. Une fois que le service Bastion est provisionné et déployé dans votre réseau virtuel, vous pouvez l’utiliser pour vous connecter à toutes les machines virtuelles dans le réseau virtuel. 
  * Pour configurer un hôte Azure Bastion, consultez [Créer un hôte Bastion](tutorial-create-host-portal.md#createhost). Si vous envisagez de configurer des valeurs de port personnalisées, veillez à sélectionner la référence SKU Standard lors de la configuration de Bastion.

* Une machine virtuelle Windows hébergée sur le réseau virtuel.

### <a name="required-roles"></a>Rôles nécessaires

* Rôle de lecteur sur la machine virtuelle.
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle.
* Rôle de lecteur sur la ressource Azure Bastion.

### <a name="ports"></a>Ports

Pour vous connecter à la machine virtuelle Windows, les ports suivants doivent être ouverts sur votre machine virtuelle Windows :

*   Port d’entrée : RDP (3389) ***ou***
*   Port d’entrée : valeur personnalisée (vous devez spécifier ce port personnalisé quand vous vous connectez à la machine virtuelle avec Azure Bastion)

> [!NOTE]
> Si vous souhaitez spécifier une valeur de port personnalisée, vous devez configurer Azure Bastion avec la référence SKU Standard. La référence SKU De base ne permet pas de spécifier des ports personnalisés. La référence SKU standard est actuellement en préversion.
>

## <a name="connect"></a><a name="rdp"></a>Connexion

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
