---
title: Créer et gérer des comptes d’intégration
description: Créez et gérez des comptes d’intégration pour générer des flux de travail d’intégration d’entreprise B2B dans Azure Logic Apps avec l’Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/14/2021
ms.openlocfilehash: fa900b7df1db1efbc8fe28a96cd2048113d7390d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128550261"
---
# <a name="create-and-manage-integration-accounts-for-b2b-workflows-in-azure-logic-apps-using-the-enterprise-integration-pack"></a>Créer et gérer des comptes d’intégration pour des flux de travail B2B dans Azure Logic Apps à l’aide de l’Enterprise Integration Pack

Avant de pouvoir générer des flux de travail d’intégration B2B et d’entreprise à l’aide d’Azure Logic Apps, vous devez créer une ressource *compte d’intégration*. Ce compte est un conteneur cloud évolutif dans Azure, qui simplifie le stockage et la gestion des artefacts B2B que vous définissez et utilisez dans vos flux de travail pour les scénarios B2B. Ces artefacts incluent des [partenaires commerciaux](logic-apps-enterprise-integration-partners.md), des [contrats](logic-apps-enterprise-integration-agreements.md), des [mappages](logic-apps-enterprise-integration-maps.md), des [schémas](logic-apps-enterprise-integration-schemas.md), des [certificats](logic-apps-enterprise-integration-certificates.md), etc. Vous devez également disposer d’un compte d’intégration pour échanger par voie électronique des messages B2B avec d’autres organisations. Lorsque d’autres organisations utilisent des protocoles et des formats de message différents de ceux de votre organisation, vous devez convertir ces formats de façon à ce que le système de votre organisation puisse traiter ces messages. Les protocoles standard pris en charge sont [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), [EDIFACT](logic-apps-enterprise-integration-edifact.md) et [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

> [!TIP]
> Pour créer un compte d’intégration à utiliser avec un [environnement de service d’intégration](connect-virtual-network-vnet-isolated-environment-overview.md), consultez [Créer des comptes d’intégration dans un environnement de service d’intégration](add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

Cet article explique comment accomplir les tâches suivantes :

* Créer un compte d’intégration.
* Lier un compte d’intégration à une ressource d’application logique.
* Modifier le niveau tarifaire de votre compte d’intégration.
* Dissocier un compte d’intégration d’une application logique.
* Déplacer un compte d’intégration vers un autre groupe de ressources ou abonnement Azure.
* Supprimer un compte d’intégration.

Si vous débutez avec Azure Logic Apps, consultez [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md) ? Pour plus d’informations sur l’intégration d’entreprise B2B, consultez [Workflows d’intégration d’entreprise B2B avec Azure Logic Apps et Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Vous devez utiliser le même abonnement Azure pour votre compte d’intégration et votre ressource d’application logique.

* Si vous utilisez le [type de ressource **Application logique (consommation)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), vous devez disposer d’une ressource d’application logique que vous pouvez [lier à votre compte d’intégration](#link-account). Ce lien est requis pour pouvoir utiliser vos artefacts dans votre flux de travail. Vous pouvez créer vos artefacts sans ce lien, mais le lien est requis lorsque vous êtes prêt à utiliser ces artefacts dans vos flux de travail.

* Si vous utilisez le [type de ressource **Application logique (standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), vous pouvez ajouter directement des mappages et schémas à votre ressource d’application logique en utilisant le portail Azure ou Visual Studio Code. Vous pouvez ensuite utiliser ces artefacts sur plusieurs workflows au sein de la *même ressource d’application logique*. Vous devez toujours créer un compte d’intégration pour vos autres artefacts B2B, et utiliser des opérations B2B telles que des opérations [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), [EDIFACT](logic-apps-enterprise-integration-edifact.md) et [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). Toutefois, comme vous n’avez pas besoin de lier votre compte d’intégration à votre ressource d’application logique, la fonctionnalité de liaison n’existe pas.

## <a name="create-integration-account"></a>Créer un compte d’intégration

Des comptes d’intégration sont disponibles dans différents niveaux dont [la tarification varie](https://azure.microsoft.com/pricing/details/logic-apps/). Selon le niveau que vous choisissez, la création d’un compte d’intégration peut occasionner des coûts. Pour plus d’informations, consultez [Modèles de tarification et de facturation de Logic Apps](logic-apps-pricing.md#integration-accounts) et [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

En fonction de vos besoins et scénarios, déterminez le niveau de compte d’intégration approprié à créer. Votre compte d’intégration et votre ressource d’application logique doivent utiliser les *mêmes* emplacement ou région Azure. Le tableau suivant décrit les niveaux disponibles :

| Niveau | Description |
|------|-------------|
| **De base** | Pour les scénarios où vous souhaitez utiliser la gestion des messages ou agir en tant que petite entreprise partenaire ayant une relation de partenariat commercial avec une entité professionnelle plus importante. <p><p>Pris en charge par le contrat SLA Logic Apps. |
| **Standard** | Pour les scénarios où vous avez des relations B2B plus complexes et un nombre croissant d’entités que vous devez gérer. <p><p>Pris en charge par le contrat SLA Logic Apps. |
| **Gratuit** | Pour les scénarios exploratoires, et non pour les scénarios de production. Ce niveau de service est limité quant à la disponibilité, au débit et à l’utilisation des régions. Par exemple, le niveau Gratuit est disponible uniquement pour les régions publiques dans Azure, par exemple, USA Ouest et Asie Sud-Est, mais pas pour [Azure China 21Vianet](/azure/china/overview-operations) ni [Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Remarque** : Non pris en charge par le contrat SLA Logic Apps. |
|||

Pour cette tâche, vous pouvez utiliser le portail Azure, [Azure CLI](/cli/azure/resource#az_resource_create) ou [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount).

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec les informations d’identification de votre compte Azure.

1. Dans la zone de recherche principale Azure, entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

1. Sous **Comptes d’intégration**, sélectionnez **Créer**.

1. Dans le volet **Créer un compte d’intégration**, fournissez les informations suivantes sur votre compte d’intégration :

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Abonnement** | Oui | <*Azure-subscription-name*> | Nom de votre abonnement Azure. |
   | **Groupe de ressources** | Oui | <*nom-groupe-de-ressources-Azure*> | Le nom du [groupe de ressources Azure](../azure-resource-manager/management/overview.md) à utiliser pour organiser les ressources connexes. Pour cet exemple, créez un groupe de ressources nommé `FabrikamIntegration-RG`. |
   | **Nom de compte d’intégration** | Oui | <*integration-account-name*> | Nom de votre compte d’intégration, qui peut contenir uniquement des lettres, des chiffres, des traits d’union (`-`) des traits de soulignement (`_`), des parenthèses (`(`, `)`) et des points (`.`). Cet exemple utilise `Fabrikam-Integration`. |
   | **Région** | Oui | <*Azure-region*> | Région Azure dans laquelle stocker les métadonnées de votre compte d’intégration. Sélectionnez l’emplacement de votre application logique ou créez vos applications logiques au même emplacement que votre compte d’intégration. Pour cet exemple, utilisez `West US`. <p>**Remarque**: pour créer un compte d’intégration à l’intérieur d’un [environnement de service d’intégration (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), sélectionnez **Associer à l’environnement de service d’intégration**, puis choisissez votre ISE comme emplacement. Pour plus d’informations, consultez [Créer des comptes d’intégration dans un environnement ISE](add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Niveau tarifaire** | Oui | <*pricing-level*> | Niveau tarifaire pour le compte d’intégration, que vous pouvez modifier par la suite. Dans cet exemple, sélectionnez **Gratuit**. Pour plus d’informations, consultez la documentation suivante : <p>- [Modèle de tarification de Logic Apps](logic-apps-pricing.md#integration-accounts) <br>- [Limites et configuration de Logic Apps](logic-apps-limits-and-config.md#integration-account-limits) <br>- [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Activer l’analytique des journaux** | Non | Non sélectionné | Pour cet exemple, ne sélectionnez pas cette option. |
   |||||

1. Quand vous avez terminé, sélectionnez **Vérifier + créer**.

   Une fois le déploiement terminé, Azure ouvre votre compte d’intégration.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Pour ajouter l’extension [az logic integration-account](/cli/azure/logic/integration-account), utilisez la commande [az extension add](/cli/azure/extension#az_extension_add) :

   ```azurecli
   az extension add –-name logic
   ```

1. Pour créer un groupe de ressources ou utiliser un groupe de ressources existant, exécutez la commande [az group create](/cli/azure/group#az_group_create) :

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Pour répertorier les comptes d’intégration d’un groupe de ressources, utilisez la commande [az logic integration-account list](/cli/azure/logic/integration-account#az_logic_integration_account_list) :

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Pour créer un compte d’intégration, exécutez la commande [az logic integration-account create](/cli/azure/logic/integration-account#az_logic_integration_account_create) :

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   Nom de votre compte d’intégration, qui peut contenir uniquement des lettres, des chiffres, des traits d’union [-] des traits de soulignement [ _ ], des parenthèses [(, )] et des points [.].

   Pour afficher un compte d’intégration spécifique, utilisez la commande [az logic integration-account show](/cli/azure/logic/integration-account#az_logic_integration_account_show) :

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   Vous pouvez modifier votre SKU ou le niveau tarifaire à l’aide de la commande [az logic integration-account update](/cli/azure/logic/integration-account#az_logic_integration_account_update) :

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Pour plus d’informations sur la tarification, consultez ces ressources :

   * [Modèle de tarification de Logic Apps](logic-apps-pricing.md#integration-accounts)
   * [Limites et configuration de Logic Apps](logic-apps-limits-and-config.md#integration-account-limits)
   * [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)

Pour importer un compte d’intégration à l’aide d’un fichier JSON, utilisez la commande [az logic integration-account import](/cli/azure/logic/integration-account#az_logic_integration_account_import) :

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

---

<a name="link-account"></a>

## <a name="link-to-logic-app-consumption-resource-only"></a>Lien vers l’application logique (ressource de consommation uniquement)

Pour que votre flux de travail d’**Application logique (consommation)** accède aux artefacts B2B dans votre compte d’intégration, vous devez commencer par lier votre ressource d’application logique à votre compte d’intégration. Votre application logique et votre compte d’intégration doivent utiliser le même abonnement et la même région Azure. Pour effectuer cette tâche, vous pouvez utiliser le portail Azure. Si vous utilisez Visual Studio et que votre application logique se trouve dans un [projet Azure Resource Group](../azure-resource-manager/templates/create-visual-studio-deployment-project.md), vous pouvez [lier votre application logique à un compte d’intégration avec Visual Studio](manage-logic-apps-with-visual-studio.md#link-integration-account).

1. Dans le [portail Azure](https://portal.azure.com), ouvrez une application logique existante ou créez-en une.

1. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Paramètres de flux de travail**. Sous **compte d’intégration**, ouvrez la liste **Sélectionner un compte d’intégration**, puis sélectionnez le compte d’intégration de votre choix.

   ![Capture d’écran montrant le portail Azure affichant le menu du compte d’intégration avec le volet « Paramètres du flux de travail » ouvert et la liste « Sélectionner un compte d’intégration » ouverte.](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Pour terminer la liaison, sélectionnez **Enregistrer**.

   ![Capture d’écran montrant le volet « Paramètres du flux de travail » et l’option « Enregistrer » sélectionnée.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Une fois votre compte d’intégration correctement associé, Azure affiche un message de confirmation.

   ![Capture d’écran montrant le message de confirmation d’Azure.](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Désormais, votre application logique peut utiliser les artefacts de votre compte d’intégration en plus des connecteurs B2B, tels que la validation XML et l’encodage ou le décodage de fichiers plats.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Changer le niveau tarifaire

Pour augmenter les [limites](logic-apps-limits-and-config.md#integration-account-limits) d’un compte d’intégration, vous pouvez [procéder à une mise à niveau vers un niveau tarifaire supérieur](#upgrade-pricing-tier), le cas échéant. Par exemple, vous pouvez effectuer une mise à niveau à partir du niveau gratuit vers le niveau De base ou Standard. Vous pouvez également [passer à un niveau inférieur](#downgrade-pricing-tier), le cas échéant. Pour plus d’informations sur la tarification, consultez la documentation suivante :

* [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Modèle de tarification de Logic Apps](logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Mettre à niveau le niveau tarifaire

Pour effectuer cette modification, vous pouvez utiliser le portail Azure ou l’interface de ligne de commande Azure.

#### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec les informations d’identification de votre compte Azure.

1. Dans la zone de recherche principale Azure, entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

   Azure affiche tous les comptes d’intégration dans vos abonnements Azure.

1. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration à déplacer. Dans le menu de votre compte d’intégration, sélectionnez **Vue d’ensemble**.

   ![Capture d’écran montrant le portail Azure avec le menu du compte d’intégration et l’option « Vue d’ensemble » sélectionnée.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Dans le volet Vue d’ensemble, sélectionnez **Mettre à niveau le niveau tarifaire** pour afficher la liste des niveaux supérieurs disponibles. Lorsque vous sélectionnez un niveau, la modification prend effet immédiatement.

   ![Capture d’écran montrant le volet « Vue d’ensemble » du compte d’intégration avec l’option « Mettre à niveau le niveau tarifaire » sélectionnée.](media/logic-apps-enterprise-integration-create-integration-account/upgrade-pricing-tier.png)

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Si ce n’est pas déjà fait, [installez les composants requis de l’interface de ligne de commande Azure](/cli/azure/get-started-with-azure-cli).

1. Dans le portail Azure, ouvrez l’environnement [Azure Cloud Shell](../cloud-shell/overview.md).

   ![Capture d’écran montrant la barre d’outils du portail Azure avec l’option « Cloud Shell » sélectionnée.](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. À l’invite de commandes, entrez la commande [**az resource**](/cli/azure/resource#az_resource_update), puis définissez `skuName` sur le niveau le plus élevé souhaité.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Par exemple, si vous avez le niveau De base, vous pouvez définir `skuName` sur `Standard` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Passage à un niveau tarifaire inférieur

Pour effectuer cette modification, utilisez l’[interface de ligne de commande Azure](/cli/azure/get-started-with-azure-cli).

1. Si ce n’est pas déjà fait, [installez les composants requis de l’interface de ligne de commande Azure](/cli/azure/get-started-with-azure-cli).

1. Dans le portail Azure, ouvrez l’environnement [Azure Cloud Shell](../cloud-shell/overview.md).

   ![Capture d’écran montrant la barre d’outils du portail Azure avec l’option « Cloud Shell » sélectionnée.](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. À l’invite de commandes, entrez la commande [**az resource**](/cli/azure/resource#az_resource_update), puis définissez `skuName` sur le niveau le plus bas souhaité.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Par exemple, si vous avez le niveau Standard, vous pouvez définir `skuName` sur `Basic` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Dissocier de l’application logique

Si vous voulez lier votre application logique à un autre compte d’intégration, ou ne plus utiliser un compte d’intégration avec votre application logique, supprimez la liaison à l’aide d’Azure Resource Explorer.

1. Ouvrez la fenêtre de votre navigateur, puis accédez à [Azure Resource Explorer (https://resources.azure.com)](https://resources.azure.com). Connectez-vous avec les mêmes informations d’identification de votre compte Azure.

   ![Capture d’écran montrant un navigateur web avec Azure Resource Explorer.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Dans la zone de recherche, entrez le nom de votre application logique, puis recherchez et sélectionnez votre application logique.

   ![Capture d’écran montrant la zone de recherche de l’explorateur, contenant le nom de votre application logique.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Dans la barre de titre de l’explorateur, sélectionnez **Lecture/écriture**.

   ![Capture d’écran montrant la barre de titre avec l’option « Lecture/Écriture » sélectionnée.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Sous l’onglet **Données**, sélectionnez **Modifier**.

   ![Capture d’écran montrant l’onglet « Données » avec l’option « Modifier » sélectionnée.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Dans l’éditeur, recherchez l’objet `integrationAccount`, puis supprimez-le. Son format est le suivant :

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Par exemple :

   ![Capture d’écran montrant comment rechercher l’objet « integrationAccount ».](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Sous l’onglet **Données**, sélectionnez **Put** pour enregistrer vos modifications.

   ![Capture d’écran montrant l’onglet « Données » avec l’option « Placer » sélectionnée.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Dans le Portail Azure, ouvrez votre application logique. Dans le menu de votre application logique, sous **Paramètres de flux de travail**, vérifiez que la propriété **Compte d’intégration** apparaît désormais vide.

   ![Capture d’écran montrant le portail Azure avec le menu de l’application logique et l’option « Paramètres du flux de travail » sélectionnée.](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Déplacer un compte d’intégration

Vous pouvez déplacer votre compte d’intégration vers un autre groupe de ressources Azure ou un abonnement Azure. Lorsque vous déplacez des ressources, Azure crée de nouveaux ID de ressource. Veillez donc à utiliser les nouveaux ID et à mettre à jour tous les scripts ou outils associés aux ressources déplacées. Si vous souhaitez modifier l’abonnement, vous devez également spécifier un groupe de ressources nouveau ou existant.

Pour cette tâche, vous pouvez utiliser le Portail Azure en suivant les étapes décrites dans cette section ou l’[interface de ligne de commande Azure](/cli/azure/resource#az_resource_move).

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec les informations d’identification de votre compte Azure.

1. Dans la zone de recherche principale Azure, entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

   Azure affiche tous les comptes d’intégration dans vos abonnements Azure.

1. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration à déplacer. Dans le menu de votre compte d’intégration, sélectionnez **Vue d’ensemble**.

1. Dans le volet Vue d’ensemble, en regard de **Groupe de ressources** ou de **Nom d’abonnement**, sélectionnez **Modifier**.

   ![Capture d’écran montrant le portail Azure et le volet « Vue d’ensemble » avec l’option « Modifier » sélectionnée en regard de « Groupe de ressources » ou de « Nom d’abonnement ».](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Sélectionnez les ressources associées que vous souhaitez également déplacer.

1. En fonction de votre sélection, procédez comme suit pour modifier le groupe de ressources ou l’abonnement :

   * Groupe de ressources : Dans la liste **Groupe de ressources**, sélectionnez le groupe de ressources de destination. Ou, pour créer un groupe de ressources différent, sélectionnez **Créer un groupe de ressources**.

   * Abonnement : Dans la liste **Abonnement**, sélectionnez l’abonnement de destination. Dans la liste **Groupe de ressources**, sélectionnez le groupe de ressources de destination. Ou, pour créer un groupe de ressources différent, sélectionnez **Créer un groupe de ressources**.

1. Pour confirmer que tous les scripts ou outils associés aux ressources déplacées ne fonctionneront pas tant que vous ne les aurez pas mis à jour avec les nouveaux ID de ressource, sélectionnez la zone de confirmation, puis sélectionnez **OK**.

1. Une fois que vous avez terminé, veillez à mettre à jour tous les scripts avec les nouveaux ID de ressource pour vos ressources déplacées.  

## <a name="delete-integration-account"></a>Supprimer un compte d’intégration

Pour cette tâche, vous pouvez utiliser le Portail Azure en suivant les étapes de cette section, l’[interface de ligne de commande Azure](/cli/azure/resource#az_resource_delete) ou [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec les informations d’identification de votre compte Azure.

1. Dans la zone de recherche principale Azure, entrez `integration accounts` et sélectionnez **Comptes d’intégration**.

   Azure affiche tous les comptes d’intégration dans vos abonnements Azure.

1. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration à supprimer. Dans le menu de votre compte d’intégration, sélectionnez **Vue d’ensemble**.

   ![Capture d’écran montrant le portail Azure avec la liste « Comptes d’intégration » et le menu du compte d’intégration avec l’option « Vue d’ensemble » sélectionnée.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Dans le volet Vue d’ensemble, sélectionnez **Supprimer**.

   ![Capture d’écran montrant le volet « Vue d’ensemble » avec l’option « Supprimer » sélectionnée.](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Pour confirmer que vous souhaitez supprimer votre compte d’intégration, sélectionnez **Oui**.

   ![Capture d’écran montrant la boîte de dialogue de confirmation et l’option « Oui » sélectionnée.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

<a name="delete-account-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vous pouvez supprimer un compte d’intégration à l’aide de la commande [az logic integration-account delete](/cli/azure/logic/integration-account#az_logic_integration_account_delete) :

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

---

## <a name="next-steps"></a>Étapes suivantes

* [Créer des partenaires commerciaux dans votre compte d’intégration](logic-apps-enterprise-integration-partners.md)
* [Créer des accords entre partenaires dans votre compte d’intégration](logic-apps-enterprise-integration-agreements.md)
