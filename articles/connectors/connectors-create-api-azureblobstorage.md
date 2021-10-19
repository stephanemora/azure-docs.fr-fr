---
title: Se connecter à Stockage Blob Azure
description: Créer et gérer des objets Blob dans les comptes Stockage Azure avec Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/11/2021
tags: connectors
ms.openlocfilehash: cc56c079173fad1509d9da9cf1d435675b1918f7
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808983"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Créer et gérer des objets Blob dans Stockage Blob Azure avec Azure Logic Apps

Au sein de votre flux de travail dans Azure Logic Apps, vous pouvez accéder aux fichiers stockés sous forme d’objet blobs dans votre compte de stockage Azure et les gérer à l’aide du [connecteur Stockage Blob Azure](/connectors/azureblobconnector/). Ce connecteur fournit des déclencheurs et des actions que votre flux de travail peut utiliser pour les opérations sur les objets blob. Vous pouvez ensuite automatiser des tâches de gestion des fichiers dans votre compte de stockage. Il existe différentes [actions de connecteur](/connectors/azureblobconnector/#actions), par exemple la vérification, la suppression, la lecture et le chargement d’objets blob. Le [déclencheur disponible ](/connectors/azureblobconnector/#triggers) s’active lorsqu’un objet blob est ajouté ou modifié.

Vous pouvez vous connecter au Stockage Blob à partir des types de ressources **Application logique (Consommation)** et **Application logique (Standard)** . Le connecteur peut être utilisé avec des flux de travail d’application logique dans un environnement Azure Logic Apps multilocataire, un environnement Azure Logic Apps monolocataire et l’environnement de service d’intégration (ISE, Integration Service Environment). Avec **Application logique (Standard)** , le Stockage Blob fournit des opérations intégrées *et* des opérations de connecteur géré.

> [!IMPORTANT]
> Un flux de travail d’application logique ne peut pas accéder directement à un compte de stockage situé derrière un pare-feu s’ils se trouvent tous deux dans la même région. Pour contourner ce problème, vous pouvez placer votre application logique et votre compte de stockage dans des régions différentes. Pour savoir comment autoriser l’accès à des comptes de stockage situés derrière des pare-feu à partir d’Azure Logic Apps, consultez la section [Accès à des comptes de stockage situés derrière des pare-feu](#access-storage-accounts-behind-firewalls) plus loin dans cette rubrique.

## <a name="prerequisites"></a>Prérequis

- Un compte et un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Un [compte de stockage Azure et un conteneur de stockage](../storage/blobs/storage-quickstart-blobs-portal.md)

- Un workflow d’application logique à partir duquel vous voulez accéder à votre compte Stockage Blob. Si vous souhaitez commencer votre flux de travail par un déclencheur Stockage Blob, il vous faut un [flux de travail d’application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="limits"></a>Limites

- Dans le cas des flux de travail d’applications logiques qui s’exécutent dans un [environnement de service d’intégration](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la version de ce connecteur qui comporte l’étiquette ISE applique à la place les [limites de messages de l’ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits).

- Par défaut, les actions de Stockage Blob peuvent lire ou écrire des fichiers dont la taille est *inférieure ou égale à 50 Mo*. Pour gérer les fichiers d’une taille supérieure à 50 Mo, mais n’excédant pas 1024 Mo, les actions de Stockage Blob prennent en charge la [segmentation du message](../logic-apps/logic-apps-handle-large-messages.md). L’[action **Obtenir le contenu de l’objet blob**](/connectors/azureblobconnector/#get-blob-content) utilise implicitement la segmentation.

- Les déclencheurs du Stockage Blob ne prennent pas en charge la segmentation. Quand ils demandent du contenu de fichiers, les déclencheurs sélectionnent uniquement des fichiers dont la taille est inférieure ou égale à 50 Mo. Pour obtenir des fichiers supérieurs à 50 Mo, suivez ce modèle :

  - Utilisez un déclencheur Stockage Blob qui retourne des propriétés de fichier, comme [**Quand un blob est ajouté ou modifié (propriétés uniquement)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)).

  - Suivez le déclencheur avec l’[action Stockage Blob **Obtenir le contenu de l’objet blob**](/connectors/azureblobconnector/#get-blob-content), qui lit le fichier complet et utilise implicitement la segmentation.

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques sur ce connecteur (déclencheurs, actions, limites, etc.), consultez la [page de référence du connecteur](/connectors/azureblobconnector/). Si vous ne souhaitez pas avoir recours au connecteur Stockage Blob, vous pouvez [utiliser à la place le déclencheur ou l’action HTTP avec une identité managée pour les opérations sur les objets blob](#access-blob-storage-with-managed-identities).

## <a name="add-blob-storage-trigger"></a>Ajouter un déclencheur de stockage d’objets blob

Dans Azure Logic Apps, chaque flux de travail doit commencer par un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou qu’une condition particulière est remplie.

Ce connecteur dispose d’un déclencheur, appelé soit [**Quand un blob est ajouté ou modifié dans le Stockage Azure** soit **Quand un blob est ajouté ou modifié (propriétés uniquement)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)). Le déclencheur s’active lorsque les propriétés d’un objet blob sont ajoutées ou mises à jour dans votre conteneur de stockage. Chaque fois, le moteur Azure Logic Apps crée une instance d’application logique et lance l’exécution de votre flux de travail.

### <a name="consumption"></a>[Consommation](#tab/consumption)

Pour ajouter un déclencheur Stockage Blob Azure à un flux de travail d’application logique dans un environnement Azure Logic Apps multilocataire, procédez comme suit :

1. Sur le [Portail Azure](https://portal.azure.com), ouvrez votre flux de travail d’application logique dans le Concepteur.

1. Dans la zone de recherche du Concepteur, entrez le filtre `Azure blob`. Dans la liste des déclencheurs, sélectionnez le déclencheur nommé **Quand un objet blob est ajouté ou modifié (propriétés uniquement)** .

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-add.png" alt-text="Capture d’écran montrant le Portail Azure et le Concepteur de flux de travail avec une application logique Consommation et le déclencheur nommé « Quand un objet blob est ajouté ou modifié (propriétés uniquement) » sélectionnés.":::

1. Si vous êtes invité à entrer les informations de connexion, [créez votre connexion Stockage Blob Azure maintenant](#connect-blob-storage-account).

1. Entrez les informations nécessaires pour le déclencheur.

   1. Pour la valeur de propriété **Conteneur**, sélectionnez l’icône en forme de dossier afin de rechercher votre conteneur Stockage Blob. Ou entrez le chemin d’accès manuellement.

   1. Configurez d’autres paramètres de déclenchement au besoin.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-configure.png" alt-text="Capture d’écran montrant le déclencheur Blob Azure avec la configuration des paramètres.":::

1. Ajoutez une ou plusieurs actions à votre workflow.

1. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer** pour enregistrer vos modifications.

### <a name="standard"></a>[Standard](#tab/standard)

Pour ajouter un déclencheur Blob Azure à un flux de travail d’application logique dans un environnement Azure Logic Apps monolocataire, procédez comme suit :

1. Sur le [Portail Azure](https://portal.azure.com), ouvrez votre flux de travail d’application logique dans le Concepteur.

1. Dans le concepteur, sélectionnez **Choisir une opération**. Dans la zone de recherche **Choisir une opération** du volet **Ajouter un déclencheur** qui s’ouvre, sélectionnez soit **Intégré** pour trouver le déclencheur *intégré***Blob Azure**, soit **Azure** pour trouver le déclencheur *connecteur géré* **Stockage Blob Azure**.

1. Dans la zone de recherche, entrez `Azure blob`. Dans la liste des déclencheurs, sélectionnez le déclencheur nommé **Quand un blob est ajouté ou modifié dans le Stockage Azure**.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-add.png" alt-text="Capture d’écran montrant le Portail Azure, le Concepteur de flux de travail, le flux de travail d’application logique Standard et le déclencheur Blob Azure sélectionnés.":::

1. Si vous êtes invité à entrer les informations de connexion, [créez votre connexion Stockage Blob Azure maintenant](#connect-blob-storage-account).

1. Entrez les informations nécessaires pour le déclencheur. Dans l’onglet **Paramètres**, ajoutez le **Chemin de l’objet blob** dont vous voulez effectuer le monitoring.

   1. Pour rechercher le chemin d’accès à votre objet blob, ouvrez votre compte de stockage dans le portail Azure.

   1. Dans le menu de navigation, sous **Stockage de données**, sélectionnez **Conteneurs**.

   1. Sélectionnez votre conteneur blob. Dans le menu de navigation du conteneur, sous **Paramètres**, sélectionnez **Propriétés**.

   1. Copiez la valeur **URL**, qui est le chemin d’accès à l’objet blob. Le chemin d’accès ressemble à `https://<storage-container-name>/<folder-name>/{name}`. Indiquez à la place le nom du conteneur et du dossier, mais conservez la chaîne littérale `{name}`.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-configure.png" alt-text="Capture d’écran montrant le Concepteur de flux de travail pour un flux de travail d’application logique Standard avec un déclencheur Stockage Blob et la configuration des paramètres.":::

1. Continuez à créer votre flux de travail en ajoutant une ou plusieurs actions.

1. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer** pour enregistrer vos modifications.

---

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Ajouter une action de stockage d’objets Blob

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre flux de travail qui suit un déclencheur ou une autre action.

### <a name="consumption"></a>[Consommation](#tab/consumption)

Pour ajouter une action Stockage Blob Azure à un flux de travail d’application logique dans un environnement Azure Logic Apps multilocataire, procédez comme suit :

1. Sur le [Portail Azure](https://portal.azure.com), ouvrez votre flux de travail dans le Concepteur.

1. Si votre flux de travail est vide, ajoutez le déclencheur de votre choix.

   Cet exemple commence avec le [déclencheur **Récurrence**](connectors-native-recurrence.md).

1. Sous le déclencheur ou l’action à laquelle vous souhaitez ajouter l’action Stockage Blob, sélectionnez **Nouvelle étape** ou **Ajouter une action** s’il y a des étapes avant et après.

1. Dans la zone de recherche du Concepteur, entrez `Azure blob`. Sélectionnez l’action Stockage Blob que vous souhaitez utiliser.

   Dans cet exemple, il s’agit de l’action nommée **Obtenir le contenu de l’objet blob**.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-add.png" alt-text="Capture d’écran de l’application logique de consommation dans le concepteur, montrant la liste des actions de Stockage d’objets blob disponibles.":::

1. Si vous êtes invité à entrer les informations de connexion, [créez une connexion à votre compte de stockage d’objets blob](#connect-blob-storage-account).

1. Entrez les informations nécessaires pour l’action.

    1. Pour la valeur de propriété **Blob**, sélectionnez l’icône en forme de dossier afin de rechercher votre conteneur Stockage Blob. Ou entrez le chemin d’accès manuellement.

       :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-configure.png" alt-text="Capture d’écran de l’application logique de consommation dans le concepteur, montrant la configuration de l’action Stockage d’objets blob.":::

    1. Configurez d’autres paramètres d’action au besoin.

### <a name="standard"></a>[Standard](#tab/standard)

Pour ajouter une action Blob Azure à un flux de travail d’application logique dans un environnement Azure Logic Apps monolocataire, procédez comme suit :

1. Sur le [Portail Azure](https://portal.azure.com), ouvrez votre flux de travail dans le Concepteur.

1. Si votre flux de travail est vide, ajoutez le déclencheur de votre choix.

   Cet exemple commence avec le [déclencheur **Récurrence**](connectors-native-recurrence.md).

1. Sous le déclencheur ou l’action à laquelle vous souhaitez ajouter l’action Stockage Blob, sélectionnez **Insérer une nouvelle étape** ( **+** ) > **Ajouter une action**.

1. Dans le Concepteur, vérifiez que l’option **Ajouter une opération** est sélectionnée. Dans la zone de recherche **Choisir une opération** du volet **Ajouter une action** qui s’ouvre, sélectionnez soit **Intégré** pour trouver les actions *intégrées***Blob Azure**, soit **Azure** pour trouver les actions *connecteur géré* **Stockage Blob Azure**.

1. Dans la zone de recherche, entrez `Azure blob`. Sélectionnez l’action Blob Azure que vous souhaitez utiliser.

   Dans cet exemple, il s’agit de l’action nommée **Lire le contenu de l’objet blob issu du Stockage Azure**, qui lit uniquement le contenu de l’objet blob. Pour afficher ultérieurement le contenu, ajoutez une autre action qui crée un fichier comportant le contenu de l’objet blob à l’aide d’un autre connecteur. Par exemple, vous pouvez insérer une action OneDrive qui crée un fichier basé sur le contenu de l’objet blob.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-add.png" alt-text="Capture d’écran montrant le Portail Azure et le Concepteur de flux de travail avec un flux de travail d’application logique Standard et les actions Stockage Blob Azure disponibles.":::

1. Si vous êtes invité à entrer les informations de connexion, [créez une connexion à votre compte de stockage d’objets blob](#connect-blob-storage-account).

1. Entrez les informations nécessaires pour l’action.

    1. Pour **Nom du conteneur**, entrez le chemin du conteneur Stockage Blob que vous voulez utiliser.

    1. Pour la valeur de propriété **Nom de l’objet blob**, saisissez le chemin de l’objet blob que vous souhaitez utiliser.

       :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-configure.png" alt-text="Capture d’écran de l’application logique Standard dans le concepteur, montrant la sélection du déclencheur stockage blob.":::

    1. Configurez d’autres paramètres d’action au besoin.

1. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

1. Testez votre application logique pour vous assurer que votre conteneur sélectionné contient un objet blob.

---

<a name="connect-blob-storage-account"></a>

## <a name="connect-to-blob-storage-account"></a>Connexion à un compte Stockage Blob

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

### <a name="consumption"></a>[Consommation](#tab/consumption)

Pour pouvoir configurer votre [déclencheur Stockage Blob Azure](#add-blob-storage-trigger) ou votre [action Stockage Blob Azure](#add-blob-storage-action), vous devez vous connecter à un compte de stockage. Une connexion exige les propriétés suivantes :

| Propriété | Obligatoire | Valeur | Description |
|----------|----------|-------|-------------|
| **Nom de connexion** | Oui | <*connection-name*> | Nom à utiliser pour votre connexion. |
| **Compte de stockage** | Oui | <*storage-account*> | Sélectionnez votre compte de stockage dans la liste ou fournissez une chaîne. <p>**Remarque** : Pour trouver la chaîne de connexion, accédez à la page du compte de stockage. Dans le menu de navigation, sélectionnez **Clés d’accès** > **Afficher les clés** sous **Sécurité + mise en réseau**. Copiez l’une des valeurs de chaîne de connexion disponibles. |
|||||

Pour créer une connexion Stockage Blob Azure à partir d’un flux de travail d’application logique dans un environnement Azure Logic Apps multilocataire, procédez comme suit :

1. Pour **Nom de la connexion**, donnez un nom à votre connexion.

1. Pour **Compte de stockage**, sélectionnez le compte de stockage dans lequel se trouve votre conteneur d’objets blob. Ou bien, sélectionnez **Entrer manuellement les informations de connexion** pour fournir vous-même le chemin.

1. Sélectionnez **Créer** pour établir votre connexion.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-connection-create.png" alt-text="Capture d’écran montrant le Concepteur de flux de travail avec un flux de travail d’application logique Consommation et un message invitant à ajouter une nouvelle connexion pour l’étape Stockage Blob Azure.":::

> [!NOTE]
> Si, après avoir créé votre connexion, vous souhaitez utiliser une autre connexion Stockage Blob Azure existante, sélectionnez **Changer de connexion** dans l’éditeur des informations relatives au déclencheur ou à l’action.

Si vous rencontrez des problèmes de connexion à votre compte de stockage, consultez [Guide pratique pour accéder à des comptes de stockage situés derrière des pare-feu](#access-storage-accounts-behind-firewalls).

### <a name="standard"></a>[Standard](#tab/standard)

Pour pouvoir configurer votre [déclencheur Blob Azure](#add-blob-storage-trigger) ou votre [action Blob Azure](#add-blob-storage-action), vous devez vous connecter à un compte de stockage. Une connexion exige les propriétés suivantes :

| Propriété | Obligatoire | Valeur | Description |
|----------|----------|-------|-------------|
| **Nom de connexion** | Oui | <*connection-name*> | Nom à utiliser pour votre connexion. |
| **Chaîne de connexion de stockage d’objets blob Azure** | Oui | <*storage-account*> | Sélectionnez votre compte de stockage dans la liste ou fournissez une chaîne. <p>**Remarque** : Pour trouver la chaîne de connexion, accédez à la page du compte de stockage. Dans le menu de navigation, sélectionnez **Clés d’accès** > **Afficher les clés** sous **Sécurité + mise en réseau**. Copiez l’une des valeurs de chaîne de connexion disponibles. |
|||||

Pour créer une connexion Stockage Blob Azure à partir d’un flux de travail d’application logique dans un environnement Azure Logic Apps monolocataire, procédez comme suit :

1. Pour **Nom de la connexion**, entrez un nom pour votre connexion.

1. Pour **Chaîne de connexion Stockage Blob Azure**, entrez la chaîne de connexion du compte de stockage que vous souhaitez utiliser.

1. Sélectionnez **Créer** pour établir votre connexion.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-connection-create.png" alt-text="Capture d’écran montrant le Concepteur de flux de travail avec un flux de travail d’application logique Standard et un message invitant à ajouter une nouvelle connexion pour l’étape Stockage Blob Azure.":::

> [!NOTE]
> Si, après avoir créé votre connexion, vous souhaitez utiliser une autre connexion Stockage Blob Azure existante, sélectionnez **Changer de connexion** dans l’éditeur des informations relatives au déclencheur ou à l’action.

Si vous rencontrez des problèmes de connexion à votre compte de stockage, consultez [Guide pratique pour accéder à des comptes de stockage situés derrière des pare-feu](#access-storage-accounts-behind-firewalls).

---

## <a name="access-storage-accounts-behind-firewalls"></a>Accéder à des comptes de stockage derrière des pare-feu

Vous pouvez ajouter la sécurité réseau à un compte de stockage Azure en restreignant l’accès avec un [pare-feu et des règles de pare-feu](../storage/common/storage-network-security.md). Toutefois, cette configuration crée une difficulté pour Azure et d’autres services Microsoft qui ont besoin d’accéder au compte de stockage. La communication locale dans le centre de données fait abstraction des adresses IP internes, vous ne pouvez donc pas configurer de règles de pare-feu avec des restrictions d’adresse IP.

pour accéder aux comptes de stockage derrière des Pare-feu à l’aide du connecteur Stockage Blob :

- [Accéder aux comptes de stockage dans d’autres régions](#access-storage-accounts-in-other-regions)
- [Accéder aux comptes de stockage via un réseau virtuel approuvé](#access-storage-accounts-through-trusted-virtual-network)

Autres solutions pour accéder aux comptes de stockage derrière des pare-feu :

- [Accéder aux comptes de stockage en tant que service approuvé avec des identités gérées](#access-blob-storage-with-managed-identities)
- [Accéder aux comptes de stockage par le biais de la gestion des API Azure](#access-storage-accounts-through-azure-api-management)

### <a name="access-storage-accounts-in-other-regions"></a>Accéder aux comptes de stockage dans d’autres régions

Les flux de travail d’applications logiques ne peuvent pas accéder directement aux comptes de stockage situés derrière des pare-feu s’ils se trouvent tous deux dans la même région. Pour contourner ce problème, placez vos ressources d’applications logiques dans une région différente de celle de votre compte de stockage. Ensuite, donnez accès aux [adresses IP sortantes pour les connecteurs managés dans votre région](/connectors/common/outbound-ip-addresses#azure-logic-apps).

> [!NOTE]
> Cette solution ne s’applique pas aux connecteurs Stockage Table Azure et Stockage File d’attente Azure. À la place, pour accéder à votre connecteur Stockage Table ou Stockage File d’attente, [utilisez le déclencheur et les actions HTTP intégrés](../logic-apps/logic-apps-http-endpoint.md).

Pour ajouter vos adresses IP sortantes au pare-feu du compte de stockage, procédez comme suit :

1. Notez les [adresses IP sortantes du connecteur géré](/connectors/common/outbound-ip-addresses#azure-logic-apps) dans la région de votre ressource d’application logique.

1. Sur le [Portail Azure](https://portal.azure.com), trouvez et ouvrez votre ressource de compte de stockage.

1. Dans le menu de navigation du compte de stockage, sélectionnez **Mise en réseau** sous **Sécurité + mise en réseau**.

   1. Sous **Autoriser l’accès à partir de**, sélectionnez **Réseaux sélectionnés** pour afficher les paramètres concernés.

   1. Sous **Pare-feu**, ajoutez les adresses ou plages d’adresses IP qui doivent être accessibles. Si vous devez accéder au compte de stockage à partir de votre ordinateur, sélectionnez **Ajouter l’adresse IP de votre client**.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-ip-configure.png" alt-text="Capture d’écran de la page Mise en réseau du compte de stockage d’objets blob dans le portail Azure, montrant les paramètres du pare-feu pour ajouter des adresses IP et des plages à la liste d’autorisation.":::

   1. Quand vous avez terminé, sélectionnez **Enregistrer**.

### <a name="access-storage-accounts-through-trusted-virtual-network"></a>Accéder aux comptes de stockage via un réseau virtuel approuvé

Vous pouvez placer le compte de stockage dans un réseau virtuel Azure que vous gérez, puis ajouter ce réseau virtuel à la liste des réseaux virtuels approuvés. Pour que votre application logique accède au compte de stockage via un [réseau virtuel approuvé](../virtual-network/virtual-networks-overview.md), vous devez déployer cette application logique sur un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), qui peut se connecter aux ressources d’un réseau virtuel. Vous pouvez ensuite ajouter les sous-réseaux de cet ISE à la liste approuvée. Les connecteurs de stockage Azure, tels que le connecteur Stockage Blob, peuvent accéder directement au conteneur de stockage. Cette configuration est la même que l’utilisation de points de terminaison de service à partir d’un ISE.

### <a name="access-storage-accounts-through-azure-api-management"></a>Accéder aux comptes de stockage par le biais de la gestion des API Azure

Si vous utilisez un niveau dédié pour la [Gestion des API](../api-management/api-management-key-concepts.md), vous pouvez avoir recours à l’API de stockage en utilisant le service Gestion des API et en autorisant les adresses IP de ce dernier via le pare-feu. En gros, ajoutez le réseau virtuel Azure utilisé par le service Gestion des API au paramètre de pare-feu du compte de stockage. Vous pouvez ensuite utiliser l’action Gestion des API ou l’action HTTP pour appeler les API Stockage Azure. Cela dit, si vous choisissez cette option, vous devez gérer vous-même le processus d’authentification. Pour plus d’informations, consultez [Architecture d’intégration d’entreprise simple](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration).

## <a name="access-blob-storage-with-managed-identities"></a>Accéder au Stockage Blob avec des identités managées

Si vous voulez accéder au Stockage Blob sans utiliser ce connecteur, vous pouvez utiliser à la place des [identités managées pour l’authentification](../active-directory/managed-identities-azure-resources/overview.md). Vous pouvez créer une exception qui donne aux services de confiance Microsoft, comme une identité managée, l’accès à votre compte de stockage via un pare-feu.

Pour utiliser des identités managées dans votre application logique afin d’accéder au Stockage Blob, procédez comme suit :

1. [Configurez l’accès à votre compte de stockage](#configure-storage-account-access).

1. [Créez une attribution de rôle pour votre application logique](#create-role-assignment-logic-app).

1. [Activez la prise en charge de l’identité managée dans votre application logique](#enable-managed-identity-support).

> [!NOTE]
> Limitations de cette solution :
>
> - Vous pouvez *uniquement* utiliser le déclencheur ou l’action HTTP dans votre workflow.
> - Vous devez configurer une identité managée pour authentifier la connexion de votre compte de stockage.
> - Vous ne pouvez pas utiliser d’opérations de Stockage Blob intégrées si vous vous authentifiez avec une identité managée.
> - Pour les applications logiques dans un environnement à locataire unique, seule l’identité managée affectée par le système est disponible et prise en charge, et non l’identité managée affectée par l’utilisateur.

### <a name="configure-storage-account-access"></a>Configurer l’accès du compte de stockage

Pour configurer l’exception et la prise en charge de l’identité managée, configurez tout d’abord l’accès approprié à votre compte de stockage :

1. Sur le [Portail Azure](https://portal.azure.com), trouvez et ouvrez votre ressource de compte de stockage.

1. Dans le menu de navigation du compte de stockage, sélectionnez **Mise en réseau** sous **Sécurité + mise en réseau**.

   1. Sous **Autoriser l’accès à partir de**, sélectionnez **Réseaux sélectionnés** pour afficher les paramètres concernés.

   1. Si vous devez accéder au compte de stockage à partir de votre ordinateur, sélectionnez **Ajouter l’adresse IP de votre client** sous **Pare-feu**.

   1. Sous **Exception**, sélectionnez **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage**.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-networking-configure.png" alt-text="Capture d’écran montrant le Portail Azure et le volet Mise en réseau du compte Stockage Blob avec les paramètres Autoriser.":::

   1. Quand vous avez terminé, sélectionnez **Enregistrer**.

> [!NOTE]
> Si vous recevez une erreur **403 Forbidden** lorsque vous essayez de vous connecter au compte de stockage à partir de votre flux de travail, il y a plusieurs causes possibles. Essayez la résolution suivante avant de passer aux étapes supplémentaires. Tout d’abord, désactivez le paramètre **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** et enregistrez vos modifications. Ensuite, réactivez le paramètre et enregistrez à nouveau vos modifications.

<a name="create-role-assignment-logic-app"></a>

### <a name="create-role-assignment-for-logic-app"></a>Créer une attribution de rôle pour l’application logique

Ensuite, [activez la prise en charge de l’identité managée](../logic-apps/create-managed-service-identity.md) sur votre ressource d’application logique.

Les étapes suivantes sont identiques pour les applications logiques Consommation dans les environnements multilocataires et les applications logiques Standard dans les environnements monolocataires.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre ressource d’application logique.

1. Dans le menu de navigation de la ressource d’application logique, sélectionnez **Identité** sous **Paramètres**.

1. Dans le volet **Système affecté**, définissez **État** sur **Activé** si ce n’est pas déjà le cas. Sous **Autorisations**, sélectionnez **Attributions de rôles Azure**.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-1.png" alt-text="Capture d’écran montrant le Portail Azure et le menu de la ressource d’application logique avec le volet Paramètres « Identité » et le bouton « Autorisations d’attributions de rôles Azure ».":::

1. Sur le volet **Attributions de rôle Azure**, sélectionnez **Ajouter une attribution de rôle**.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-2.png" alt-text="Capture d’écran montrant le volet d’attributions de rôles d’application logique, avec l’abonnement sélectionné et le bouton permettant d’ajouter une nouvelle attribution de rôle.":::

1. Dans le volet **Ajouter des attributions de rôles**, configurez la nouvelle attribution de rôle en procédant comme suit :

   1. Pour **Étendue**, sélectionnez **Stockage**.

   1. Pour **Abonnement**, sélectionnez l’abonnement de votre compte de stockage.

   1. Pour **Ressource**, sélectionnez le compte de stockage auquel vous souhaitez accéder à partir de votre flux de travail d’application logique.

   1. Pour **Rôle**, sélectionnez les autorisations appropriées pour votre scénario.

      Cet exemple utilise **Contributeur aux données Blob du stockage**, qui autorise l’accès en lecture, écriture et suppression aux conteneurs d’objets blob et à la date. Pour voir le détail des autorisations, déplacez votre souris sur l’icône d’informations située à côté d’un rôle dans le menu déroulant.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-configure.png" alt-text="Capture d’écran du volet de configuration de l’attribution de rôle, qui présente les paramètres de l’étendue, de l’abonnement, de la ressource et du rôle.":::

   1. Sélectionnez **Enregistrer** pour terminer la création de l’attribution de rôle.

<a name="enable-managed-identity-support"></a>

### <a name="enable-managed-identity-support-on-logic-app"></a>Activation de la prise en charge de l’identité managée sur l’application logique

Ensuite, ajoutez un [déclencheur ou une action HTTP](connectors-native-http.md) dans votre workflow. Assurez-vous de [définir le type d’authentification pour utiliser l’identité managée](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity).

Les étapes suivantes sont identiques pour les applications logiques Consommation dans les environnements multilocataires et les applications logiques Standard dans les environnements monolocataires.

1. Ouvrez votre flux de travail d’application logique dans le Concepteur.

1. En fonction de votre scénario, ajoutez un déclencheur ou une action **HTTP** à votre flux de travail. Configurez les valeurs des paramètres requis.

   1. Sélectionnez une **Méthode** pour votre demande. Dans cet exemple, il s’agit de la méthode HTTP **PUT**.

   1. Entrez l’**URI** de votre objet blob. Le chemin d’accès ressemble à `https://<storage-container-name>/<folder-name>/{name}`. Indiquez à la place le nom du conteneur et du dossier, mais conservez la chaîne littérale `{name}`.

   1. Sous **En-têtes**, ajoutez les éléments suivants :

      - L’en-tête de type d’objet blob `x-ms-blob-type` avec la valeur `BlockBlob`

      - L’en-tête de version de l’API `x-ms-version` avec la valeur appropriée

      Pour plus d’informations, consultez [Authentifier l’accès avec une identité managée](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) et [Gestion des versions pour les services Stockage Azure](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-connect.png" alt-text="Capture d’écran montrant le Concepteur de flux de travail et le déclencheur ou l’action HTTP avec les paramètres « PUT » requis.":::

1. Dans la liste **Ajouter un nouveau paramètre**, sélectionnez **Authentification** pour [configurer l’identité managée](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity).

    1. Sous **Authentification**, sélectionnez **Identité managée** comme **Type d’authentification**.

    1. Pour la propriété **Identité managée**, sélectionnez **Identité managée affectée par le système**.

    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-authenticate.png" alt-text="Capture d’écran montrant le Concepteur de flux de travail et l’action HTTP avec les paramètres d’authentification Identité managée.":::

1. Lorsque c’est chose faite, dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

Vous pouvez maintenant appeler l’[API REST du service Blob](/rest/api/storageservices/blob-service-rest-api) pour exécuter toutes les opérations de stockage nécessaires.

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble des connecteurs pour Azure Logic Apps](apis-list.md)