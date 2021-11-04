---
title: Se connecter à Azure Cosmos DB
description: Traitez et créez des documents Azure Cosmos DB à l’aide d’Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: jcocchi
ms.author: jucocchi
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
tags: connectors
ms.openlocfilehash: 7a5025e284933284d910412fb2f9771dee43e31d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098020"
---
# <a name="process-and-create-azure-cosmos-db-documents-using-azure-logic-apps"></a>Traiter et créer des documents Azure Cosmos DB à l’aide d’Azure Logic Apps

À partir de votre flux de travail dans Azure Logic Apps, vous pouvez vous connecter à Azure Cosmos DB et travailler sur des documents à l’aide du [connecteur Azure Cosmos DB](/connectors/documentdb/). Ce connecteur fournit des déclencheurs et des actions que votre flux de travail peut utiliser pour des effectuer opérations Azure Cosmos DB. Vous pouvez créer des flux de travail automatisés pour gérer des documents. Des actions possibles sont, par exemple, la création ou la mise à jour, la lecture, l’interrogation et la suppression de documents.

Vous pouvez vous connecter à Azure Cosmos DB à partir des types de ressources **Application logique (Consommation)** et **Application logique (Standard)** à l’aide des opérations de [*connecteur géré*](managed.md). Pour **Application logique (Standard)** , Azure Cosmos DB fournit également des opérations [*intégrées*](built-in.md), actuellement en préversion, qui offrent d’autres fonctionnalités, de meilleures performances et un débit plus élevé. Par exemple, si vous utilisez le type de ressource **Application logique (Standard)** , vous pouvez utiliser le déclencheur intégré pour répondre aux modifications dans un conteneur Azure Cosmos DB. Vous pouvez combiner des opérations d’Azure Cosmos DB avec d’autres actions et déclencheurs dans vos flux de travail d’application logique pour activer des scénarios tels que l’approvisionnement en événements et le traitement de données générales.

> [!NOTE]
> Actuellement, seuls des flux de travail avec état dans une ressource de type **Application logique (Standard)** peuvent utiliser les opérations de connecteur managé et les opérations intégrées. Des flux de travail sans état peuvent utiliser uniquement des opérations intégrées.

## <a name="prerequisites"></a>Prérequis

- Un compte et un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Un [compte Azure Cosmos DB](../cosmos-db/sql/create-cosmosdb-resources-portal.md).

- Un flux de travail d’application logique à partir duquel vous souhaitez accéder à votre compte Azure Cosmos DB. Pour utiliser le déclencheur Azure Cosmos DB, vous devez [créer votre application logique à l’aide du type de ressource **Application logique (Standard)** ](../logic-apps/create-single-tenant-workflows-azure-portal.md) et ajouter un flux de travail vide.

## <a name="add-azure-cosmos-db-trigger"></a>Ajouter un déclencheur Azure Cosmos DB

Dans Azure Logic Apps, chaque flux de travail doit commencer par un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou qu’une condition particulière est remplie.

Si vous utilisez le type de ressource **Application logique (Standard)** , le déclencheur intégré nommé **Lors de la création ou de la modification d’un élément (préversion)** est disponible. Celui-ci est basé sur le [modèle de flux de modification Azure Cosmos DB](../cosmos-db/sql/change-feed-design-patterns.md). Ce déclencheur n’est pas disponible pour le type de ressource **Application logique (Consommation)** .

### <a name="consumption"></a>[Consommation](#tab/consumption)

Aucun déclencheur Azure Cosmos DB n’est disponible pour le type de ressource **Application logique (Consommation)** .

### <a name="standard"></a>[Standard](#tab/standard)

Pour ajouter un déclencheur intégré Azure Cosmos DB à un flux de travail d’application logique dans Azure Logic Apps monolocataire, procédez comme suit :

