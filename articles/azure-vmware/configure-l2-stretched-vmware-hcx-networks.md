---
title: Configurer DHCP sur les réseaux VMware HCX étendus L2
description: Découvrez comment envoyer des demandes DHCP à partir de vos machines virtuelles Azure VMware Solution à un serveur DHCP non-NSX-T.
ms.topic: how-to
ms.date: 05/28/2021
ms.openlocfilehash: fb72fef74d83c2e860cb0d2ad661a91c40197f5a
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746966"
---
# <a name="configure-dhcp-on-l2-stretched-vmware-hcx-networks"></a>Configurer DHCP sur les réseaux VMware HCX étendus L2

DHCP ne fonctionne pas pour les machines virtuelles sur le réseau Stretch HCX L2 VMware lorsque le serveur DHCP se trouve dans le centre de données local.  Par défaut, le service NSX empêche toutes les requêtes DHCP de traverser le réseau Stretch L2. Pour envoyer des demandes DHCP à partir de vos machines virtuelles Azure VMware Solution à un serveur DHCP non-NSX-T, vous devez configurer DHCP sur des réseaux VMware HCX étendus L2.

1. (Facultatif) Si vous devez localiser le nom de segment de l’extension L2 :

   1. Connectez-vous au vCenter local puis, sous **Accueil**, sélectionnez **HCX**.

   1. Sélectionnez **Extension réseau** sous **Services**.

   1. Sélectionnez l’extension réseau devant prendre en charge les requêtes DHCP d’Azure VMware Solution vers l’emplacement local.

   1. Entrez ici le nom du réseau de destination.

      :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="Capture d’écran d’une extension réseau dans le client VMware vSphere" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. Dans NSX-T Manager, sélectionnez **Mise en réseau** > **Segments** > **Profils de segment**.

1. Sélectionnez **Ajouter un profil de segment**, puis **Sécurité du segment**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="Capture d’écran de la procédure d’ajout d’un profil de segment dans NSX-T" lightbox="media/manage-dhcp/add-segment-profile.png":::
1. Fournissez un nom et une balise, puis définissez le bouton bascule **Filtre BPDU** sur ACTIVÉ et tous les boutons bascule DHCP sur DÉSACTIVÉ.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="Capture d’écran montrant le filtre BPDU activé et les boutons bascule DHCP désactivés" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::
    
   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="Capture d’écran du champ Sécurité du segment" lightbox="media/manage-dhcp/edit-segment-security.png":::
