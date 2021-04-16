---
title: Gérer des réseaux virtuels - Portail Azure - Azure Database pour PostgreSQL - Serveur flexible
description: Créer et gérer des réseaux virtuels pour le serveur flexible Azure Database pour PostgreSQL à l'aide du Portail Azure
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: d5b60cfeb3bfce1253ece3708e6b48ce6bcb0b4f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226903"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Créer et gérer des réseaux virtuels pour le serveur flexible Azure Database pour PostgreSQL à l'aide du Portail Azure

> [!IMPORTANT]
> Azure Database pour PostgreSQL - Serveur flexible est en préversion

Azure Database pour PostgreSQL - Serveur flexible prend en charge deux types de méthodes de connectivité réseau mutuellement exclusives pour se connecter à votre serveur flexible. Les deux options sont les suivantes :

* Accès public (adresses IP autorisées)
* Accès privé (intégration au réseau virtuel)

Dans cet article, nous allons nous concentrer sur la création d’un serveur PostgreSQL avec **accès privé (intégration au réseau virtuel)** avec le Portail Azure. Avec l’option Accès privé (intégration au réseau virtuel), vous pouvez déployer votre serveur flexible sur votre propre [réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md). Les réseaux virtuels Azure offrent des communications réseau privées et sécurisées. Dans un accès privé, les connexions au serveur PostgreSQL sont limitées à votre réseau virtuel. Pour en savoir plus à ce sujet, reportez-vous à [Accès privé (intégration au réseau virtuel)](./concepts-networking.md#private-access-vnet-integration).

Vous pouvez déployer votre serveur flexible dans un réseau virtuel et un sous-réseau au moment de la création du serveur. Une fois le serveur flexible déployé, vous ne pouvez pas le déplacer vers un autre réseau virtuel ou sous-réseau, ni vers l’*accès public (adresses IP autorisées)* .

## <a name="prerequisites"></a>Prérequis
Pour créer un serveur flexible dans un réseau virtuel, vous avez besoin des éléments suivants :
- Un [réseau virtuel](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > Le réseau virtuel et le sous-réseau doivent se trouver dans la même région et le même abonnement que votre serveur flexible.

-  Pour [déléguer un sous-réseau](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) à **Microsoft.DBforPostgreSQL/flexibleServers**. Cette délégation spécifie que seuls les serveurs flexibles Azure Database pour PostgreSQL peuvent utiliser ce sous-réseau. Aucun autre type de ressource Azure ne peut se trouver sur le sous-réseau délégué.
-  Ajoutez `Microsoft.Storage` au point de terminaison de service pour le sous-réseau délégué aux serveurs Flexible. Voici les étapes qui permettent d’effectuer cette opération :
     1. Accédez à la page de votre réseau virtuel.
     2. Sélectionnez le réseau virtuel dans lequel vous envisagez de déployer votre serveur flexible.
     3. Choisissez le sous-réseau qui est délégué pour le serveur flexible.
     4. Sur l’écran d’extraction, sous **Point de terminaison de service**, choisissez `Microsoft.storage` dans la liste déroulante.
     5. Enregistrez les modifications.


## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>Créer un serveur flexible Azure Database pour PostgreSQL dans un réseau virtuel déjà existant

1. Sélectionnez **Créer une ressource** (+) dans l’angle supérieur gauche du portail.
2. Sélectionnez **Bases de données** > **Azure Database pour PostgreSQL**. Vous pouvez également entrer **PostgreSQL** dans la zone de recherche pour trouver le service.
3. Sélectionnez **Serveur flexible** comme option de déploiement.
4. Remplissez le formulaire **Informations de base**.
5. Accédez à l’onglet **Mise en réseau** pour configurer la façon dont vous souhaitez vous connecter à votre serveur.
6. Dans **Méthode de connectivité**, sélectionnez **Accès privé (intégration au réseau virtuel)** . Accédez à **Réseau virtuel**, puis sélectionnez le *réseau virtuel* existant et le *sous-réseau* créé dans le cadre des conditions préalables ci-dessus.
7. Sélectionnez **Vérifier + créer** pour passer en revue la configuration de votre serveur flexible.
8. Sélectionnez **Créer** pour approvisionner le serveur. L’approvisionnement peut prendre quelques minutes.

>[!Note]
> Une fois le serveur flexible déployé sur un réseau virtuel et un sous-réseau, vous ne pouvez pas le déplacer vers un accès public (adresses IP autorisées).
## <a name="next-steps"></a>Étapes suivantes
- [Créer et gérer un réseau virtuel Azure Database pour PostgreSQL - Serveur flexible à l’aide de Azure CLI](./how-to-manage-virtual-network-cli.md).
- En savoir plus sur la [mise en réseau dans Azure Database pour PostgreSQL - Serveur flexible](./concepts-networking.md).
- Apprenez-en plus sur le [réseau virtuel de serveur flexible Azure Database pour PostgreSQL](./concepts-networking.md#private-access-vnet-integration).