---
title: Gérer des règles de pare-feu - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Créer et gérer des règles de pare-feu pour Azure Database pour PostgreSQL - Hyperscale (Citus) à l’aide du portail Azure
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b7de5755a9a1e49b994e7efa7fc4bca58cc6cfd9
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584040"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Gérer des règles de pare-feu dans Azure Database pour PostgreSQL - Hyperscale (Citus)
Les règles de pare-feu au niveau du serveur peuvent être utilisées pour gérer l’accès à un nœud coordinateur Hyperscale (Citus) à partir d’une adresse IP ou d’une plage d’adresses IP spécifique.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un groupe de serveurs (consultez [Créer une base de données Azure pour PostgreSQL – groupe de serveurs Hyperscale (Citus)](quickstart-create-hyperscale-portal.md)).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Créer une règle de pare-feu au niveau du serveur dans le portail Azure

> [!NOTE]
> Ces paramètres sont également accessibles lors de la création d’une base de données Azure pour PostgreSQL - groupe de serveurs Hyperscale (Citus). Sous l’onglet **Réseaux**, cliquez sur **Point de terminaison public**.
> ![Portail Azure - Onglet Réseaux](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. Dans la page du groupe de serveurs PostgreSQL, sous le titre Sécurité, cliquez sur **Réseaux** pour ouvrir les règles de pare-feu.

   ![Portail Azure - Cliquer sur Réseaux](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Cliquez sur **Ajouter une adresse IP cliente** dans la barre d’outils (option A ci-dessous) ou sur le lien (option B). Dans les deux cas, cette opération crée automatiquement une règle de pare-feu avec l’adresse IP publique de votre ordinateur, telle que détectée par le système Azure.

   ![Portail Azure - Cliquer sur Ajouter une adresse IP cliente](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

Vous pouvez également cliquer sur **+ Ajouter 0.0.0.0 - 255.255.255.255** (à droite de l’option B) pour autoriser l’accès au port 5432 du nœud coordinateur non seulement à votre adresse IP, mais à l’ensemble du réseau Internet. Dans ce cas, les clients doivent toujours se connecter avec le nom d’utilisateur et le mot de passe corrects pour utiliser le cluster. Néanmoins, nous vous recommandons d’autoriser l’accès mondial uniquement sur de courtes périodes et pour des bases de données hors production.

3. Vérifiez votre adresse IP avant d’enregistrer la configuration. Dans certains cas, l’adresse IP identifiée par le portail Azure diffère de l’adresse IP utilisée lors de l’accès à Internet et aux serveurs Azure. Par conséquent, vous devrez peut-être modifier l’adresse IP de début et l’adresse IP de fin pour que la règle fonctionne comme prévu.
   Utilisez un moteur de recherche ou tout autre outil en ligne pour vérifier votre adresse IP (par exemple, lancez une recherche sur « quelle est mon adresse IP »).

   ![Recherche Bing « quelle est mon adresse IP »](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Ajoutez des plages d’adresses supplémentaires. Dans les règles de pare-feu, vous pouvez spécifier une seule adresse IP ou une plage d’adresses. Si vous souhaitez limiter la règle à une seule adresse IP, saisissez la même adresse dans les champs d’adresse IP de début et d’adresse IP de fin. L’ouverture du pare-feu permet aux administrateurs, aux utilisateurs et aux applications d’accéder au nœud coordinateur sur le port 5432.

5. Cliquez sur **Enregistrer** sur la barre d’outils pour enregistrer cette règle de pare-feu de niveau serveur. Attendez la confirmation que la mise à jour des règles de pare-feu a réussi.

## <a name="connecting-from-azure"></a>Connexion à partir d’Azure

Il existe un moyen simple d’accorder l’accès à la base de données Hyperscale aux applications hébergées sur Azure (par exemple, une application Azure Web Apps ou celles qui s’exécutent sur une machine virtuelle Azure). Il vous suffit de définir l’option **Permettre aux services et ressources Azure d’accéder à ce groupe de serveurs** sur **Oui** dans le volet **Réseaux** du portail, puis de cliquer sur **Enregistrer**.

> [!IMPORTANT]
> Cette option configure le pare-feu pour autoriser toutes les connexions à partir d’Azure, notamment les connexions issues des abonnements d’autres clients. Lorsque vous sélectionnez cette option, vérifiez que votre connexion et vos autorisations utilisateur limitent l’accès aux seuls utilisateurs autorisés.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gérer des règles de pare-feu au niveau du serveur existantes via le portail Azure
Répétez les étapes pour gérer les règles de pare-feu.
* Pour ajouter l’ordinateur actuel, cliquez sur le bouton + **Ajouter une adresse IP cliente**. Cliquez sur **Enregistrer** pour enregistrer les modifications.
* Pour ajouter des adresses IP supplémentaires, remplissez les champs Nom de la règle, Adresse IP de début et Adresse IP de fin. Cliquez sur **Enregistrer** pour enregistrer les modifications.
* Pour modifier une règle existante, cliquez sur les champs dans la règle et modifiez-les. Cliquez sur **Enregistrer** pour enregistrer les modifications.
* Pour supprimer une règle existante, cliquez sur les points de suspension [...], puis sur **Supprimer**. Cliquez sur **Enregistrer** pour enregistrer les modifications.

## <a name="next-steps"></a>Étapes suivantes
- Apprenez-en plus sur le [concept des règles de pare-feu](concepts-hyperscale-firewall-rules.md) et notamment sur la résolution des problèmes de connexion.
