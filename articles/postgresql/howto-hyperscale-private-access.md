---
title: Activer l’accès privé (préversion) – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Comment configurer une liaison privée dans un groupe de serveurs pour Azure Database pour PostgreSQL – Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: e7694cd55eb6b6da7adb0313de47575880854cc5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075084"
---
# <a name="private-access-preview-in-azure-database-for-postgresql-hyperscale-citus"></a>Accès privé (préversion) dans Azure Database pour PostgreSQL – Hyperscale (Citus)

L’[accès privé](concepts-hyperscale-private-access.md) (préversion) permet aux ressources d’un réseau virtuel Azure de se connecter de façon sécurisée et privée aux nœuds d’un groupe de serveurs Hyperscale (Citus). Ce guide pratique suppose que vous avez déjà créé un réseau virtuel et un sous-réseau. Pour obtenir un exemple de configuration des conditions préalables, consultez le [tutoriel relatif à l’accès privé](tutorial-hyperscale-private-access.md).

## <a name="create-a-server-group-with-a-private-endpoint"></a>Créer un groupe de serveurs avec un point de terminaison privé

1. Sélectionnez **Créer une ressource** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Bases de données** dans la page **Nouveau**, puis **Base de données Azure pour PostgreSQL** dans la page **Bases de données**.

3. Pour l’option de déploiement, sélectionnez le bouton **Créer** sous **Groupe de serveurs Hyperscale (Citus)** .

4. Renseignez le formulaire des détails du nouveau serveur en indiquant votre groupe de ressources, le nom du groupe de serveurs souhaité, l’emplacement et le mot de passe utilisateur de la base de données.

5. Sélectionnez **Configurer le groupe de serveurs**, choisissez le plan souhaité, puis sélectionnez **Enregistrer**.

6. Sélectionnez **Suivant : Réseau** en bas de la page.

7. Sélectionnez **Accès privé (préversion)** .

    > [!NOTE]
    >
    > L’accès privé est disponible en préversion dans [certaines régions](concepts-hyperscale-limits.md#regions) uniquement.
    >
    > Si l’option d’accès privé ne peut pas être sélectionnée pour votre groupe de serveurs, même si votre groupe de serveurs se trouve dans une région autorisée, ouvrez une [demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Azure et incluez votre ID d’abonnement Azure pour obtenir l’accès.

8. Un écran appelé **Créer un point de terminaison privé** apparaît. Choisissez les valeurs appropriées pour vos ressources existantes, puis cliquez sur **OK** :

    - **Groupe de ressources**
    - **Lieu**
    - **Nom**
    - **Sous-ressource cible**
    - **Réseau virtuel**
    - **Sous-réseau**
    - **Intégrer à une zone DNS privée**

9. Après avoir créé le point de terminaison privé, sélectionnez **Vérifier + créer** pour créer votre groupe de serveurs Hyperscale (Citus).

## <a name="enable-private-access-on-an-existing-server-group"></a>Activer l’accès privé sur un groupe de serveurs existant

Pour créer un point de terminaison privé vers un nœud d’un groupe de serveurs existant, ouvrez la page **Mise en réseau** du groupe de serveurs.

1. Sélectionnez **+ Ajouter un point de terminaison privé**.

   :::image type="content" source="media/howto-hyperscale-private-access/networking.png" alt-text="Écran Mise en réseau":::

1. Dans l’onglet **Informations de base**, confirmez l’**abonnement**, le **groupe de ressources** et la **région**. Entrez un **nom** pour le point de terminaison, par exemple `my-server-group-eq`.

    > [!NOTE]
    >
    > Sauf si vous avez une bonne raison de faire un choix différent, nous vous recommandons de choisir un abonnement et une région qui correspondent à ceux de votre groupe de serveurs.  Les valeurs par défaut des champs du formulaire peuvent être incorrectes ; vérifiez-les et mettez-les à jour si nécessaire.

2. Sélectionnez **Suivant : Ressource >** . Dans la **sous-ressource cible**, choisissez le nœud cible du groupe de serveurs. En général, `coordinator` est le nœud souhaité.

3. Sélectionnez **Suivant : Configuration >** . Choisissez le **réseau virtuel** et le **sous-réseau** désirés. Personnalisez l’**intégration à un DNS privé** ou acceptez ses paramètres par défaut.

4. Sélectionnez **Suivant : Balises >** et ajoutez les balises souhaitées.

5. Enfin, sélectionnez **Vérifier + créer >** . Passez en revue les paramètres, puis sélectionnez **Créer** lorsque vous êtes satisfait.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’[accès privé](concepts-hyperscale-private-access.md) (préversion).
* Suivez un [tutoriel](tutorial-hyperscale-private-access.md) pour voir l’accès privé (préversion) en action.