1. Sur le [Portail Azure](https://portal.azure.com), ouvrez votre flux de travail d’application logique dans le Concepteur.

1. Pour trouver le déclencheur, procédez comme suit :

   1. Dans le concepteur, sélectionnez **Choisir une opération**.

   1. Une fois le volet **Ajouter un déclencheur** ouvert, dans la zone de recherche **Choisir une opération**, sélectionnez **Intégrée**.

   1. Dans la zone de recherche, entrez `Azure Cosmos DB`. Dans la liste des déclencheurs, sélectionnez le déclencheur nommé **Lors de la création ou de la modification d’un élément (préversion)** .

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-trigger-add.png" alt-text="Capture d’écran montrant le portail Azure et le concepteur pour un flux de travail d’application logique Standard avec le déclencheur nommé « Lors de la création ou de la modification d’un élément (préversion) » sélectionné.":::

1. Si vous êtes invité à entrer les informations de connexion, [créez une connexion à Azure Cosmos DB maintenant](#connect-to-azure-cosmos-db).

1. Sous l’onglet **Paramètres**, fournissez les informations nécessaires pour le déclencheur.

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **ID de base de données** | Oui | <*database-name*> | Nom de la base de données avec le conteneur que vous souhaitez surveiller. Cette base de données doit également comprendre le conteneur de baux. Si vous n’avez pas encore de conteneur de baux, le connecteur en créera un pour vous dans une étape ultérieure. |
   | **ID de conteneur surveillé** | Oui | <*container-name*> | Nom du conteneur que vous souhaitez surveiller. Ce conteneur doit préexister dans la base de données spécifiée. |
   | **ID de conteneur de baux** |  Oui | <*lease-container-name*> | Nom d’un conteneur de baux existant ou d’un nouveau conteneur à créer pour vous. Le déclencheur pré-remplit `leases` comme nom par défaut commun. |
   | **Créer un conteneur de baux** | Non | **Non** ou **Oui** | Si le conteneur de baux existe déjà dans la base de données spécifiée, sélectionnez **Non**. Si vous souhaitez que le déclencheur crée ce conteneur, sélectionnez **Oui**. Si vous sélectionnez **Oui**, veillez à ouvrir la liste **Ajouter un nouveau paramètre**, puis sélectionnez la propriété **Débit du conteneur de baux**. Entrez le nombre d’[unités de requête (RU)](../cosmos-db/request-units.md) que vous souhaitez approvisionner pour ce conteneur. |
   |||||

   L’illustration suivante montre un exemple de déclencheur :

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-trigger-parameters.png" alt-text="Capture d’écran montrant le Concepteur pour un flux de travail d’application logique Standard avec un déclencheur Azure Cosmos DB et la configuration des paramètres.":::

   > [!Note]
   > Le déclencheur crée plusieurs exécutions de flux de travail pour chaque élément créé ou modifié dans Azure Cosmos DB, de sorte que la sortie de contenu dynamique de ce déclencheur est toujours un élément unique.

1. Ajoutez toutes les autres actions souhaitées à votre flux de travail.

1. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

---

## <a name="add-azure-cosmos-db-action"></a>Ajouter une action Azure Cosmos DB

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre flux de travail qui suit un déclencheur ou une autre action. Le connecteur Azure Cosmos DB offre des actions pour les types de ressources **Application logique (Consommation)** et **Application logique (Standard)** . Les exemples suivants pour chaque type de ressource montrent comment utiliser une action qui crée ou met à jour un document.

### <a name="consumption"></a>[Consommation](#tab/consumption)

Pour ajouter une action Azure Cosmos DB à un flux de travail d’application logique dans Azure Logic Apps multilocataire, procédez comme suit :

1. Sur le [Portail Azure](https://portal.azure.com), ouvrez votre flux de travail dans le Concepteur.

1. Si votre flux de travail est vide, ajoutez le déclencheur de votre choix.

   Cet exemple démarre avec le déclencheur [**Lors de la réception d’une requête HTTP**](connectors-native-reqres.md#add-request-trigger).

1. Sous le déclencheur ou l’action auxquels vous souhaitez ajouter l’action Azure Cosmos DB, sélectionnez **Nouvelle étape** ou **Ajouter une action** s’il y a des étapes intermédiaires.

1. Dans la zone de recherche du Concepteur, entrez `Azure Cosmos DB`. Sélectionnez l’action Azure Cosmos DB que vous souhaitez utiliser.

   Cet exemple utilise l’action nommée **Créer ou mettre à jour un document (v3)** .

   :::image type="content" source="./media/connectors-create-api-cosmos-db/consumption-action-add.png" alt-text="Capture d’écran montrant le concepteur pour un flux de travail d’application logique Consommation avec les actions Azure Cosmos DB disponibles.":::

1. Si vous êtes invité à entrer les informations de connexion, [créez une connexion à votre compte Azure Cosmos DB maintenant](#connect-to-azure-cosmos-db).

1. Entrez les informations nécessaires pour l’action.

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Nom de compte Azure Cosmos DB** | Oui | Sélectionnez **Utiliser les paramètres de connexion (<*Azure-Cosmos-DB-account-name*>)** ou entrez le nom manuellement. | Nom de compte de votre compte Azure Cosmos DB. |
   | **ID de base de données** | Oui | <*database-ID*> | Base de données à laquelle vous souhaitez vous connecter. |
   | **ID de conteneur** | Oui | <*container-ID*> | Conteneur que vous souhaitez interroger. |
   | **Document** | Oui | <*JSON-document*> | Document JSON que vous souhaitez créer. Cet exemple utilise le corps de la demande de la sortie du déclencheur. <p><p>**Conseil** : si le jeton **Body** du déclencheur HTTP ne figure pas dans la liste de contenu dynamique à ajouter, en regard du nom du déclencheur, sélectionnez **Afficher plus**. <p><p>**Remarque**: assurez-vous que le format JSON du corps est correct et qu’il contient au minimum la propriété `id` et la propriété de clé de partition pour votre document. Si un document avec l’`id` et la clé de partition spécifiés existe déjà, le document est mis à jour. Sinon, un nouveau document est créé. |
   |||||

   L’illustration suivante montre un exemple d’action :

   :::image type="content" source="./media/connectors-create-api-cosmos-db/consumption-create-action.png" alt-text="Capture d’écran montrant le concepteur pour un flux de travail d’application logique Consommation avec l’action Azure Cosmos DB « Créer ou mettre à jour les documents (V3) » et la configuration des paramètres.":::

1. Configurez tous les autres paramètres d’action en fonction des besoins.

1. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

1. Testez votre application logique pour vous assurer que votre flux de travail crée un document dans le conteneur spécifié.

### <a name="standard"></a>[Standard](#tab/standard)

Pour ajouter une action Azure Cosmos DB à un flux de travail d’application logique dans Azure Logic Apps monolocataire, procédez comme suit :

1. Sur le [Portail Azure](https://portal.azure.com), ouvrez votre flux de travail dans le Concepteur.

1. Si votre flux de travail est vide, ajoutez le déclencheur de votre choix.

   Cet exemple commence par le [déclencheur **Lors de la réception d’une requête HTTP**](connectors-native-reqres.md#add-request-trigger) qui utilise une définition de schéma de base pour représenter l’élément que vous souhaitez créer.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-http-trigger.png" alt-text="Capture d’écran montrant le portail Azure et le concepteur pour un flux de travail d’application logique Standard avec le déclencheur « Lors de la réception d’une requête HTTP » et la configuration des paramètres.":::

1. Sous le déclencheur ou l’action à laquelle vous souhaitez ajouter l’action Azure Cosmos DB, sélectionnez **Insérer une nouvelle étape** ( **+** ) > **Ajouter une action**.

1. Dans le Concepteur, vérifiez que l’option **Ajouter une opération** est sélectionnée. Dans le volet **Ajouter une action** qui s’ouvre, sous la zone de recherche **Choisir une opération**, sélectionnez **Intégré** pour trouver les actions **Azure Cosmos DB**.

   > [!NOTE]
   > Si vous avez un flux de travail avec état, des actions de *connecteur managé* sont également disponibles sous l’onglet **Azure**. Ne les utilisez que si les actions *intégrées* que vous souhaitez ne sont pas disponibles.

1. Dans la zone de recherche, entrez `Azure Cosmos DB`. Sélectionnez l’action Azure Cosmos DB que vous souhaitez utiliser.

   Cet exemple utilise l’action nommée **Créer ou mettre à jour un élément (préversion)** qui crée un élément ou met à jour un élément existant.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-action-add.png" alt-text="Capture d’écran montrant le concepteur pour un flux de travail d’application logique Standard et les actions Azure Cosmos DB disponibles.":::

1. Si vous êtes invité à entrer les informations de connexion, [créez une connexion à votre compte Azure Cosmos DB maintenant](#connect-to-azure-cosmos-db).

1. Entrez les informations nécessaires pour l’action.

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **ID de base de données** | Oui | <*database-ID*> | Base de données à laquelle vous souhaitez vous connecter. |
   | **ID de conteneur** | Oui | <*container-ID*> | Conteneur que vous souhaitez interroger. |
   | **Item** | Oui | <*JSON-document*> | Document JSON que vous souhaitez créer. Cet exemple utilise le corps de la demande de la sortie du déclencheur. <p><p>**Conseil** : si le jeton **Body** du déclencheur HTTP ne figure pas dans la liste de contenu dynamique à ajouter, en regard du nom du déclencheur, sélectionnez **Afficher plus**. <p><p>**Remarque** : assurez-vous que le format JSON du corps est correct et qu’il contient au minimum la propriété `id` et la propriété de clé de partition pour votre document. Si un document avec l’`id` et la clé de partition spécifiés existe déjà, le document est mis à jour. Sinon, un nouveau document est créé. | 
   | **Clé de partition** | Oui | <*partition-key*> | Valeur de clé de partition pour le document que vous souhaitez créer. Si cette propriété n’apparaît pas, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez **Clé de partition**. <p><p>**Important** : bien que cette propriété ne soit pas marquée comme obligatoire avec un astérisque (*), elle est requise pour que le flux de travail aboutisse. |
   |||||

   L’illustration suivante montre un exemple d’action :

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-create-action.png" alt-text="Capture d’écran montrant le concepteur pour un flux de travail d’application logique Standard avec l’action Azure Cosmos DB « Créer ou mettre à jour l’élément » et la configuration des paramètres.":::

1. Configurez tous les autres paramètres d’action en fonction des besoins.

1. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

1. Testez votre application logique pour vous assurer que votre flux de travail crée un document dans le conteneur spécifié.

---

## <a name="connect-to-azure-cosmos-db"></a>Se connecter à Azure Cosmos DB

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Pour pouvoir configurer votre [déclencheur Azure Cosmos DB](#add-azure-cosmos-db-trigger) ou votre [action Azure Cosmos DB](#add-azure-cosmos-db-action), vous devez vous connecter à un compte de base de données.

### <a name="consumption"></a>[Consommation](#tab/consumption)

Dans un flux de travail d’**application logique (Consommation)** , une connexion Azure Cosmos DB requiert les valeurs de propriétés suivantes :

| Propriété | Obligatoire | Valeur | Description |
|----------|----------|-------|-------------|
| **Nom de connexion** | Oui | <*connection-name*> | Nom à utiliser pour votre connexion. |
| **Type d'authentification** | Oui | <*connection-type*> | Le type d’authentification que vous souhaitez utiliser. Cet exemple utilise une **Clé d’accès**. <p><p>- Si vous sélectionnez **Clé d’accès**, spécifiez les autres valeurs de propriétés requises pour créer la connexion. <p><p>- Si vous sélectionnez **Azure AD intégré**, aucune autre valeur de propriété n’est requise, mais vous devez configurer votre connexion en suivant les étapes décrites dans [Authentification Azure AD et connecteur Cosmos DB](/connectors/documentdb/#azure-ad-authentication-and-cosmos-db-connector).  |
| **Clé d’accès à votre compte Cosmos DB** | Oui | <*access-key*> | Clé d’accès pour le compte Azure Cosmos DB à utiliser pour cette connexion. Cette valeur est soit une clé en lecture-écriture, soit une clé en lecture seule. <p><p>**Remarque** : pour trouver la clé, accédez à la page du compte Azure Cosmos DB. Dans le menu de navigation, sous **Paramètres**, sélectionnez **Identité**. Copiez l’une des valeurs de clé disponibles. |
| **ID de compte** | Oui | <*account-ID*> | Nom du compte Azure Cosmos DB à utiliser pour cette connexion. |
|||||

L’illustration suivante montre un exemple de connexion :

:::image type="content" source="./media/connectors-create-api-cosmos-db/consumption-connection-configure.png" alt-text="Capture d’écran montrant un exemple de configuration de connexion Azure Cosmos DB pour un flux de travail d’application logique Consommation.":::

> [!NOTE]
> Si, après avoir créé votre connexion, vous souhaitez utiliser une autre connexion Azure Cosmos DB existante ou créer une connexion, sélectionnez **Modifier la connexion** dans l’éditeur des informations relatives au déclencheur ou à l’action.

### <a name="standard"></a>[Standard](#tab/standard)

Dans un flux de travail d’**application logique (Standard)** , une connexion Azure Cosmos DB requiert les valeurs de propriétés suivantes :

| Propriété | Obligatoire | Valeur | Description |
|----------|----------|-------|-------------|
| **Nom de connexion** | Oui | <*connection-name*> | Nom à utiliser pour votre connexion. |
| **Chaîne de connexion** | Oui | <*connection-string*> | Chaîne de connexion Azure Cosmos DB à utiliser pour votre connexion. <p><p>**Remarque** : pour trouver la chaîne de connexion, accédez à la page du compte Azure Cosmos DB. Dans le menu de navigation, sous **Paramètres**, sélectionnez **Identité**. Copiez l’une des valeurs de chaîne de connexion disponibles. |
|||||

L’illustration suivante montre un exemple de connexion :

:::image type="content" source="./media/connectors-create-api-cosmos-db/standard-connection-configure.png" alt-text="Capture d’écran montrant un exemple de configuration de connexion Azure Cosmos DB pour un flux de travail d’application logique Standard.":::

> [!NOTE]
> Si, après avoir créé votre connexion, vous souhaitez utiliser une autre connexion Azure Cosmos DB existante ou créer une connexion, sélectionnez **Modifier la connexion** dans l’éditeur des informations relatives au déclencheur ou à l’action.

---

## <a name="connector-reference"></a>Référence de connecteur

Pour accéder à des informations de référence sur les opérations de *connecteur managé* Azure Cosmos DB, par exemple, sur les déclencheurs, les actions et les limites, consultez la [page de référence du connecteur](/connectors/documentdb/).

Il n’existe aucune page de référence correspondante pour les opérations Azure Cosmos DB *intégrées*. Au lieu de cela, consultez le tableau suivant pour plus d’informations :

| Type | Nom | Paramètres |
|------|------|------------|
| Déclencheur | Lors de la création ou de la modification d’un élément | - **ID de base de données** : obligatoire. Nom de la base de données avec les conteneurs surveillés et de bail. <br>- **ID de conteneur surveillé** : obligatoire. Nom du conteneur surveillé. <br>- **ID de conteneur de baux** : obligatoire. Nom du conteneur utilisé pour stocker les baux. <br>- **Créer un conteneur de baux** : obligatoire. Si la valeur est true, créez le conteneur de baux s’il n’existe pas. <br>- **Débit du conteneur de baux** : facultatif. Nombre d’unités de requête à attribuer lors de la création du conteneur de baux. |
| Action | Créer ou mettre à jour un élément | - **ID de base de données** : obligatoire. Nom de la base de données. <br>- **ID de conteneur** : obligatoire. nom du conteneur. <br>- **Élément** : obligatoire. Élément à créer ou à mettre à jour. <br>- **Clé de partition** : obligatoire. Valeur de clé de partition pour l’élément demandé. <br>- **Est upsert** : facultatif. Si la valeur est true, remplacez l’élément s’il existe. Dans le cas contraire, créez-le. |
| Action | Créer ou mettre à jour un grand nombre d’éléments en bloc | Cette action est optimisée pour les scénarios à débit élevé, et implique un traitement supplémentaire avant l’envoi de vos éléments à créer dans le conteneur Azure Cosmos DB. Pour un grand nombre d’éléments, ce traitement supplémentaire accélère l’exécution de la requête. Pour un petit nombre d’éléments, cette surcharge peut ralentir les performances par rapport à l’utilisation de plusieurs actions de création d’élément. <p><p>- **ID de base de données** : obligatoire. Nom de la base de données. <br>- **ID de conteneur** : obligatoire. nom du conteneur. <br>- **Élément** : obligatoire. Tableau d’éléments à créer ou à mettre à jour. <br>- **Est upsert** : facultatif. Si la valeur est true, remplacez un élément s’il existe. Dans le cas contraire, créez-le. |
| Action | Lire un élément | - **ID de base de données** : obligatoire. Nom de la base de données. <br>- **ID de conteneur** : obligatoire. nom du conteneur. <br>- **ID d’élément** : obligatoire. Valeur `id` de l’élément demandé. <br>- **Clé de partition** : obligatoire. Valeur de clé de partition pour l’élément demandé. |
| Action | Supprimer un élément | - **ID de base de données** : obligatoire. Nom de la base de données. <br>- **ID de conteneur** : obligatoire. nom du conteneur. <br>- **ID d’élément** : obligatoire. Valeur `id` de l’élément demandé. <br>- **Clé de partition** : obligatoire. Valeur de clé de partition pour l’élément demandé. |
| Action | Éléments de requête | - **ID de base de données** : obligatoire. Nom de la base de données. <br>- **ID de conteneur** : obligatoire. nom du conteneur. <br>- **Requête SQL** : obligatoire. Texte de la requête SQL Azure Cosmos DB.  <br>- **Clé de partition** : facultatif. Valeur de clé de partition pour la requête, le cas échéant. <br>- **Jeton de continuation** : facultatif. Jeton de continuation pour cette requête, attribué par le service Azure Cosmos DB, le cas échéant. <br>- **Nombre maximal d’éléments** : facultatif. Nombre maximal d’éléments que la requête peut retourner. |
||||

## <a name="best-practices-for-azure-cosmos-db-built-in-operations"></a>Meilleures pratiques pour les opérations intégrées Azure Cosmos DB

### <a name="get-iterable-results-from-the-query-items-action-by-using-expressions"></a>Obtenir des résultats pouvant être itérés de l’action d’interrogation d’éléments à l’aide d’expressions

Le type de sortie de l’action intégrée **Interroger les éléments** dans un flux de travail d’**application logique (Standard)** est un objet ne pouvant pas être itéré. Pour obtenir les résultats de la requête sous la forme d’un objet pouvant être itéré en vue de leur traitement, procédez comme suit :

1. Sur le [Portail Azure](https://portal.azure.com), ouvrez votre flux de travail dans le Concepteur.

1. Si votre flux de travail est vide, ajoutez le déclencheur de votre choix.

   Cet exemple commence avec le [déclencheur **Récurrence**](connectors-native-recurrence.md).

1. Sous le déclencheur ou l’action à laquelle vous souhaitez ajouter l’action Azure Cosmos DB, sélectionnez **Insérer une nouvelle étape** ( **+** ) > **Ajouter une action**.

1. Dans le Concepteur, vérifiez que l’option **Ajouter une opération** est sélectionnée. Dans le volet **Ajouter une action** qui s’ouvre, sous la zone de recherche **Choisir une opération**, sélectionnez **Intégré** pour trouver les actions **Azure Cosmos DB**.

1. Dans la zone de recherche, entrez `Azure Cosmos DB`. Sélectionnez l’action **Interroger les éléments (préversion)** .

1. Si vous êtes invité à entrer les informations de connexion, [créez une connexion à votre compte Azure Cosmos DB](#connect-to-azure-cosmos-db).

1. Entrez les informations nécessaires pour l’action.

   | Propriétés | Obligatoire | Valeur | Description |
   |------------|----------|-------|-------------|
   | **ID de base de données** | Oui | <*database-ID*> | Base de données à laquelle vous souhaitez vous connecter. |
   | **ID de conteneur** | Oui | <*container-ID*> | Conteneur que vous souhaitez interroger. |
   | **Requête SQL** | Oui | <*sql-query*> | Requête SQL pour votre demande. |
   |||||

   L’illustration suivante montre un exemple d’action :

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-query-action.png" alt-text="Capture d’écran montrant le concepteur pour un flux de travail d’application logique Standard avec l’action Azure Cosmos DB « Interroger les éléments » et la configuration des paramètres.":::

1. Configurez tous les autres paramètres d’action en fonction des besoins.

1. Sous l’action, sélectionnez **Insérer une nouvelle étape** ( **+** ) > **Ajouter une action**. Dans le volet **Ajouter une action** qui s’ouvre, sous la zone de recherche **Choisir une opération**, sélectionnez **Intégré** pour trouver et ajouter l’action **Pour chaque**.

1. Pour convertir les sorties de l’action **Interroger les éléments**, procédez comme suit :

   1. Dans le volet de détail de l’action **Pour chaque**, cliquez dans la zone **Sélectionnez un résultat à partir des étapes précédentes** afin d’afficher la liste de contenu dynamique. Sélectionnez l’onglet **Expression** pour ouvrir l’éditeur d’expressions.

   1. Dans la liste de fonctions, recherchez la section **Fonctions de référencement**, puis sélectionnez la fonction **outputs(actionName)** qui apparaît dans la zone de l’éditeur d’expressions.

   1. Dans la zone de l’éditeur, entrez `'Query_items'` en tant que paramètre pour la fonction **outputs(actionName)** , suivi de `?['body']?['item']?['results']` pour accéder au tableau contenant les résultats de la requête, puis sélectionnez **OK**. Le format de l’expression est le suivant :

      `outputs('Query_items')?['body']?['item']?['results']`

      :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-query-configure.png" alt-text="Capture d’écran montrant le portail Azure et le concepteur pour un flux de travail d’application logique Standard avec l’action « Pour chaque » afin de traiter les résultats de la requête Azure Cosmos DB.":::

1. Dans la boucle **Pour chaque**, ajoutez toutes les autres actions souhaitées.

1. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

1. Testez votre application logique pour vous assurer que votre flux de travail retourne la sortie attendue.

### <a name="confirm-action-success-by-checking-the-operation-status-code"></a>Confirmer la réussite de l’action en vérifiant le code d’état de l’opération

Lorsque vous utilisez les actions intégrées Azure Cosmos DB dans un flux de travail d’**application logique (Standard)** , vous devez avoir une étape après chaque action, qui vérifie le code d’état retourné par l’appel de service au service Azure Cosmos DB. Ainsi, les actions suivantes du flux de travail peuvent réagir aux différents codes d’état que renvoie le service Azure Cosmos DB, en fonction de votre scénario.

> [!NOTE]
> Actuellement, contrairement à d’autres actions intégrées et de connecteur managé, le code d’état que renvoie une action Azure Cosmos DB diffère de celui que renvoie l’appel de service envoyé au service Azure Cosmos DB. Le code d’état de l’action est disponible dans la liste de contenu dynamique, tandis que le code d’état de l’appel de service est retourné et imbriqué dans le `body` de l’objet de réponse.
>
> Les étapes suivantes montrent comment accéder au code d’état de l’appel de service à l’aide d’une expression qui analyse le `body` de l’objet de réponse.

Par exemple, les étapes suivantes ont pour effet de créer un flux de travail qui utilise l’action **Lire un élément** et montre comment vérifier le code d’état que l’appel de service renvoie au service Azure Cosmos DB :

1. Sur le [Portail Azure](https://portal.azure.com), ouvrez votre flux de travail dans le Concepteur.

1. Si votre flux de travail est vide, ajoutez le déclencheur de votre choix.

   Cet exemple commence par le [déclencheur **Lors de la réception d’une requête HTTP**](connectors-native-reqres.md#) qui utilise une définition de schéma de base pour représenter le document que vous souhaitez lire.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-http-trigger.png" alt-text="Capture d’écran montrant le Concepteur pour un flux de travail d’application logique Standard avec le déclencheur Demande et la configuration des paramètres.":::

1. Sous le déclencheur ou l’action à laquelle vous souhaitez ajouter l’action Azure Cosmos DB, sélectionnez **Insérer une nouvelle étape** ( **+** ) > **Ajouter une action**.

1. Dans le Concepteur, vérifiez que l’option **Ajouter une opération** est sélectionnée. Dans le volet **Ajouter une action** qui s’ouvre, sous la zone de recherche **Choisir une opération**, sélectionnez **Intégré** pour trouver les actions **Azure Cosmos DB**.

1. Dans la zone de recherche, entrez `Azure Cosmos DB`. Sélectionnez l’action **Interroger les éléments (préversion)** .

1. Si vous êtes invité à entrer les informations de connexion, [créez une connexion à votre compte Azure Cosmos DB](#connect-to-azure-cosmos-db).

1. Entrez les informations nécessaires pour l’action.

   | Propriétés | Obligatoire | Valeur | Description |
   |------------|----------|-------|-------------|
   | **ID de base de données** | Oui | <*database-ID*> | Base de données à laquelle vous souhaitez vous connecter. |
   | **ID de conteneur** | Oui | <*container-ID*> | Conteneur que vous souhaitez interroger. |
   | **Item Id** | Oui | <*item-ID*> | Valeur `id` du document que vous souhaitez lire. |
   | **Clé de partition** | Oui | <*partition-key*> | Valeur de clé de partition pour le document que vous souhaitez lire. |
   |||||

   L’illustration suivante montre un exemple d’action :

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-read-configure.png" alt-text="Capture d’écran montrant le concepteur pour un flux de travail d’application logique Standard avec l’action Azure Cosmos DB « Lire un élément » et la configuration des paramètres.":::

1. Configurez tous les autres paramètres d’action en fonction des besoins.

1. Sous l’action, sélectionnez **Insérer une nouvelle étape** ( **+** ) > **Ajouter une action**. Dans le volet **Ajouter une action** qui s’ouvre, sous la zone de recherche **Choisir une opération**, sélectionnez **Intégré** pour trouver et ajouter l’action **Condition**.

1. Cliquez dans la partie la plus à gauche de la zone **Choisir une valeur** afin d’afficher la liste de contenu dynamique. Sélectionnez l’onglet **Expression** pour ouvrir l’éditeur d’expressions.

1. Dans la liste de fonctions, recherchez la section **Fonctions de référencement**, puis sélectionnez la fonction **outputs(actionName)** qui apparaît dans la zone de l’éditeur d’expressions.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-condition-configure.png" alt-text="Capture d’écran montrant le concepteur pour un flux de travail d’application logique Standard avec une configuration de condition.":::

1. Dans la zone de l’éditeur d’expressions, entrez `'Read_an_item'` en tant que paramètre pour la fonction **outputs(actionName)** , suivi de `?['body']?['statusCode']`, puis sélectionnez **OK**.

   Cette expression accède au code d’état retourné par l’appel de service qui exécute l’opération de lecture sur Azure Cosmos DB en analysant le contenu `body` dans l’objet de réponse. Le format de l’expression est le suivant :

   `outputs('Read_an_item')?['body']?['statusCode']`

   > [!NOTE]
   > N’oubliez pas que le code d’état retourné à partir d’une action Azure Cosmos DB diffère de celui retourné par l’appel réellement envoyé à Azure Cosmos DB. Le code d’état de l’action est disponible dans la liste de contenu dynamique, tandis que le code d’état de l’appel de service est retourné et imbriqué dans le `body` de l’objet de réponse.

1. Dans la zone centrale de la condition et la zone **Choisir une valeur** à droite, spécifiez les valeurs appropriées pour votre flux de travail. Dans cet exemple, la condition vérifie si le code d’état est `OK`.

   > [!NOTE]
   > Les codes d’état sont représentés sous forme de texte, non de nombres.

1. Dans les chemins d’accès **True** et **False** de la condition, ajoutez toutes les autres actions correspondant à votre scénario.

1. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

1. Testez votre application logique pour vous assurer que votre flux de travail retourne la sortie attendue.

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble des connecteurs pour Azure Logic Apps](apis-list.md)
