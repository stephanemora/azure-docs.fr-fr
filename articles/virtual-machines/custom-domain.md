---
title: Créer et utiliser un domaine personnalisé
description: Connectez un domaine personnalisé à une machine virtuelle dans Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: c4797420904b6dc03550f658c2aa950a4de99c9c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107250910"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>Ajouter un domaine personnalisé à une machine virtuelle ou à une ressource Azure

Dans Azure, il existe plusieurs façons de connecter un domaine personnalisé à votre machine virtuelle ou ressource. Pour toute ressource avec une adresse IP publique (machine virtuelle, équilibreur de charge, passerelle applicative), la méthode la plus simple consiste à créer un jeu d’enregistrements A dans auprès de votre registrar de domaines correspondant. 

## <a name="prerequisites"></a>Prérequis 
- Vous avez besoin d’une machine virtuelle avec un serveur web en cours d’exécution. Vous pouvez utiliser le [Démarrage rapide](./linux/quick-create-cli.md) pour créer une machine virtuelle et ajouter NGINX.

- La machine virtuelle doit être accessible sur le web (Ouvrez le port 80 ou 443). Pour un déploiement plus sécurisé, vous devez d’abord placer votre machine virtuelle derrière un équilibreur de charge ou une passerelle applicative. Pour plus d’informations, consultez [Démarrage rapide : Équilibreur de charge](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal?tabs=option-1-create-load-balancer-standard).

- Vous devez avoir un domaine existant et l’accès aux paramètres DNS. Pour plus d’informations, consultez [Acheter un domaine personnalisé pour Azure App Service](../app-service/manage-custom-dns-buy-domain.md).


## <a name="add-custom-domain-to-vm-public-ip-address"></a>Ajouter un domaine personnalisé à l’adresse IP publique de la machine virtuelle

Lorsque vous créez une machine virtuelle dans le portail Azure, une ressource d’adresse IP publique est créée automatiquement pour la machine virtuelle. Votre adresse IP publique est affichée dans la page Vue d’ensemble de la machine virtuelle. 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="Affiche l’adresse IP publique dans la section Informations de base de la page Vue d’ensemble de la machine virtuelle.":::

Si vous sélectionnez l’adresse IP, vous pouvez obtenir plus d’informations sur celle-ci. Vérifiez que votre **Attribution d’IP** est définie sur **Statique**. Une adresse IP statique n’est pas modifiée en cas de redémarrage ou d’arrêt de la machine virtuelle ou de la ressource.

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="Affiche la configuration d’adresse IP publique pour vous permettre de voir si l’adresse IP est statique.":::

Si votre adresse IP n’est pas statique, vous devez créer un nom de domaine complet. 

1. Sélectionnez votre machine virtuelle dans le portail. 
1. Dans le menu de gauche, sélectionnez **Propriétés**.
1. Sous **Adresse IP publique\Étiquette du nom DNS**, sélectionnez votre adresse IP.
2. Sous **Étiquette du nom DNS**, entrez le préfixe à utiliser.
3. En haut de la page, sélectionnez **Enregistrer**.
4. Sélectionnez **Vue d’ensemble** dans le menu de gauche pour revenir à la vue d’ensemble de la machine virtuelle.
5. Vérifiez que le *nom DNS* s’affiche correctement. 

Ouvrez un navigateur et entrez votre adresse IP ou nom de domaine complet, et vérifiez qu’il affiche le contenu web en cours d’exécution sur votre machine virtuelle.
 
Après avoir vérifié votre IP statique ou nom de domaine complet, accédez à votre fournisseur de domaine, puis à Paramètres DNS.

Ajoutez alors un *Enregistrement A* pointant vers votre adresse IP publique ou un nom de domaine complet. Par exemple, la procédure pour le bureau d’enregistrement de domaines GoDaddy est la suivante :
1. Connectez-vous et sélectionnez le domaine personnalisé que vous souhaitez utiliser.
2. Dans la section **Domaines**, sélectionnez **Gérer tous**, puis sélectionnez **DNS | Gérer les zones**.
3. Pour le **Nom de domaine**, entrez votre domaine personnalisé, puis sélectionnez **Rechercher**.
4. À partir de la page Gestion DNS, sélectionnez Ajouter, puis sélectionnez A dans la liste Type.
5. Renseignez les champs de l’entrée A :
    - Type : laissez **A** sélectionné.
    - Hôte : entrez **@**
    - Pointe vers : entrez l’adresse IP publique ou le nom de domaine complet de votre machine virtuelle. 
    - TTL : laissez la valeur Une heure sélectionnée.
6. Sélectionnez **Enregistrer**.

L’entrée de l’enregistrement A est ajoutée à la table des enregistrements DNS.
 
Une fois l’enregistrement créé, il faut généralement environ une heure pour la propagation DNS, mais cela peut parfois prendre jusqu’à 48 heures. 


 
## <a name="next-steps"></a>Étapes suivantes
[Présentation de la terminaison TLS et du chiffrement TLS de bout en bout avec Application Gateway](../application-gateway/ssl-overview.md).

 
