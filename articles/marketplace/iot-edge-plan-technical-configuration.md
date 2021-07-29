---
title: Définir la configuration technique d’un plan pour une offre de module IoT Edge sur la Place de marché Azure
description: Définissez la configuration technique d’un plan pour une offre de module IoT Edge sur la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: acae63eff8be41453c4483a2d8364531ee109f71
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955708"
---
# <a name="set-plan-technical-configuration-for-an-iot-edge-module-offer"></a>Définir la configuration technique d’un plan pour une offre de module IoT Edge

Le type d’offre Module IoT Edge est un type spécifique de conteneur qui s’exécute sur un appareil IoT Edge. Sous l’onglet **Configuration technique**, vous fournissez des informations de référence pour le dépôt d’images conteneur à l’intérieur de [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) ainsi que des paramètres de configuration qui permettent aux clients d’utiliser le module.

Une fois l’offre envoyée, votre image conteneur IoT Edge est copiée sur la Place de marché Azure dans un registre de conteneurs public spécifique. Toutes les demandes des utilisateurs Azure pour utiliser votre module sont traitées à partir du registre de conteneurs publics de la Place de marché Azure, et non pas depuis votre registre de conteneurs privé.

Vous pouvez cibler plusieurs plateformes et fournir plusieurs versions de l’image conteneur du module par le biais d’étiquettes. Pour en apprendre davantage sur les étiquettes et le versioning, consultez [Préparer vos ressources techniques de module IoT Edge](iot-edge-technical-asset.md).

## <a name="image-repository-details"></a>Détails du référentiel d’images

Sélectionnez **Azure Container Registry** comme source de l’image.

