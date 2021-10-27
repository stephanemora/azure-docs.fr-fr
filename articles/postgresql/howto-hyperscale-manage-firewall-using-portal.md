---
title: Gérer des règles de pare-feu - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Créer et gérer des règles de pare-feu pour Azure Database pour PostgreSQL - Hyperscale (Citus) à l’aide du portail Azure
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: 23874f83e55fb8ff1ea3f60872cdfc10a64dfc80
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071571"
---
# <a name="manage-public-access-for-azure-database-for-postgresql---hyperscale-citus"></a>Gérer l’accès public pour Azure Database pour PostgreSQL - Hyperscale (Citus)

Les règles de pare-feu au niveau du serveur peuvent être utilisées pour gérer l’[accès public](concepts-hyperscale-firewall-rules.md) à un nœud coordinateur Hyperscale (Citus) à partir d’une adresse IP spécifiée (ou d’une plage d’adresses IP) dans l’Internet public.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un groupe de serveurs (consultez [Créer une base de données Azure pour PostgreSQL – groupe de serveurs Hyperscale (Citus)](quickstart-create-hyperscale-portal.md)).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Créer une règle de pare-feu au niveau du serveur dans le portail Azure

> [!NOTE]
> Ces paramètres sont également accessibles lors de la création d’une base de données Azure pour PostgreSQL - groupe de serveurs Hyperscale (Citus). Sous l’onglet **Réseaux**, cliquez sur **Point de terminaison public**.

> :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png" alt-text="Portail Azure - Onglet Réseaux":::

1. Dans la page du groupe de serveurs PostgreSQL, sous le titre Sécurité, cliquez sur **Réseaux** pour ouvrir les règles de pare-feu.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png" alt-text="Portail Azure - Cliquer sur Réseaux":::

2. Si vous le souhaitez, sélectionnez **Activer l’accès aux nœuds Worker**. Avec cette option, les règles de pare-feu autorisent l’accès à tous les nœuds Worker ainsi qu’au nœud coordinateur.

3. Cliquez sur **Ajouter l’adresse IP actuelle du client** pour créer une règle de pare-feu avec l’adresse IP publique de votre ordinateur, telle que détectée par le système Azure.

Vous pouvez également cliquer sur **+ Ajouter 0.0.0.0 - 255.255.255.255** (à droite de l’option B) pour autoriser l’accès au port 5432 du nœud coordinateur non seulement à votre adresse IP, mais à l’ensemble du réseau Internet. Dans ce cas, les clients doivent toujours se connecter avec le nom d’utilisateur et le mot de passe corrects pour utiliser le cluster. Néanmoins, nous vous recommandons d’autoriser l’accès mondial uniquement sur de courtes périodes et pour des bases de données hors production.

4. Vérifiez votre adresse IP avant d’enregistrer la configuration. Dans certains cas, l’adresse IP identifiée par le portail Azure diffère de l’adresse IP utilisée lors de l’accès à Internet et aux serveurs Azure. Ainsi, vous devrez peut-être modifier l’adresse IP de début et l’adresse IP de fin pour que la règle fonctionne comme prévu.
   Utilisez un moteur de recherche ou tout autre outil en ligne pour vérifier votre adresse IP (par exemple, lancez une recherche sur « quelle est mon adresse IP »).

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="Recherche Bing « quelle est mon adresse IP »":::

5. Ajoutez des plages d’adresses supplémentaires. Dans les règles de pare-feu, vous pouvez spécifier une seule adresse IP ou une plage d’adresses. Si vous souhaitez limiter la règle à une seule adresse IP, saisissez la même adresse dans les champs d’adresse IP de début et d’adresse IP de fin. L’ouverture du pare-feu permet aux administrateurs, aux utilisateurs et aux applications d’accéder au nœud coordinateur sur le port 5432.

6. Cliquez sur **Enregistrer** sur la barre d’outils pour enregistrer cette règle de pare-feu de niveau serveur. Attendez la confirmation que la mise à jour des règles de pare-feu a réussi.

## <a name="connecting-from-azure"></a>Connexion à partir d’Azure

Il existe un moyen simple d’accorder l’accès à la base de données Hyperscale (Citus) aux applications hébergées dans Azure (par exemple, une application Azure Web Apps ou celles qui s’exécutent sur une machine virtuelle Azure). Cochez la case **Permettre aux services et ressources Azure d’accéder à ce groupe de serveurs** dans le volet **Mise en réseau** du portail, puis cliquez sur **Enregistrer**.

> [!IMPORTANT]
> Cette option configure le pare-feu pour autoriser toutes les connexions à partir d’Azure, notamment les connexions provenant des abonnements d’autres clients. Lorsque vous sélectionnez cette option, vérifiez que votre connexion et vos autorisations utilisateur limitent l’accès aux seuls utilisateurs autorisés.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gérer des règles de pare-feu au niveau du serveur existantes via le portail Azure
Répétez les étapes pour gérer les règles de pare-feu.
* Pour ajouter l’ordinateur actuel, cliquez sur le bouton + **Ajouter l’adresse IP actuelle du client**. Cliquez sur **Enregistrer** pour enregistrer les modifications.
* Pour ajouter des adresses IP supplémentaires, remplissez les champs Nom de la règle, Adresse IP de début et Adresse IP de fin. Cliquez sur **Enregistrer** pour enregistrer les modifications.
* Pour modifier une règle existante, cliquez sur les champs dans la règle et modifiez-les. Cliquez sur **Enregistrer** pour enregistrer les modifications.
* Pour supprimer une règle existante, cliquez sur les points de suspension [...], puis sur **Supprimer**. Cliquez sur **Enregistrer** pour enregistrer les modifications.

## <a name="next-steps"></a>Étapes suivantes
- Apprenez-en plus sur le [concept des règles de pare-feu](concepts-hyperscale-firewall-rules.md) et notamment sur la résolution des problèmes de connexion.
