---
title: Infrastructure et connectivité à SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Configurez l’infrastructure de connectivité requise pour utiliser SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/1/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f8fc161f83bcdb070ca4be1c1da3be84f1086c93
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111412544"
---
# <a name="sap-hana-large-instances-deployment"></a>Déploiement de SAP HANA (grandes instances) 

Dans cet article, nous allons répertorier les informations dont vous avez besoin pour déployer de grandes instances SAP HANA (autrement dit, des instances d’infrastructure BareMetal). Tout d’abord, pour plus d’informations, consultez :
- [Termes communs des grandes instances HANA](hana-know-terms.md)
-  [SKU des grandes instances HANA](hana-available-skus.md)

## <a name="required-information"></a>Informations requises

Vous avez acheté SAP HANA sur de grandes instances Azure auprès de Microsoft et vous souhaitez le déployer. Microsoft aura besoin des informations suivantes :

- Nom du client.
- Coordonnées professionnelles (y compris l’adresse e-mail et le numéro de téléphone).
- Coordonnées techniques (y compris l’adresse e-mail et le numéro de téléphone).
- Coordonnées de mise en réseau technique (y compris l’adresse e-mail et le numéro de téléphone).
- Région de déploiement Azure (par exemple, USA Ouest, Australie Est ou Europe Nord).
- Référence SKU (configuration) de SAP HANA sur Azure (grandes instances).
- Pour chaque région de déploiement Azure :
    - Une plage d’adresses IP /29 pour les connexions ER-P2P qui relient les réseaux virtuels Azure aux grandes instances HANA.
    - Un bloc CIDR /24 utilisé pour le pool d’adresses IP du serveur de grandes instances HANA.
    - Facultatif lors de l’utilisation de [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md), réservez une autre plage d’adresses IP/29. La plage ajoutée permet le routage direct à partir d’unités de grande instance locales vers HANA. La plage ajoutée permet également le routage entre les unités de grande instance HANA dans différentes régions Azure. Cette plage ne peut pas chevaucher les plages d’adresses IP que vous avez définies auparavant.
- Les valeurs de plage d’adresses IP utilisées dans l’attribut Espace d’adressage du réseau virtuel de chaque réseau virtuel Azure qui se connecte à de grandes instances HANA.
- Données pour chaque système de grandes instances HANA :
  - Nom d’hôte souhaité, idéalement avec le nom de domaine complet.
  - Adresse IP souhaitée pour l’unité de grande instance HANA en dehors de la plage d’adresses du pool d’adresses IP du serveur. (Les 30 premières adresses IP dans la plage d’adresses du pool d’adresses IP du serveur sont réservées à un usage interne au sein des grandes instances HANA.)
  - Nom de SID SAP HANA pour l’instance HANA (requis pour créer les volumes de disque liés à SAP HANA nécessaires). Microsoft a besoin de l’identificateur de sécurité HANA pour créer les autorisations pour sidadm sur les volumes NFS. Ces volumes sont attachés à l’unité de grande instance HANA. L’identificateur de sécurité HANA est également utilisé comme l’un des composants de nom des volumes de disque qui sont montés. Si vous souhaitez exécuter plusieurs instances HANA sur l’unité, vous devez répertorier plusieurs identificateurs de sécurité HANA. Chacun d’eux obtient un ensemble distinct de volumes assignés.
  - Dans le système d’exploitation Linux, l’utilisateur sidadm a un ID de groupe. Cet ID est nécessaire pour créer les volumes de disque liés à SAP HANA nécessaires. L’installation de SAP HANA crée généralement le groupe sapsys avec un ID de groupe de 1 001. L’utilisateur sidadm fait partie de ce groupe.
  - Dans le système d’exploitation Linux, l’utilisateur sidadm a un ID d’utilisateur. Cet ID est nécessaire pour créer les volumes de disque liés à SAP HANA nécessaires. Si vous exécutez plusieurs instances HANA sur l’unité, répertoriez tous les utilisateurs sidadm. 
- ID de l’abonnement Azure auquel les grandes instances SAP HANA sur Azure HANA vont être directement connectées. Cet ID d’abonnement désigne l’abonnement Azure qui va être facturé avec les unités de grande instance HANA.

Une fois les informations précédentes fournies, Microsoft approvisionne SAP HANA sur Azure (grandes instances). Microsoft vous envoie des informations pour lier vos réseaux virtuels Azure aux grandes instances HANA. Vous pouvez également accéder aux unités de grande instance HANA.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants dans la séquence pour vous connecter aux grandes instances HANA après leur déploiement par Microsoft :

1. [Connexion de machines virtuelles Azure à de grandes instances HANA](hana-connect-azure-vm-large-instances.md)
2. [Connexion d’un réseau virtuel à de grandes instances HANA ExpressRoute](hana-connect-vnet-express-route.md)
3. [Conditions requises supplémentaires du réseau (facultatives)](hana-additional-network-requirements.md)