Indiquez l’**ID d’abonnement Azure** dans lequel l’utilisation des ressources est signalée et les services sont facturés pour le registre de conteneurs Azure qui contient votre image conteneur. Vous pouvez trouver cet ID dans la [page Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure.

Indiquez le [**nom du groupe de ressources Azure**](../azure-resource-manager/management/manage-resource-groups-portal.md) qui contient le registre de conteneurs Azure abritant votre image conteneur. Le groupe de ressources doit être accessible dans l’ID d’abonnement (ci-dessus). Vous trouverez le nom dans la page [Groupes de ressources](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) du portail Azure.

Indiquez le [**nom du registre de conteneurs Azure**](../container-registry/container-registry-intro.md) contenant votre image conteneur. Le registre de conteneurs doit être présent dans le groupe de ressources Azure que vous avez fourni. Indiquez uniquement le nom du registre, et non le nom complet du serveur de connexion. Omettez **azurecr.io** dans le nom. Vous trouverez le nom du registre dans la [page Registres de conteneurs](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) du portail Azure.

Indiquez le [**nom d’utilisateur administrateur pour le registre de conteneurs Azure**](../container-registry/container-registry-authentication.md#admin-account) associé au registre de conteneurs Azure contenant votre image conteneur. Le nom d’utilisateur et le mot de passe (étape suivante) sont nécessaires pour que votre entreprise ait accès au registre. Pour obtenir le nom de l’utilisateur administrateur et le mot de passe, définissez la propriété **admin-enabled** sur **True** à l’aide de l’interface de ligne de commande Azure (CLI). Vous pouvez éventuellement définir **Utilisateur administrateur** sur **Activer** dans le portail Azure.

:::image type="content" source="media/iot-edge/example-iot-update-container-registry.png" alt-text="Illustre la boîte de dialogue Mettre à jour le registre de conteneurs.":::

#### <a name="call-out-description"></a>Description de légende

1. Utilisateur administrateur

<br>Indiquez le **mot de passe administrateur du registre de conteneurs Azure** associé au registre de conteneurs Azure et qui a votre image conteneur. Le nom d’utilisateur et le mot de passe sont nécessaires pour que votre entreprise ait accès au registre. Vous pouvez récupérer le mot de passe à partir du portail Azure en accédant à **Registre de conteneurs** > **Clés d’accès** ou avec Azure CLI à l’aide de la [commande show](/cli/azure/acr/credential#az-acr-credential-show).

:::image type="content" source="media/iot-edge/example-iot-access-keys.png" alt-text="Illustre l’écran des clés d’accès dans le portail Azure.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Clés d'accès
2. Nom d’utilisateur
3. Mot de passe

Indiquez le **nom du référentiel dans Azure Container Registry** contenant votre image. Vous spécifiez le nom du dépôt quand vous envoyez (push) l’image vers le registre. Vous trouverez le nom du dépôt en accédant à [Registre de conteneurs](https://azure.microsoft.com/services/container-registry/) > **Page Dépôts**. Pour plus d’informations, consultez [Afficher les dépôts de registre de conteneurs dans le portail Azure](../container-registry/container-registry-repositories.md). Une fois le nom défini, il ne peut plus être modifié. Utilisez un nom unique pour chaque offre dans votre compte.

> [!NOTE]
> Nous ne prenons pas en charge d’instance chiffrée d’Azure Container Registry pour la certification du module Edge. L’instance d’Azure Container Registry doit être créée sans que le chiffrement soit activé.

## <a name="image-versions"></a>Versions d’images

Quand vous publiez une mise à jour, les clients doivent être en mesure de la récupérer automatiquement de la Place de marché Azure. S’ils ne souhaitent pas effectuer de mise à jour, ils doivent être en mesure de rester sur une version spécifique de votre image. Pour ce faire, vous pouvez ajouter de nouvelles étiquettes d’image chaque fois que vous effectuez une mise à jour de l’image.

Sélectionnez **Ajouter une version d’image** pour inclure une **balise d’image** pointant vers la dernière version de votre image sur toutes les plateformes prises en charge. Il doit également comprendre une étiquette de version (par exemple, commençant par xx.xx.xx, où xx est un nombre). Les clients doivent utiliser des [étiquettes de manifeste](https://github.com/estesp/manifest-tool) pour cibler plusieurs plateformes. Toutes les balises référencées par une balise de manifeste doivent également être ajoutées afin que nous puissions les charger. Toutes les étiquettes de manifeste (sauf l’étiquette « latest ») doivent commencer par X.Y- ou X.Y.Z, où X, Y et Z sont des entiers. Par exemple, si une étiquette « latest » pointe vers `1.0.1-linux-x64`, `1.0.1-linux-arm32` et `1.0.1-windows-arm32`, ces étiquettes doivent être ajoutées à ce champ. Pour plus d’informations sur les étiquettes et le versioning, consultez [Préparer les ressources techniques de module IoT Edge](iot-edge-technical-asset.md).

> [!TIP]
> Ajoutez une étiquette de test à votre image afin de l’identifier plus facilement pendant les tests.

## <a name="default-deployment-settings"></a>Paramètres de déploiement par défaut

Définissez les paramètres les plus courants pour déployer votre module IoT Edge (facultatif). Optimisez les déploiements des clients en les autorisant à lancer votre module IoT Edge prêt à l’emploi avec ces paramètres par défaut.

**Itinéraires par défaut**. Le hub IoT Edge gère la communication entre les modules, le hub IoT et les appareils. Vous pouvez définir des routes par défaut pour l’entrée et la sortie de données entre les modules et le hub IoT afin d’envoyer des messages au bon destinataire sans avoir recours à d’autres services pour traiter les messages ou écrire du code supplémentaire. Les routes sont construites à l’aide de paires nom/valeur. Vous pouvez définir jusqu’à cinq noms de routes par défaut, chacun d’une longueur maximale de 512 caractères.

Utilisez la [syntaxe de route](../iot-edge/module-composition.md#declare-routes) correcte dans la valeur de la route (généralement définie sous la forme FROM/message/* INTO $upstream). Cela signifie que tous les messages envoyés par les modules sont dirigés vers votre hub IoT. Pour faire référence à votre module, utilisez son nom de module par défaut, qui est votre **Nom d’offre** sans espaces ou caractères spéciaux. Pour faire référence à d’autres modules qui ne sont pas encore connus, utilisez la convention <NOM_MODULE_SOURCE> pour informer vos clients qu’ils doivent mettre à jour ces informations. Pour plus d’informations sur les routes IoT Edge, consultez [Déclarer des routes](../iot-edge/module-composition.md#declare-routes)).

Par exemple, si le module ContosoModule écoute les entrées sur ContosoInput et les données de sortie au niveau de ContosoOutput, il est judicieux de définir les deux routes par défaut suivantes :

- Nom 1 : ToContosoModule
- Valeur 1 : FROM /messages/modules/<NOM_MODULE_SOURCE>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Nom 2 : FromContosoModuleToCloud
- Valeur 2 : FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Propriétés souhaitées du jumeau de module par défaut**. Un jumeau de module est un document JSON dans le hub IoT qui stocke les informations d’état d’une instance de module, notamment les propriétés souhaitées. Les propriétés souhaitées sont utilisées en même temps que les propriétés signalées pour synchroniser une configuration ou une condition de module. Le back-end de solution peut définir les propriétés souhaitées et le module peut les lire. Le module peut également recevoir des notifications sur les changements des propriétés souhaitées. Les propriétés souhaitées sont créées à l’aide de cinq paires nom/valeur, et chaque valeur par défaut doit contenir moins de 512 caractères. Vous pouvez définir jusqu’à cinq noms/valeurs de propriétés souhaitées du jumeau. Les valeurs des propriétés souhaitées du jumeau doivent être un code JSON valide, sans séquence d’échappement ni tableaux, ayant une hiérarchie imbriquée maximale de quatre niveaux. Dans un scénario où un paramètre requis pour une valeur par défaut n’est pas judicieux (par exemple, l’adresse IP du serveur d’un client), vous pouvez ajouter un paramètre comme valeur par défaut. Pour plus d’informations sur les propriétés souhaitées du jumeau, consultez [Définir ou mettre à jour les propriétés souhaitées](../iot-edge/module-composition.md#define-or-update-desired-properties)).

Par exemple, si un module prend en charge une fréquence d’actualisation configurable de manière dynamique à l’aide de ses propriétés souhaitées du jumeau, il semble judicieux de définir la propriété souhaitée du jumeau par défaut suivante :

- Nom 1 : RefreshRate
- Valeur 1 : 60

**Variables d’environnement par défaut**. Les variables d’environnement fournissent des informations complémentaires à un module, qui aident le processus de configuration. Les variables d’environnement sont créées à l’aide de paires nom/valeur. Chaque nom et valeur de variable d’environnement par défaut doit contenir moins de 512 caractères et vous pouvez définir jusqu’à cinq paires. Quand un paramètre requis pour une valeur par défaut n’est pas judicieux (par exemple, l’adresse IP du serveur d’un client), vous pouvez ajouter un paramètre comme valeur par défaut.

Par exemple, si un module a besoin d’accepter des conditions d’utilisation avant d’être démarré, vous pouvez définir la variable d’environnement suivante :

- Nom 1 : ACCEPT_EULA
- Valeur 1: O

**Options de création de conteneur par défaut**. Les options de création de conteneur dirigent la création du conteneur Docker du module IoT Edge. IoT Edge prend en charge les options de création de conteneur de l’API du moteur Docker. Vous trouverez toutes les options dans la page [List containers](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList). Le champ des options de création doit être un code JSON valide, sans séquence d’échappement et contenant moins de 512 caractères.

Par exemple, si un module nécessite une liaison de port, définissez les options de création suivantes :

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="samples"></a>Exemples

Voici un exemple de détails du plan dans la Place de marché Azure (les prix répertoriés sont fournis à titre d’exemple uniquement et ne reflètent pas les coûts réels) :

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-plan.png" alt-text="Illustre des détails du plan dans la Place de marché Azure.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Nom de l’offre
2. Nom du plan
3. Description du plan

<br>Voici un exemple de détails du plan dans le portail Azure (les prix répertoriés sont fournis à titre d’exemple uniquement et ne reflètent pas les coûts réels) :

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-plan-details.png" alt-text="Illustre les détails du plan dans le portail Azure.":::

#### <a name="call-out-descriptions"></a>Descriptions de légende

1. Nom de l’offre
2. Nom du plan
3. Description du plan

Sélectionnez **Enregistrer le brouillon**, puis **← Vue d’ensemble du plan** dans le menu de navigation de gauche pour revenir à la page de vue d’ensemble du plan.

## <a name="next-steps"></a>Étapes suivantes

- Pour **co-vendre avec Microsoft** (facultatif), sélectionnez cette option dans le menu de navigation de gauche. Pour plus d’informations, consultez [Engagement en tant que partenaire de co-vente](./co-sell-overview.md).
- Si vous ne configurez aucune option de co-vente ou que vous avez terminé, vous pouvez [passer en revue et publier votre offre](review-publish-offer.md).