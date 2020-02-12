---
title: Gérer un cloud privé Azure VMware Solutions (AVS)
description: Décrit les fonctionnalités disponibles pour gérer les ressources et les activités de votre cloud privé AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47bf2251f71204b99245c1a9d55ef87157c41dd8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014825"
---
# <a name="manage-avs-private-cloud-resources-and-activities"></a>Gérer les ressources et les activités d'un cloud privé AVS

La gestion des clouds privés AVS s'effectue à partir du portail AVS. Vérifiez l’état, les ressources disponibles, les activités sur le cloud privé AVS et d’autres paramètres à partir du portail AVS.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Accéder au portail AVS

Accéder au [portail AVS](access-cloudsimple-portal.md).

## <a name="view-the-list-of-avs-private-clouds"></a>Afficher la liste des clouds privés AVS

L’onglet **Clouds privés AVS** de la page **Ressources** répertorie tous les clouds privés AVS dans votre abonnement. Les détails fournis incluent le nom, le nombre de clusters vSphere, l’emplacement, l’état actuel du cloud privé AVS et des informations sur les ressources.

![Page Clouds privés AVS](media/manage-private-cloud.png)

Sélectionnez un cloud privé AVS pour avoir accès à des informations et à des actions supplémentaires.

## <a name="avs-private-cloud-summary"></a>Résumé du cloud privé AVS

Affichez un résumé complet du cloud privé AVS sélectionné. La page de résumé inclut les serveurs DNS déployés sur le cloud privé AVS. Vous pouvez configurer la redirection DNS des serveurs DNS locaux vers vos serveurs DNS de cloud privé AVS. Pour plus d’informations sur la redirection DNS, consultez [Configurer DNS pour la résolution de noms pour le vCenter de cloud privé AVS sur site](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Résumé du cloud privé AVS](media/private-cloud-summary.png)

### <a name="available-actions"></a>Actions disponibles

* [Lancer le client vSphere](https://docs.azure.cloudsimple.com/vsphere-access/). Accédez au vCenter de ce cloud privé AVS.
* [Acheter des nœuds](create-nodes.md). Ajoutez des nœuds à ce cloud privé AVS.
* [Étendre](expand-private-cloud.md). Ajoutez des nœuds à ce cloud privé AVS.
* **Actualiser**. Mettez à jour les informations de cette page.
* **Supprimer**. Vous pouvez supprimer le cloud privé AVS à tout moment. **Avant de le supprimer, assurez-vous que vous avez sauvegardé l’ensemble des systèmes et des données.** La suppression d’un cloud privé AVS supprime toutes les machines virtuelles, la configuration de vCenter et les données. Cliquez sur **Supprimer** dans la section de résumé du cloud privé AVS sélectionné. Après la suppression, toutes les données du cloud privé AVS sont effacées via un processus d’effacement sécurisé et conforme à des normes strictes.
* [Modifier les privilèges vSphere](escalate-private-cloud-privileges.md). Élevez vos privilèges sur ce cloud privé AVS.

## <a name="avs-private-cloud-vlanssubnets"></a>Réseaux VLAN/Sous-réseaux de cloud privé AVS

Affichez la liste des réseaux VLAN/sous-réseaux définis pour le cloud privé AVS sélectionné. Cette liste inclut les réseaux VLAN/sous-réseaux de gestion créés au moment de la création du cloud privé AVS.

![Cloud privé AVS - Réseaux VLAN/Sous-réseaux](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Actions disponibles

* [Ajouter des réseaux VLAN/sous-réseaux](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Ajoutez un réseau VLAN/sous-réseau à ce cloud privé AVS.

Sélectionnez un réseau VLAN/sous-réseau pour avoir accès aux actions suivantes.
* [Attacher une table de pare-feu](https://docs.azure.cloudsimple.com/firewall/). Attachez une table de pare-feu à ce cloud privé AVS.
* **Modifier**
* **Supprimer** (uniquement les réseaux VLAN/sous-réseaux définis par l’utilisateur)

## <a name="avs-private-cloud-activity"></a>Activité du cloud privé AVS

Affichez les informations suivantes sur le cloud privé AVS sélectionné. Les informations d’activité constituent une liste filtrée de toutes les activités sur le cloud privé AVS sélectionné. Cette page peut afficher jusqu’à 25 activités récentes.

* Alertes récentes
* Événements récents
* Tâches récentes
* Audit récent

![Cloud privé AVS - Activité](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Racks de cloud

Les racks de cloud sont les principaux composants de votre cloud privé AVS. Chaque rack fournit une unité de capacité. AVS configure automatiquement les racks de cloud en fonction de vos sélections lors de la création ou de l’extension d’un cloud privé AVS. Affichez la liste complète des racks de cloud, y compris le cloud privé AVS auquel chacun est attribué.

![Cloud privé AVS - Racks de cloud](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Réseau de gestion vSphere

Liste des ressources de gestion et des machines virtuelles VMware actuellement configurées sur le cloud privé AVS. Ces informations incluent le version du logiciel, le nom de domaine complet (FQDN) et l’adresse IP des ressources.

![Cloud privé AVS - Réseau de gestion vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser des machines virtuelles VMware sur Azure](quickstart-create-vmware-virtual-machine.md)
* En savoir plus sur les [clouds privés AVS](cloudsimple-private-cloud.md)