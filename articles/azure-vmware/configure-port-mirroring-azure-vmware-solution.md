---
title: Configurer la mise en miroir des ports pour Azure VMware Solution
description: Découvrez comment configurer la mise en miroir des ports pour analyser le trafic réseau qui implique le transfert d’une copie de chaque paquet d’un port de commutateur réseau à un autre.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 07/16/2021
ms.openlocfilehash: 3f1b205cc75911132abeed83d1e52e3566919551
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563833"
---
# <a name="configure-port-mirroring-in-the-azure-portal"></a>Configurer la mise en miroir des ports dans le portail Azure

Après avoir déployé Azure VMware Solution, vous pouvez configurer la mise en miroir des ports à partir le Portail Azure. La mise en miroir des ports place un analyseur de protocoles sur le port qui reçoit les données en miroir. Il analyse le trafic provenant d’une source, d’une machine virtuelle ou d’un groupe de machines virtuelles, puis envoyé à une destination définie. 

Dans ce guide pratique, vous pouvez configurer la mise en miroir des ports pour analyser le trafic réseau qui implique le transfert d’une copie de chaque paquet d’un port de commutateur réseau à un autre. 

## <a name="prerequisites"></a>Prérequis

Un cloud privé Azure VMware Solution avec accès aux interfaces de gestion vCenter et NSX-T Manager. Pour plus d’informations, consultez le tutoriel [Configurer la mise en réseau](tutorial-configure-networking.md).

## <a name="create-the-vms-or-vm-groups"></a>Créer des machines virtuelles ou des groupes de machines virtuelles

Vous allez créer les machines virtuelles source et de destination ou les groupes de machines virtuelles. Le groupe source comporte une ou plusieurs machines virtuelles sur lesquelles le trafic est mis en miroir.

1. Dans votre cloud privé Azure VMware Solution, sous **Réseau des charges de travail**, sélectionnez **Mise en miroir de ports** > **Groupes de machines virtuelles** > **Ajouter**.

   :::image type="content" source="media/networking/add-port-mirroring-vm-groups.png" alt-text="Capture d’écran montrant comment créer un groupe de machines virtuelles pour la mise en miroir des ports":::

1. Fournissez un nom pour le nouveau groupe de machines virtuelles, sélectionnez les machines virtuelles dans la liste, puis sélectionnez **OK**.

1. Répétez ces étapes pour créer le groupe de machines virtuelles de destination.

   >[!NOTE]
   >Avant de créer un profil de mise en miroir de ports, assurez-vous que vous avez créé les groupes de machines virtuelles sources et de destination.

## <a name="create-a-port-mirroring-profile"></a>Créer un profil de mise en miroir des ports

Vous allez créer un profil de mise en miroir des ports qui définit la direction du trafic pour les groupes de machines virtuelles sources et de destination.

1. Sélectionnez **Mise en miroir de ports** > **Mise en miroir de ports** > **Ajouter**, puis indiquez les informations suivantes :

   :::image type="content" source="media/networking/add-port-mirroring-profile.png" alt-text="Capture d’écran montrant les informations nécessaires pour le profil de la mise en miroir des ports":::

   - **Nom de la mise en miroir des ports** : nom descriptif du profil.

   - **Direction** : sélectionnez Entrée, Sortie ou Bidirectionnel.

   - **Source** : sélectionnez le groupe de machines virtuelles source.

   - **Destination** : sélectionnez le groupe de machines virtuelles de destination.

   - **Description** : entrez la description de la mise en miroir des ports.

1. Sélectionnez **OK** pour terminer le profil. 

   Le profil et les groupes de machines virtuelles sont visibles dans la console Azure VMware Solution.
