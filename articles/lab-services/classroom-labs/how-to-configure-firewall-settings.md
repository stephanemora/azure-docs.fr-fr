---
title: Paramètres de pare-feu pour Azure Lab Services
description: Découvrez comment déterminer l’adresse IP publique et la plage de numéros de port pour les machines virtuelles d’un labo de façon à pouvoir ajouter ces informations aux règles de pare-feu.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443451"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Paramètres de pare-feu pour Azure Lab Services

Chaque organisation ou établissement d’enseignement configure son propre réseau en fonction de ses besoins.  Parfois, cela peut consister à définir des règles de pare-feu qui bloquent les connexions RDP (Remote Desktop Protocol) ou SSH (Secure Shell) vers les ordinateurs situés en dehors du réseau.  Sachant qu’Azure Lab Services s’exécute dans le cloud public, il peut être nécessaire de procéder à une configuration supplémentaire pour permettre aux étudiants d’accéder à leur machine virtuelle en se connectant à partir du réseau du campus.

Chaque labo utilise une adresse IP publique unique et plusieurs ports.  Toutes les machines virtuelles, que ce soit le modèle de machine virtuelle ou les machines virtuelles des étudiants, sont appelées à utiliser cette adresse IP publique.  L’adresse IP publique ne changera pas pendant la durée du labo.  En revanche, chaque machine virtuelle aura un numéro de port différent.  Les numéros de port sont compris entre 49152 et 65535.  L’adresse IP publique et le numéro de port sont utilisés conjointement pour connecter le formateur et les étudiants à la machine virtuelle appropriée.  Cet article explique notamment comment trouver l’adresse IP publique utilisée par un labo.  Ces informations s’avèrent utiles pour mettre à jour les règles de pare-feu entrantes et sortantes et ainsi permettre aux étudiants d’accéder à leur machine virtuelle.

>[!IMPORTANT]
>Chaque labo aura une adresse IP publique différente.

## <a name="find-public-ip-for-a-lab"></a>Trouver l’adresse IP publique d’un labo

L’adresse IP publique de chaque labo figure dans la page **Tous les labs** du compte Lab Services.  Pour savoir comment trouver la page **Tous les labs**, suivez les instructions dans [Gérer les labos dans un compte Lab](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account).  

> [!div class="mx-imgBorder"]
> ![Page Tous les labs](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Vous ne verrez pas l’adresse IP publique si le modèle d’ordinateur de votre labo n’a pas encore été publié.

## <a name="conclusion"></a>Conclusion

Nous connaissons maintenant l’adresse IP publique du labo.  Des règles trafic entrant et sortant peuvent être créées pour le pare-feu de l’organisation, l’adresse IP publique et la plage de ports 49152-65535.  Une fois les règles mises à jour, les étudiants peuvent accéder à leur machine virtuelle sans que le pare-feu réseau bloque l’accès.

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- [Autoriser un créateur de labo à choisir l’emplacement du labo](allow-lab-creator-pick-lab-location.md)
- [Connecter un réseau lab avec un réseau virtuel pair](how-to-connect-peer-virtual-network.md)
- [Attacher une galerie d’images partagées à un lab](how-to-attach-detach-shared-image-gallery.md)
- [Ajouter un utilisateur comme propriétaire de labo](how-to-add-user-lab-owner.md)
- [Afficher les paramètres de pare-feu pour un labo](how-to-configure-firewall-settings.md)
- [Configurer les autres paramètres d’un lab](how-to-configure-lab-accounts.md)
