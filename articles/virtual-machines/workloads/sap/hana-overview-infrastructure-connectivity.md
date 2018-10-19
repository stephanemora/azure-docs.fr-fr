---
title: Infrastructure et connectivité à SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Configurez l’infrastructure de connectivité requise pour utiliser SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c61dffc6fd9d0c65f5e925daebdf2a02cfb5d6ba
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161354"
---
# <a name="sap-hana-large-instances-deployment"></a>Déploiement de SAP HANA (grandes instances) 

Une fois l’achat de SAP HANA sur Azure (grandes instances) finalisé entre vous et l’équipe Microsoft responsable des comptes d’entreprise, Microsoft a besoin des informations suivantes pour déployer des unités de grande instance HANA :

- Nom du client
- Coordonnées professionnelles (y compris l’adresse électronique et le numéro de téléphone)
- Coordonnées techniques (y compris l’adresse électronique et le numéro de téléphone)
- Coordonnées de mise en réseau technique (y compris l’adresse électronique et le numéro de téléphone)
- Région de déploiement Azure (USA Ouest, USA Est, Australie Est, Australie Sud-Est, Europe Ouest et Europe Nord à partir de juillet 2017)
- Confirmer la référence SKU (configuration) de SAP HANA sur Azure (grandes instances)
- Déjà décrit dans le document de présentation et d’architecture des grandes instances HANA, pour chaque région Azure déployée sur :
    - Une plage d’adresses IP /29 pour les connexions ER-P2P qui relient les réseaux virtuels Azure aux grandes instances HANA
    - Un bloc CIDR /24 utilisé pour le pool d’adresses IP du serveur de grandes instances HANA
- Les valeurs de plage d’adresses IP utilisées dans l’attribut Espace d'adressage du réseau virtuel de chaque réseau virtuel Azure qui se connecte à de grandes instances HANA
- Données pour chaque système de grande instance HANA :
  - Nom d’hôte souhaité : idéalement, avec le nom de domaine complet.
  - Adresse IP souhaitée pour la grande unité d’instance HANA en dehors de la plage d’adresses IP du pool de serveur. N’oubliez pas que les 30 premières adresses IP de la plage d’adresses IP du pool de serveur sont réservées à une utilisation interne dans les grandes instances HANA
  - Nom de SID SAP HANA pour l’instance HANA (requis pour créer les volumes de disque liés à SAP HANA nécessaires). Le SID HANA est requis afin de créer des autorisations pour sidadm sur les volumes NFS, qui sont attachés à l’unité de grande instance HANA. Il est également utilisé comme l’un des composants de nom des volumes de disque qui sont montés. Si vous souhaitez exécuter plusieurs instances HANA sur l’unité, vous devez répertorier plusieurs SID HANA. Chacun d’eux obtient un ensemble distinct de volumes assignés.
  - L’ID de groupe détenu par l’utilisateur sidadm dans le système d’exploitation Linux est nécessaire pour créer des volumes de disque liés à SAP HANA. L’installation de SAP HANA crée généralement le groupe sapsys avec l’ID de groupe 1001. L’utilisateur sidadm fait partie de ce groupe
  - L’ID de l’utilisateur sidadm dans le système d’exploitation Linux est nécessaire pour créer des volumes de disque liés à SAP HANA. Si vous exécutez plusieurs instances HANA sur l’unité, vous devez répertorier tous les <sid>utilisateurs adm 
- ID de l’abonnement Azure auquel les grandes instances HANA Azure vont être directement connectées. Cet ID d’abonnement désigne l’abonnement Azure qui va être facturé avec les unités de grande instance HANA.

Après avoir renseigné ces informations, Microsoft configure SAP HANA sur Azure (grandes instances) et renvoie les informations nécessaires pour lier vos réseaux virtuels Azure aux grandes instances HANA et pour accéder aux unités de grande instance HANA.

Avant de lire cet article, familiarisez-vous avec les [termes courants concernant les grandes Instances HANA](hana-know-terms.md) et les [références SKU des grandes instances HANA](hana-available-skus.md).

Vous pouvez utiliser la séquence suivante pour vous connecter à de grandes Instances HANA après leur déploiement par Microsoft :

1. [Connexion de machines virtuelles Azure à de grandes instances HANA](hana-connect-azure-vm-large-instances.md)
2. [Connexion d’un réseau virtuel à de grandes instances HANA ExpressRoute](hana-connect-vnet-express-route.md)
3. [Conditions requises supplémentaires du réseau (facultatives)](hana-additional-network-requirements.md)

**Étapes suivantes**

- Voir [Connexion de machines virtuelles Azure à de grandes instances HANA](hana-connect-azure-vm-large-instances.md).
- Voir [Connexion d’un réseau virtuel à une grande instance HANA ExpressRoute](hana-connect-vnet-express-route.md).