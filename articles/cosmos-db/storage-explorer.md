---
title: Gérer les ressources Azure Cosmos DB à l’aide de l’Explorateur Stockage Azure
description: Apprenez à vous connecter à Azure Cosmos DB et à gérer ses ressources à l’aide de l’Explorateur Stockage Azure.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: b892e4c5078b50bb865a715ddf12aebc1eb05f57
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799109"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Utiliser des données à l’aide de l’Explorateur Stockage Azure

L’utilisation d’Azure Cosmos DB dans l’Explorateur Stockage Azure permet aux utilisateurs de gérer des entités Azure Cosmos DB, de manipuler des données, de mettre à jour des procédures stockées et des déclencheurs, ainsi que d’autres entités Azure comme les files d’attente et les objets blob de stockage. À présent, vous pouvez utiliser le même outil pour gérer vos différentes entités Azure au même endroit. Pour le moment, l’Explorateur Stockage Azure prend en charge les comptes Cosmos configurés pour les API SQL, MongoDB, Graph et Table.


## <a name="prerequisites"></a>Prérequis

Un compte Cosmos avec l’API SQL ou l’API pour MongoDB d’Azure Cosmos DB. Si vous n’avez pas de compte, vous pouvez en créer un dans le portail Azure, comme décrit dans [Azure Cosmos DB : Développer une application web API SQL avec .NET et le portail Azure](create-sql-api-dotnet.md).

## <a name="installation"></a>Installation

Installez ici la version la plus récente de l’Explorateur Stockage Azure : [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/). Nous prenons désormais en charge les versions Windows, Linux et MAC.

## <a name="connect-to-an-azure-subscription"></a>Connexion à un abonnement Azure

1. Après avoir installé l’**Explorateur Stockage Azure**, sélectionnez l’icône de **plug-in** à gauche, comme illustré dans l’image suivante :

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Sélection de l’icône de plug-in pour vous connecter":::

2. Sélectionnez **Ajouter un compte Azure**, puis **Connexion**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Connexion à l’abonnement Azure requis":::

2. Dans la boîte de dialogue **Connexion à Azure**, sélectionnez **Se connecter**, puis entrez vos informations d’identification Azure.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Connexion à votre abonnement Azure":::

3. Sélectionnez votre abonnement dans la liste, puis sélectionnez **Appliquer**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Choix d’un ID d’abonnement dans la liste à filtrer":::

    Le volet Explorateur se met à jour et affiche les comptes de l’abonnement sélectionné.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Sélection d’un compte Azure Cosmos DB dans la liste disponible":::

    Vous êtes connecté à votre **compte Cosmos DB** dans votre abonnement Azure.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Se connecter à Azure Cosmos DB à l’aide d’une chaîne de connexion

Une autre façon de se connecter à Azure Cosmos DB est d’utiliser une chaîne de connexion. Utilisez les étapes suivantes pour vous connecter à l’aide d’une chaîne de connexion.

1. Recherchez **Locaux et joints** dans l’arborescence de gauche, cliquez avec le bouton droit sur **Comptes Cosmos DB**, choisissez **Connect to Cosmos DB...** (Se connecter à Cosmos DB...)

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Se connecter à Azure Cosmos DB à l’aide d’une chaîne de connexion":::

