---
title: Créer une extension réseau HCX
description: Découvrez comment étendre un réseau de votre environnement local vers Azure VMware Solution.
ms.topic: how-to
ms.date: 09/07/2021
ms.openlocfilehash: fd58285ab7e2999c758e818020e39799a19f6c53
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838428"
---
# <a name="create-a-hcx-network-extension"></a>Créer une extension réseau HCX

Cette étape facultative permet d’étendre un réseau de votre environnement local vers Azure VMware Solution.

1. Sous **Services**, sélectionnez **Network Extension**(Extension réseau) > **Create a Network Extension** (Créer une extension réseau).

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Capture d’écran affichant les sélections qui permettent de commencer à créer une extension réseau." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Sélectionnez chaque réseau que vous souhaitez étendre vers Azure VMware Solution, puis sélectionnez **Next** (Suivant).

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Capture d’écran présentant la sélection d’un réseau.":::

1. Entrez l’adresse IP de la passerelle locale pour chacun des réseaux que vous étendez, puis sélectionnez **Soumettre**.

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Capture d’écran montrant l’entrée d’une adresse IP de passerelle.":::

   L’extension d’un réseau peut prendre quelques minutes. Quand l’opération est terminée, l’état change et passe à **Extension complete** (Extension terminée).

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Capture d’écran montrant l’état d’extension terminée.":::


