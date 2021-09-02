---
title: Se connecter à Stockage Blob Azure
description: Créer et gérer des objets Blob dans les comptes Stockage Azure avec Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 06/23/2021
tags: connectors
ms.openlocfilehash: e3d8730976d623b5304a50341bcf257f35047e25
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113359548"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Créer et gérer des objets Blob dans Stockage Blob Azure avec Azure Logic Apps

Vous pouvez accéder aux fichiers stockés sous forme d’objet blobs dans votre compte de stockage Azure et les gérer dans Azure Logic Apps à l’aide du [connecteur Stockage Blob Azure](/connectors/azureblobconnector/). Ce connecteur fournit des déclencheurs et des actions pour les opérations d’objet blob au sein de vos workflows d’application logique. Vous pouvez utiliser ces opérations pour automatiser les tâches et les workflows de gestion des fichiers dans votre compte de stockage. Les [actions de connecteur disponibles](/connectors/azureblobconnector/#actions) comprennent la vérification, la suppression, la lecture et le téléchargement d’objets blob. Le [déclencheur disponible ](/connectors/azureblobconnector/#triggers) s’active lorsqu’un objet blob est ajouté ou modifié. 

Vous pouvez vous connecter au Stockage Blob à partir de types de ressources d’application logique standard et de consommation. Vous pouvez utiliser le connecteur avec des applications logiques dans un environnement monolocataire, mutualisé ou de service d’intégration (ISE). Pour les applications logiques dans un environnement monolocataire, les objets Stockage Blob fournissent des opérations intégrées, ainsi que des opérations de connecteur managées.

> [!NOTE]
> Pour les applications logiques utilisées dans un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la version de ce connecteur avec l’étiquette ISE applique les [limites de messages de l’ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) à la place.

Pour plus d’informations techniques sur ce connecteur, comme les déclencheurs, les actions et les limites, consultez la [page de référence du connecteur](/connectors/azureblobconnector/).

Vous pouvez également [utiliser une identité managée avec un déclencheur ou une action HTTP pour effectuer des opérations sur les blocs](#access-blob-storage-with-managed-identities) au lieu du connecteur Stockage Blob.

> [!IMPORTANT]
> Les applications logiques n’ont pas directement accès aux comptes de stockage qui sont derrière des pare-feu dans la même région qu’elles. Pour contourner ce problème, placez vos applications logiques et votre compte de stockage dans des régions différentes. Pour plus d’informations sur l’activation de l’accès à partir d’Azure Logic Apps aux comptes de stockage derrière des pare-feu, consultez la section [Accéder aux comptes de stockage derrière des pare-feu](#access-storage-accounts-behind-firewalls) plus loin dans cette rubrique.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).
- Un [compte de stockage Azure et un conteneur de stockage](../storage/blobs/storage-quickstart-blobs-portal.md)
- Un workflow d’application logique à partir duquel vous voulez accéder à votre compte Stockage Blob. Si vous souhaitez démarrer votre application logique avec un déclencheur Stockage Blob, vous avez besoin d’une [application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="limits"></a>Limites

- Par défaut, les actions de Stockage Blob peuvent lire ou écrire des fichiers dont la taille est *inférieure ou égale à 50 Mo*. Pour gérer les fichiers d’une taille supérieure à 50 Mo, mais n’excédant pas 1024 Mo, les actions de Stockage Blob prennent en charge la [segmentation du message](../logic-apps/logic-apps-handle-large-messages.md). L’[action **Obtenir le contenu de l’objet blob**](/connectors/azureblobconnector/#get-blob-content) utilise implicitement la segmentation.
- Les déclencheurs de Stockage Blob ne prennent pas en charge la segmentation. Quand ils demandent du contenu de fichiers, les déclencheurs sélectionnent uniquement des fichiers dont la taille est inférieure ou égale à 50 Mo. Pour obtenir des fichiers supérieurs à 50 Mo, suivez ce modèle :
  - Utilisez un déclencheur Stockage Blob qui retourne des propriétés de fichier, comme [**Quand un blob est ajouté ou modifié (propriétés uniquement)** ](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)).
  - Suivez le déclencheur avec l’[action Stockage Blob **Obtenir le contenu de l’objet blob**](/connectors/azureblobconnector/#get-blob-content), qui lit le fichier complet et utilise implicitement la segmentation.

## <a name="add-blob-storage-trigger"></a>Ajouter un déclencheur de stockage d’objets blob

Dans Logic Apps, chaque application logique doit démarrer avec un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. 

Ce connecteur dispose d’un déclencheur, appelé soit [**Quand un blob est ajouté ou modifié dans le Stockage Azure** soit **Quand un blob est ajouté ou modifié (propriétés uniquement)** ](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only)). Le déclencheur s’active lorsque les propriétés d’un objet blob sont ajoutées ou mises à jour dans votre conteneur de stockage. Chaque fois, le moteur Logic Apps crée une instance d’application logique et lance l’exécution de votre workflow.

### <a name="single-tenant"></a>[Monolocataire](#tab/single-tenant)

Pour ajouter une action Stockage Blob dans une application logique monolocataire qui utilise un plan Standard :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Ouvrez votre workflow dans le concepteur.
1. Dans la zone de recherche, entrez `Azure blob` en guise de filtre. Dans la liste des déclencheurs, sélectionnez le déclencheur nommé **Quand un blob est ajouté ou modifié dans le Stockage Azure**.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-add.png" alt-text="Capture d’écran de l’application logique Standard dans le concepteur, montrant la sélection du déclencheur nommé lorsqu’un objet blob est ajouté ou modifié dans le Stockage Azure.":::
1. Si vous êtes invité à entrer les informations de connexion, [créez votre connexion de stockage d’objets Blob maintenant](#connect-to-storage-account).
1. Entrez les informations nécessaires pour le déclencheur.
    1. Sous l’onglet **Paramètres**, ajoutez le **Chemin d’objet blob** de l’objet blob que vous voulez surveiller.
        Pour rechercher le chemin d’accès à votre objet blob, ouvrez votre compte de stockage dans le portail Azure. Dans le menu de navigation, sous **Stockage de données**, sélectionnez **Conteneurs**. Sélectionnez votre conteneur blob. Dans le menu de navigation du conteneur, sous **Paramètres**, sélectionnez **Propriétés**. Copiez la valeur **URL**, qui est le chemin d’accès à l’objet blob. Le chemin d’accès ressemble à `https://{your-storage-account}.blob.core.windows.net/{your-blob}`.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-configure.png" alt-text="Capture d’écran de l’application logique standard dans le concepteur, montrant la configuration des paramètres pour le déclencheur de stockage d’objets blob.":::
    1. Configurez d’autres paramètres de déclenchement au besoin.
    1. Sélectionnez **Terminé**.
1. Ajoutez une ou plusieurs actions à votre workflow.
1. Pour enregistrer vos modifications, sélectionnez **Enregistrer** sur la barre d’outils du concepteur.

### <a name="multi-tenant"></a>[Multi-locataire](#tab/multi-tenant)

Pour ajouter un objet Stockage Blob action dans une application logique mutualisée qui utilise un plan de consommation :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Ouvrez votre workflow dans le concepteur Logic Apps.
1. Dans la zone de recherche, saisissez le filtre « blob azure ». Dans la liste des déclencheurs, sélectionnez le déclencheur **Quand un blob est ajouté ou modifié (propriétés uniquement)** .
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-add.png" alt-text="Capture d’écran de l’application logique de consommation dans le concepteur, montrant la sélection du déclencheur stockage blob.":::
1. Si vous êtes invité à entrer les informations de connexion, [créez votre connexion de stockage d’objets Blob maintenant](#connect-to-storage-account).
1. Entrez les informations nécessaires pour le déclencheur.
    1. Pour **Conteneur**, sélectionnez l’icône de dossier pour choisir votre conteneur Stockage Blob. Ou entrez le chemin d’accès manuellement.
    1. Configurez d’autres paramètres de déclenchement au besoin.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-configure.png" alt-text="Capture d’écran de l’application logique de consommation dans le concepteur, montrant la configuration des paramètres pour le déclencheur de stockage d’objets blob.":::
1. Ajoutez une ou plusieurs actions à votre workflow.
1. Pour enregistrer vos modifications, sélectionnez **Enregistrer** sur la barre d’outils du concepteur.

---

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Ajouter une action de stockage d’objets Blob

Dans Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre workflow qui suit un déclencheur ou une autre action.

### <a name="single-tenant"></a>[Monolocataire](#tab/single-tenant)

Pour les applications logiques dans un environnement à locataire unique :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Ouvrez votre workflow dans le concepteur Logic Apps.
1. Ajouter un déclencheur. Cet exemple commence avec le [déclencheur **Récurrence**](../connectors/connectors-native-recurrence.md).
1. Ajoutez une nouvelle étape à votre workflow. Dans la zone de recherche, saisissez le filtre « blob azure ». Ensuite, sélectionnez l’action Stockage Blob que vous souhaitez utiliser. Cet exemple **Lit le contenu de l’objet blob à partir du Stockage Azure**. 
   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-add.png" alt-text="Capture d’écran de l’application logique Standard dans le concepteur, montrant la liste des actions de Stockage d’objets blob disponibles.":::
1. Si vous êtes invité à entrer les informations de connexion, [créez une connexion à votre compte de stockage d’objets blob](#connect-to-storage-account).
1. Entrez les informations nécessaires pour l’action.
    1. Pour **Nom du conteneur**, entrez le chemin du conteneur d’objet blob que vous voulez utiliser.
    1. Pour **Nom de l’objet blob**, saisissez le chemin d’accès au blob que vous souhaitez utiliser.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-configure.png" alt-text="Capture d’écran de l’application logique Standard dans le concepteur, montrant la sélection du déclencheur stockage blob.":::
    1. Configurez d’autres paramètres d’action au besoin.
1. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**. 
1. Testez votre application logique pour vous assurer que votre conteneur sélectionné contient un objet blob. 

> [!TIP]
> Cet exemple ne fait que lire le contenu d’un objet blob. Pour afficher le contenu, ajoutez une autre action qui crée un fichier avec l’objet Blob à l’aide d’un autre connecteur. Par exemple, ajoutez une action OneDrive qui crée un fichier basé sur le contenu de l’objet Blob.

### <a name="multi-tenant"></a>[Multi-locataire](#tab/multi-tenant)

Pour les applications logiques dans un environnement multilocataires :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Ouvrez votre workflow dans le concepteur Logic Apps.
1. Ajouter un déclencheur. Cet exemple commence avec le [déclencheur **Récurrence**](../connectors/connectors-native-recurrence.md).
1. Ajoutez une nouvelle étape à votre workflow. Dans la zone de recherche, saisissez le filtre « blob azure ». Ensuite, sélectionnez l’action Stockage Blob que vous souhaitez utiliser. Cet exemple utilise **Obtenir le contenu de l’objet blob**.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-add.png" alt-text="Capture d’écran de l’application logique de consommation dans le concepteur, montrant la liste des actions de Stockage d’objets blob disponibles.":::
1. Si vous êtes invité à entrer les informations de connexion, [créez une connexion à votre compte de stockage d’objets blob](#connect-to-storage-account).
1. Entrez les informations nécessaires pour l’action.
    1. Pour **Objet blob**, sélectionnez l’icône de dossier pour choisir votre conteneur Stockage Blob. Ou entrez le chemin d’accès manuellement.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-configure.png" alt-text="Capture d’écran de l’application logique de consommation dans le concepteur, montrant la configuration de l’action Stockage d’objets blob.":::
    1. Configurez d’autres paramètres d’action au besoin.

---

## <a name="connect-to-storage-account"></a>Se connecter à un compte de stockage

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Avant de pouvoir configurer votre [déclencheur de stockage d’objet blob](#add-blob-storage-trigger) ou votre [action de stockage d’objet blob](#add-blob-storage-action), vous devez vous connecter à un compte de stockage. Une connexion nécessite les propriétés suivantes.

| Propriété | Obligatoire | Valeur | Description |
|----------|----------|-------|-------------|
| **Nom de connexion** | Oui | <*connection-name*> | Nom à créer pour votre connexion |
| **Chaîne de connexion de stockage d’objets blob Azure** | Oui | <*storage-account*> | Sélectionnez votre compte de stockage dans la liste ou fournissez une chaîne. |

> [!TIP]
> Pour rechercher une chaîne de connexion, accédez à la page du compte de stockage. Dans le menu de navigation, sous **Sécurité + mise en réseau**, sélectionnez **Clés d’accès**. Sélectionnez **Afficher les clés**. Copiez l’une des deux valeurs de chaîne de connexion disponibles.

### <a name="single-tenant"></a>[Monolocataire](#tab/single-tenant)

Pour les applications logiques dans un environnement à locataire unique :

1. Pour **Nom de la connexion**, entrez un nom pour votre connexion.
1. Pour **Chaîne de connexion de stockage d’objets blob Azure**, saisissez la chaîne de connexion du compte de stockage que vous souhaitez utiliser.
1. Sélectionnez **Créer** pour établir votre connexion.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-connection-create.png" alt-text="Capture d’écran de l’application logique Standard dans le concepteur, qui affiche une invite pour ajouter une nouvelle connexion à une étape de stockage d’objets blob.":::

Si vous avez déjà une connexion existante, mais que vous voulez en choisir une autre, sélectionnez **Changer de connexion** dans l’éditeur de l’étape.

Si vous rencontrez des problèmes de connexion à votre compte de stockage, consultez [Accéder à des comptes de stockage derrière des pare-feu](#access-storage-accounts-behind-firewalls).

### <a name="multi-tenant"></a>[Multi-locataire](#tab/multi-tenant)

Pour les applications logiques dans un environnement multilocataires :

1. Pour **Nom de la connexion**, entrez un nom pour votre connexion.
1. Pour **Compte de stockage**, sélectionnez le compte de stockage dans lequel se trouve votre conteneur d’objet blob. Ou bien, sélectionnez **Entrer manuellement les informations de connexion** pour fournir vous-même le chemin.
1. Sélectionnez **Créer** pour établir votre connexion.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-connection-create.png" alt-text="Capture d’écran de l’application logique de consommation dans le concepteur, qui affiche une invite pour ajouter une nouvelle connexion à une étape de stockage d’objets blob.":::

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

Les applications logiques n’ont pas directement accès aux comptes de stockage qui sont derrière des pare-feu dans la même région qu’elles. Pour contourner ce problème, placez vos applications logiques dans une région différente de celle de votre compte de stockage. Ensuite, donnez accès aux [adresses IP sortantes pour les connecteurs managés dans votre région](../logic-apps/logic-apps-limits-and-config.md#outbound).

> [!NOTE]
> Cette solution ne s’applique pas aux connecteurs Stockage Table Azure et Stockage File d’attente Azure. À la place, pour accéder à votre connecteur Stockage Table ou Stockage File d’attente, [utilisez le déclencheur et les actions HTTP intégrés](../logic-apps/logic-apps-http-endpoint.md).

Pour ajouter vos adresses IP sortantes au Pare-feu de compte de stockage :

1. Notez les [adresses IP sortantes](../logic-apps/logic-apps-limits-and-config.md#outbound) pour la région de votre application logique.
1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Ouvrez la page de votre compte de stockage. Dans le menu de navigation, sous **Sécurité + mise en réseau**, sélectionnez **Mise en réseau**. 
1. Sous **Autoriser l’accès depuis**, sélectionnez l’option **Réseaux sélectionnés**. Les paramètres associés s’affichent désormais sur la page.
1. Sous **Pare-feu**, ajoutez les adresses ou plages d’adresses IP qui doivent être accessibles. 
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-ip-configure.png" alt-text="Capture d’écran de la page Mise en réseau du compte de stockage d’objets blob dans le portail Azure, montrant les paramètres du pare-feu pour ajouter des adresses IP et des plages à la liste d’autorisation.":::

### <a name="access-storage-accounts-through-trusted-virtual-network"></a>Accéder aux comptes de stockage via un réseau virtuel approuvé

Vous pouvez placer le compte de stockage dans un réseau virtuel Azure que vous gérez, puis ajouter ce réseau virtuel à la liste des réseaux virtuels approuvés. Pour que votre application logique accède au compte de stockage via un [réseau virtuel approuvé](../virtual-network/virtual-networks-overview.md), vous devez déployer cette application logique sur un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), qui peut se connecter aux ressources d’un réseau virtuel. Vous pouvez ensuite ajouter les sous-réseaux de cet ISE à la liste approuvée. Les connecteurs de stockage Azure, tels que le connecteur Stockage Blob, peuvent accéder directement au conteneur de stockage. Cette configuration est la même que l’utilisation de points de terminaison de service à partir d’un ISE.

### <a name="access-storage-accounts-through-azure-api-management"></a>Accéder aux comptes de stockage par le biais de la gestion des API Azure

Si vous utilisez un niveau dédié pour la [Gestion des API](../api-management/api-management-key-concepts.md), vous pouvez avoir recours à l’API de stockage en utilisant le service Gestion des API et en autorisant les adresses IP de ce dernier via le pare-feu. En gros, ajoutez le réseau virtuel Azure utilisé par le service Gestion des API au paramètre de pare-feu du compte de stockage. Vous pouvez ensuite utiliser l’action Gestion des API ou l’action HTTP pour appeler les API Stockage Azure. Cela dit, si vous choisissez cette option, vous devez gérer vous-même le processus d’authentification. Pour plus d’informations, consultez [Architecture d’intégration d’entreprise simple](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration).

## <a name="access-blob-storage-with-managed-identities"></a>Accéder au Stockage Blob avec des identités managées

Si vous voulez accéder au Stockage Blob sans utiliser ce connecteur Logic Apps, vous pouvez utiliser [les identités managées pour l’authentification](../active-directory/managed-identities-azure-resources/overview.md) à la place. Vous pouvez créer une exception qui donne aux services de confiance Microsoft, comme une identité managée, l’accès à votre compte de stockage via un pare-feu.

Pour utiliser des identités managées dans votre application logique pour accéder au Stockage Blob :

1. [Configurer l’accès à votre compte de stockage](#configure-storage-account-access)
1. [Créer une attribution de rôle pour votre application logique](#create-role-assignment-for-logic-app)
1. [Dans les paramètres de votre application logique, activez la prise en charge de l’identité gérée](#enable-support-for-managed-identity-in-logic-app)

> [!NOTE]
> Limitations de cette solution :
> - Vous pouvez *uniquement* utiliser le déclencheur ou l’action HTTP dans votre workflow.
> - Vous devez configurer une identité managée pour authentifier la connexion de votre compte de stockage.
> - Vous ne pouvez pas utiliser d’opérations de Stockage Blob intégrées si vous vous authentifiez avec une identité managée.
> - Pour les applications logiques dans un environnement à locataire unique, seule l’identité managée affectée par le système est disponible et prise en charge, et non l’identité managée affectée par l’utilisateur.

### <a name="configure-storage-account-access"></a>Configurer l’accès du compte de stockage

Pour configurer l’exception et la prise en charge de l’identité managée, configurez tout d’abord l’accès approprié à votre compte de stockage :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Ouvrez la page de votre compte de stockage. Dans le menu de navigation, sous **Sécurité + mise en réseau**, sélectionnez **Mise en réseau**. 
1. Sous **Autoriser l’accès depuis**, sélectionnez l’option **Réseaux sélectionnés**. Les paramètres associés s’affichent désormais sur la page.
1. Si vous devez accéder au compte de stockage depuis votre ordinateur, sous **Pare-feu**, activez **Ajouter l’adresse IP de votre client**.
1. Sous **Exception**, activez **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage**. 
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-networking-configure.png" alt-text="Capture d’écran de la page Mise en réseau du compte de stockage d’objets blob dans le portail Azure, qui présente les paramètres permettant d’autoriser les réseaux sélectionnés, l’adresse IP du client et les services Microsoft approuvés.":::
1. Sélectionnez **Enregistrer**.

> [!TIP]
> Si vous recevez une erreur **403 Forbidden** lorsque vous essayez de vous connecter au compte de stockage à partir de votre workflow, il y a plusieurs causes possibles. Essayez la résolution suivante avant de passer aux étapes supplémentaires. Tout d’abord, désactivez le paramètre **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** et enregistrez vos modifications. Ensuite, réactivez le paramètre et enregistrez à nouveau vos modifications. 

### <a name="create-role-assignment-for-logic-app"></a>Créer une attribution de rôle pour l’application logique

Ensuite, [activez la prise en charge de l’identité managée](../logic-apps/create-managed-service-identity.md) sur votre application logique.

1. Ouvrez votre application logique dans le portail Azure.
1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Identité**.
1. Sous **Affecté par le système**, définissez **État** sur **Activé**. Ce paramètre est peut-être déjà activé.
1. Sous **Autorisations**, sélectionnez **Attributions de rôles Azure**.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-1.png" alt-text="Capture d’écran du menu de l’application logique dans le portail Azure, montrant le volet Paramètres d’identité avec le bouton pour ajouter des autorisations d’attribution de rôle Azure.":::
1. Sur le volet **Attributions de rôle Azure**, sélectionnez **Ajouter une attribution de rôle**.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-2.png" alt-text="Capture d’écran du volet d’attributions de rôle d’application logique, avec l’abonnement sélectionné et le bouton pour ajouter une nouvelle attribution de rôle.":::
1. Configurez la nouvelle attribution de rôle comme suit.
    1. Pour **Étendue**, sélectionnez **Stockage**.
    1. Pour **Abonnement**, choisissez l’abonnement dans lequel se trouve votre compte de stockage.
    1. Pour **Ressource**, choisissez le compte de stockage auquel vous souhaitez accéder à partir de votre application logique.
    1. Pour **Rôle**, sélectionnez les autorisations appropriées pour votre scénario. Cet exemple utilise **Contributeur aux données Blob du stockage**, qui autorise l’accès en lecture, écriture et suppression aux conteneurs d’objets blob et à la date. Placez le curseur sur l’icône d’informations en regard d’un rôle dans le menu déroulant pour obtenir des informations sur les autorisations.
    1. Sélectionnez **Enregistrer** pour terminer la création de l’attribution de rôle.
        :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-configure.png" alt-text="Capture d’écran du volet de configuration de l’attribution de rôle, qui présente les paramètres de l’étendue, de l’abonnement, de la ressource et du rôle.":::

### <a name="enable-support-for-managed-identity-in-logic-app"></a>Activer la prise en charge de l’identité managée dans l’application logique

Ensuite, ajoutez un [déclencheur ou une action HTTP](connectors-native-http.md) dans votre workflow. Assurez-vous de [définir le type d’authentification pour utiliser l’identité managée](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). 

Les étapes sont les mêmes pour les applications logiques dans les environnements monolocataires et mutualisés.

1. Ouvrez votre workflow dans le concepteur Logic Apps.
1. Ajoutez une nouvelle étape à votre workflow avec un déclencheur ou une action **HTTP**, en fonction de votre scénario.
1. Configurez tous les paramètres requis pour votre déclencheur ou action **HTTP**.
    1. Choisissez une **méthode** pour votre requête. Cet exemple utilise la méthode HTTP PUT.
    1. Entrez l’**URI** de votre objet blob. Le chemin d’accès ressemble à `https://{your-storage-account}.blob.core.windows.net/{your-blob}`.
    1. Sous **En-têtes**, ajoutez l’en-tête de type objet blob `x-ms-blob-type` avec la valeur `BlockBlob`.
    1. Sous **En-têtes**, ajoutez également l’en-tête `x-ms-version` de la version de l’API avec la valeur appropriée. Pour plus d’informations, consultez [Authentifier l’accès avec une identité managée](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) et [Gestion des versions pour les services Stockage Azure](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-connect.png" alt-text="Capture d’écran du concepteur Logic Apps, montrant les paramètres d’action HTTP PUT requis.":::
1. Sélectionnez **Ajouter un nouveau paramètre** et choisissez **Authentification** pour [Configurer l’identité managée](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity).
    1. Sous **Authentification**, pour **Type d’authentification**, choisissez **Identité managée**.
    1. Pour **Identité managée**, choisissez **Identité managée affectée par le système**.
    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-authenticate.png" alt-text="Capture d’écran du concepteur Logic Apps, montrant les paramètres d’authentification des actions HTTP pour l’identité managée.":::
1. Dans la barre d’outils du Concepteur d’application logique, sélectionnez **Enregistrer**.

Vous pouvez maintenant appeler l’[API REST du service Blob](/rest/api/storageservices/blob-service-rest-api) pour exécuter toutes les opérations de stockage nécessaires.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les connecteurs Logic Apps](../connectors/apis-list.md)