2. Actuellement seules les API SQL et Table sont prises en charge. Choisissez l’API, collez la **chaîne de connexion**, renseignez le champ **Libellé de compte**, sélectionnez **Suivant** pour passer en revue le résumé, puis sélectionnez **Connexion** pour vous connecter au compte Azure Cosmos DB. Pour plus d’informations sur la récupération de la chaîne de connexion principale, voir [Obtenir la chaîne de connexion](manage-with-powershell.md#list-keys).

    :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Saisie de votre chaîne de connexion":::

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Se connecter à Azure Cosmos DB à l’aide d’un émulateur local

Suivez les étapes ci-après pour vous connecter à Azure Cosmos DB à l’aide d’un émulateur ; seul le compte SQL est pris en charge actuellement.

1. Installez l’émulateur et lancez-le. Pour savoir comment installer l’émulateur, consultez [Utilisation de l’émulateur Azure Cosmos DB pour le développement local et le test](https://docs.microsoft.com/azure/cosmos-db/local-emulator)

2. Recherchez **Locaux et joints** dans l’arborescence de gauche, cliquez avec le bouton droit sur **Comptes Cosmos DB**, choisissez **Connect to Cosmos DB Emulator...** (Se connecter à l’émulateur Cosmos DB...)

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Connexion à Azure Cosmos DB à partir de l’émulateur":::

3. Actuellement seules les API SQL sont prises en charge. Collez la **chaîne de connexion**, renseignez le champ **Libellé de compte**, sélectionnez **Suivant** pour passer en revue le résumé, puis sélectionnez **Connexion** pour vous connecter au compte Azure Cosmos DB. Pour plus d’informations sur la récupération de la chaîne de connexion principale, voir [Obtenir la chaîne de connexion](manage-with-powershell.md#list-keys).

    :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Connexion à Cosmos DB à partir de la boîte de dialogue de l’émulateur":::


## <a name="azure-cosmos-db-resource-management"></a>Gestion des ressources Azure Cosmos DB

Vous pouvez gérer un compte Azure Cosmos DB en effectuant les opérations suivantes :
* Ouvrir le compte dans le portail Azure
* Ajouter la ressource à la liste d’accès rapide
* Rechercher et actualiser les ressources
* Créer et supprimer des bases de données
* Créer et supprimer des collections
* Créer, modifier, supprimer et filtrer les documents
* Gérer les procédures stockées, les déclencheurs et les fonctions définies par l’utilisateur

### <a name="quick-access-tasks"></a>Tâches d’accès rapide

En cliquant avec le bouton droit sur un abonnement dans le volet Explorateur, vous pouvez effectuer de nombreuses tâches d’action rapide :

* Cliquez avec le bouton droit sur un compte ou une base de données Azure Cosmos DB, vous pouvez choisir **Ouvrir dans le portail** et gérer la ressource dans le navigateur sur le portail Azure.

     :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Ouvrir dans le portail":::

* Vous pouvez également ajouter un compte, une base de données ou une collection Azure Cosmos DB à l’**Accès rapide**.
* **Rechercher à partir d’ici** vous permet d’effectuer une recherche par mot clé dans le chemin sélectionné.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="rechercher à partir d’ici":::

### <a name="database-and-collection-management"></a>Gestion des bases de données et des collections

#### <a name="create-a-database"></a>Création d'une base de données

-   Cliquez avec le bouton droit sur le compte Azure Cosmos DB, choisissez **Créer une base de données**, entrez le nom de la base de données, puis appuyez sur **Entrée** pour terminer.

    :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Création d’une base de données dans votre compte Azure Cosmos":::

#### <a name="delete-a-database"></a>Supprimer une base de données

- Cliquez avec le bouton droit sur la base de données, sélectionnez **Supprimer la base de données**, puis **Oui** dans la fenêtre contextuelle. Le nœud de base de données est supprimé et le compte Azure Cosmos DB s’actualise automatiquement.

    :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Suppression de la première base de données":::

    :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Suppression de la seconde base de données":::

#### <a name="create-a-collection"></a>Création d'une collection

1. Cliquez avec le bouton droit sur votre base de données, choisissez **Créer une collection**, puis fournissez les informations suivantes de type **ID de collection**, **Capacité de stockage**, etc. Cliquez sur **OK** pour terminer.

    :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Création de la première collection dans la base de données":::

    :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Création de la seconde collection dans la base de données":::

2. Sélectionnez **Illimité** pour être en mesure de spécifier la clé de partition, puis sélectionnez **OK** pour terminer.

    Si une clé de partition est utilisée pendant la création d’une collection, une fois la création terminée, la valeur de la clé de partition ne peut pas être changée sur la collection.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Configuration d’une clé de partition":::

#### <a name="delete-a-collection"></a>Supprimer une collection

- Cliquez avec le bouton droit sur la collection, sélectionnez **Supprimer la collection**, puis **Oui** dans la fenêtre contextuelle.

    Le nœud de collection est supprimé et la base de données s’actualise automatiquement.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Suppression de l’une des collections":::

### <a name="document-management"></a>Gestion de documents

#### <a name="create-and-modify-documents"></a>Créer et modifier des documents

- Pour créer un document, ouvrez **Documents** dans la fenêtre de gauche, sélectionnez **Nouveau document**, modifiez le contenu dans le volet droit, puis sélectionnez **Enregistrer**. Vous pouvez également mettre à jour un document existant, puis sélectionner **Enregistrer**. Les changements peuvent être ignorés en cliquant sur **Ignorer**.

    :::image type="content" source="./media/storage-explorer/document.png" alt-text="Créer un document":::

#### <a name="delete-a-document"></a>Supprimer un document

- Cliquez sur le bouton **Supprimer** pour supprimer le document sélectionné.

#### <a name="query-for-documents"></a>Rechercher des documents

- Modifiez le filtre de document en entrant une [requête SQL](how-to-sql-query.md), puis sélectionnez **Appliquer**.

    :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Requête de documents spécifiques":::

### <a name="graph-management"></a>Gestion des graphiques

#### <a name="create-and-modify-vertex"></a>Créer et modifier un sommet

1. Pour créer un sommet, ouvrez **Graphe** dans la fenêtre de gauche, sélectionnez **Nouveau sommet**, modifiez le contenu, puis sélectionnez **OK**.
2. Pour modifier un sommet existant, sélectionnez l’icône de crayon dans le volet droit.

    :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Modification du sommet d’un graphe":::

#### <a name="delete-a-graph"></a>Supprimer un graphique

- Pour supprimer un sommet, sélectionnez l’icône de corbeille à côté du nom du sommet.

#### <a name="filter-for-graph"></a>Filtrer un graphique

- Modifiez le filtre de graphe en entrant une [requête Gremlin](gremlin-support.md), puis sélectionnez **Appliquer un filtre**.

    :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Exécution d’une requête de graphe":::

### <a name="table-management"></a>Gestion des tables

#### <a name="create-and-modify-table"></a>Créer et modifier une table

1. Pour créer une table, ouvrez **Entités** dans la fenêtre de gauche, sélectionnez **Ajouter**, modifiez le contenu de la boîte de dialogue **Ajouter une entité**, ajoutez une propriété en cliquant sur le bouton **Ajouter une propriété**, puis sélectionnez **Insérer**.
2. Pour modifier une table, sélectionnez **Modifier**, modifiez le contenu, puis sélectionnez **Mettre à jour**.

    :::image type="content" source="./media/storage-explorer/table.png" alt-text="Créer et modifier une table":::

#### <a name="import-and-export-table"></a>Importer et exporter une table

1. Pour importer, sélectionnez le bouton **Importer** et choisissez une table existante.
2. Pour exporter, sélectionnez le bouton **Exporter** et choisissez une destination.

    :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Importation ou exportation d’une table":::

#### <a name="delete-entities"></a>Supprimer des entités

- Sélectionnez les entités et sélectionnez le bouton **Supprimer**.

    :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Suppression d’une table":::

#### <a name="query-table"></a>Interroger une table

- Cliquez sur le bouton **Requête**, entrez la condition de requête, puis sélectionnez le bouton **Exécuter la requête**. Fermez le volet Requête en cliquant sur le bouton **Fermer la requête**.

    :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Exécution d’une requête de données dans la table":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gérer les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur

* Pour créer une procédure stockée, dans l’arborescence de gauche, cliquez avec le bouton droit sur **Procédure stockée**, choisissez **Créer une procédure stockée**, entrez un nom à gauche, tapez les scripts de procédure stockée dans la fenêtre de droite, puis sélectionnez **Créer**.
* Vous pouvez également modifier des procédures stockées existantes, en double-cliquant dessus, en effectuant la mise à jour, puis en cliquant sur **Mettre à jour** pour enregistrer, ou sélectionnez **Ignorer** pour annuler la modification.

    :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Création et suppression de procédures stockées":::
* Les opérations pour les **déclencheurs** et les **fonctions définies par l’utilisateur** sont similaires à celles des **procédures stockées**.

## <a name="troubleshooting"></a>Dépannage

[Azure Cosmos DB dans l’Explorateur Stockage Azure](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) est une application autonome qui permet de se connecter aux comptes Azure Cosmos DB hébergés sur Azure et sur les clouds souverains à partir de Windows, Mac OS ou Linux. Elle vous permet de gérer des entités Azure Cosmos DB, de manipuler des données, de mettre à jour des procédures stockées et des déclencheurs, ainsi que d’autres entités Azure comme les files d’attente et les objets blob de stockage.

Voici les solutions aux problèmes couramment rencontrés dans l’Explorateur Stockage d’Azure Cosmos DB.

### <a name="sign-in-issues"></a>Problèmes de connexion

Avant de continuer, essayez de redémarrer votre application et de voir si les problèmes peuvent être résolus.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certificat auto-signé dans la chaîne d’approbation

Vous pouvez voir cette erreur pour plusieurs raisons. Les deux plus courantes sont :

+ Vous vous trouvez derrière un *proxy transparent*, ce qui signifie que quelqu’un (par exemple votre service informatique) intercepte le trafic HTTPS, le déchiffre, puis le chiffre en utilisant un certificat auto-signé.

+ Vous exécutez un logiciel, tel qu’un logiciel antivirus, qui injecte un certificat TLS/SSL auto-signé dans les messages HTTPS que vous recevez.

Lorsque l’Explorateur de stockage rencontre l’un de ces « certificats auto-signés », il ne peut plus savoir si le message HTTPS qu’il reçoit a été falsifié. Si vous avez une copie du certificat auto-signé, vous pouvez indiquer à l’Explorateur de stockage de lui faire confiance. Si vous ne savez pas qui injecte le certificat, vous pouvez essayer de le découvrir vous-même en procédant comme suit :

1. Installer OpenSSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (n’importe quelle version légère convient)
     - Mac et Linux : doit être inclus dans votre système d’exploitation
2. Exécuter OpenSSL
    - Windows : accédez au répertoire d’installation, puis **/bin/** , puis double-cliquez sur **openssl.exe**.
    - Mac et Linux : exécutez **openssl** à partir d’un terminal
3. Exécutez `s_client -showcerts -connect microsoft.com:443`
4. Recherchez les certificats auto-signés. Si vous ne savez pas lesquels sont auto-signés, recherchez ceux dont le sujet (« s: ») et l’émetteur (« i: ») sont identiques.
5.  Après avoir trouvé les certificats auto-signés, pour chacun d’eux copiez et collez tout depuis **-----BEGIN CERTIFICATE-----** jusqu’à **-----END CERTIFICATE-----** (les deux inclus) dans un nouveau fichier .cer.
6.  Ouvrez l’Explorateur de stockage puis accédez à **Modifier** > **Certificats SSL** > **Importer des certificats**. À l’aide du sélecteur de fichiers, recherchez, sélectionnez et ouvrez les fichiers .cer que vous avez créé.

Si vous ne trouvez aucun certificat auto-signé à l’aide de la procédure ci-dessus, vous pouvez envoyer un commentaire pour obtenir de l’aide.

#### <a name="unable-to-retrieve-subscriptions"></a>Impossible de récupérer les abonnements

Si vous ne parvenez pas à récupérer vos abonnements une fois connecté :

- Vérifiez que votre compte a accès aux abonnements en vous connectant au [portail Azure](https://portal.azure.com/)
- Assurez-vous que vous vous êtes connecté à l’aide de l’environnement approprié ([Azure](https://portal.azure.com/), [Azure - Chine](https://portal.azure.cn/), [Azure - Allemagne](https://portal.microsoftazure.de/), [Azure - Gouvernement des États-Unis](https://portal.azure.us/) ou Environnement personnalisé/Azure Stack).
- Si vous vous trouvez derrière un proxy, vérifiez que vous avez correctement configuré le proxy de l’Explorateur de stockage.
- Essayez de supprimer et de rajouter le compte
- Essayez de supprimer les fichiers suivants de votre répertoire de base (par exemple : C:\Users\ContosoUser), , puis de rajouter le compte :
  - .adalcache
  - .devaccounts
  - .extaccounts
- Quand vous vous connectez, vérifiez si la console des outils de développement indique des messages d’erreur (en appuyant sur F12).

:::image type="content" source="./media/storage-explorer/console.png" alt-text="Recherche d’erreurs dans la console Outils de développement":::

#### <a name="unable-to-see-the-authentication-page"></a>Impossible de voir la page d’authentification

Si vous ne parvenez pas à voir la page d’authentification :

- Selon la vitesse de votre connexion, le chargement de la page de connexion peut prendre un certain temps ; attendez au moins une minute avant de fermer la boîte de dialogue d’authentification.
- Si vous vous trouvez derrière un proxy, vérifiez que vous avez correctement configuré le proxy de l’Explorateur de stockage.
- Affichez la console de développement en appuyant sur la touche F12. Dans les réponses indiquées par la console de développement, recherchez des indices éventuels sur la raison du dysfonctionnement de l’authentification.

#### <a name="cannot-remove-account"></a>Impossible de supprimer un compte

Si vous ne pouvez pas supprimer un compte, ou que le lien de réauthentification est inopérant

- Essayez de supprimer les fichiers suivants de votre répertoire de base, puis de rajouter le compte :
  - .adalcache
  - .devaccounts
  - .extaccounts
- Si vous souhaitez supprimer des ressources de stockage jointes SAP, supprimez :
  - Dossier %AppData%/StorageExplorer pour Windows
  - /Users/<votre_nom>/Library/Application SUpport/StorageExplorer pour Mac
  - ~/.config/StorageExplorer pour Linux
  - **Vous devrez entrer à nouveau toutes vos informations d’identification** si vous supprimez ces fichiers.


### <a name="httphttps-proxy-issue"></a>Problème de proxy HTTP/HTTPS

Vous ne pouvez pas afficher les nœuds Azure Cosmos DB dans l’arborescence de gauche lors de la configuration du proxy HTTP/HTTPS dans ASE. Pour le moment, vous pouvez utiliser l’Explorateur de données Azure Cosmos DB dans le portail Azure pour contourner ce problème.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problème de nœud de « Développement » sous le nœud « Local et attaché »

Il n’y a pas de réponse après avoir sélectionné le nœud « Développement » sous le nœud « Local et attaché » dans l’arborescence de gauche.  Ce comportement est attendu. L’émulateur local Azure Cosmos DB sera pris en charge dans la prochaine version.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Nœud de développement":::

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Erreur lors de l’attachement du compte Azure Cosmos DB dans le nœud « Local et attaché »

Si vous voyez l’erreur ci-dessous après avoir attaché le compte Azure Cosmos DB dans le nœud « Local et attaché», vérifiez si vous utilisez la bonne chaîne de connexion.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Erreur lors de l’attachement d’Azure Cosmos DB dans « Local et attaché »":::

### <a name="expand-azure-cosmos-db-node-error"></a>Erreur de développement du nœud Azure Cosmos DB

L’erreur ci-dessous peut s’afficher lors de la tentative de développement des nœuds dans l’arborescence de gauche.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Erreur de développement du nœud Azure Cosmos DB":::

Essayez les suggestions suivantes :

- Vérifiez si le compte Azure Cosmos DB est en cours d’approvisionnement et recommencez une fois le compte créé avec succès.
- Si le compte est sous le nœud « Accès rapide » ou « Local et connecté », vérifiez si le compte a été supprimé. Si c’est le cas, vous devez supprimer le nœud manuellement.

## <a name="next-steps"></a>Étapes suivantes

* Visionnez la vidéo suivante pour voir comment utiliser Azure Cosmos DB dans l’Explorateur Stockage Azure : [Utiliser Azure Cosmos DB dans l’Explorateur Stockage Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Pour en savoir plus sur l’Explorateur Stockage et sur comment connecter plus de services, consultez [Prise en main de l’Explorateur Stockage (préversion)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
