---
title: Définir la configuration technique du plan pour une offre Azure Container dans Microsoft AppSource
description: Définir la configuration technique du plan pour une offre Azure Container dans Microsoft AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 04/21/2021
ms.openlocfilehash: bc720b4df62a06d4c635cc9998dd453bfb7683b1
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108127476"
---
# <a name="set-plan-technical-configuration-for-an-azure-container-offer"></a>Définir la configuration technique du plan pour une offre Azure Container

Les images conteneur doivent être hébergées dans une instance privée d’[Azure Container Registry](https://azure.microsoft.com/services/container-registry/). Utilisez cette page pour fournir des informations de référence pour le dépôt d’images conteneur situé dans Azure Container Registry.

Une fois l’offre envoyée, votre image conteneur est copiée sur la Place de marché Azure dans un registre de conteneurs public spécifique. Toutes les demandes des utilisateurs Azure pour utiliser votre module sont traitées à partir du registre de conteneurs publics de la Place de marché Azure, et non pas depuis votre registre de conteneurs privé.

Vous pouvez cibler plusieurs plateformes et fournir plusieurs versions de l’image conteneur du module par le biais d’étiquettes. Pour en apprendre davantage sur les étiquettes et le contrôle de version, consultez [Préparer les ressources techniques Azure Container](azure-container-technical-assets.md).

## <a name="image-repository-details"></a>Détails du référentiel d’images

Indiquez l’**ID d’abonnement Azure** dans lequel l’utilisation des ressources est signalée et les services sont facturés pour le registre de conteneurs Azure qui contient votre image conteneur. Vous pouvez trouver cet ID dans la [page Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure.

Indiquez le [**nom du groupe de ressources Azure**](../azure-resource-manager/management/manage-resource-groups-portal.md) qui contient le registre de conteneurs Azure abritant votre image conteneur. Le groupe de ressources doit être accessible dans l’ID d’abonnement (ci-dessus). Vous trouverez le nom dans la page [Groupes de ressources](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) du portail Azure.

Indiquez le [**nom du registre de conteneurs Azure**](../container-registry/container-registry-intro.md) contenant votre image conteneur. Le registre de conteneurs doit être présent dans le groupe de ressources Azure que vous avez fourni. Indiquez uniquement le nom du registre, et non le nom complet du serveur de connexion. Omettez **azurecr.io** dans le nom. Vous trouverez le nom du registre dans la [page Registres de conteneurs](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) du portail Azure.

Indiquez le [**nom d’utilisateur administrateur pour le registre de conteneurs Azure**](../container-registry/container-registry-authentication.md#admin-account) associé au registre de conteneurs Azure contenant votre image conteneur. Le nom d’utilisateur et le mot de passe (étape suivante) sont nécessaires pour que votre entreprise ait accès au registre. Pour obtenir le nom de l’utilisateur administrateur et le mot de passe, définissez la propriété **admin-enabled** sur **True** à l’aide de l’interface de ligne de commande Azure (CLI). Vous pouvez éventuellement définir **Utilisateur administrateur** sur **Activer** dans le portail Azure.

:::image type="content" source="media/azure-container/azure-create-12-update-container-registry-edit.png" alt-text="Illustre la boîte de dialogue Mettre à jour le registre de conteneurs.":::

Indiquez le **mot de passe administrateur du registre de conteneurs Azure** associé au registre de conteneurs Azure et qui a votre image conteneur. Le nom d’utilisateur et le mot de passe sont nécessaires pour que votre entreprise ait accès au registre. Vous pouvez récupérer le mot de passe à partir du portail Azure en accédant à **Registre de conteneurs** > **Clés d’accès** ou avec Azure CLI à l’aide de la [commande show](/cli/azure/acr/credential#az-acr-credential-show).

:::image type="content" source="media/azure-container/azure-create-13-access-keys.png" alt-text="Illustre l’écran des clés d’accès dans le portail Azure.":::

Indiquez le **nom du référentiel dans Azure Container Registry** contenant votre image. Vous spécifiez le nom du dépôt quand vous envoyez (push) l’image vers le registre. Vous trouverez le nom du dépôt en accédant à [Registre de conteneurs](https://azure.microsoft.com/services/container-registry/) > **Page Dépôts**. Pour plus d’informations, consultez [Afficher les dépôts de registre de conteneurs dans le portail Azure](../container-registry/container-registry-repositories.md). Une fois le nom défini, il ne peut plus être modifié. Utilisez un nom unique pour chaque offre dans votre compte.

## <a name="image-versions"></a>Versions d’images

Quand vous publiez une mise à jour, les clients doivent être en mesure de la récupérer automatiquement de la Place de marché Azure. S’ils ne souhaitent pas effectuer de mise à jour, ils doivent être en mesure de rester sur une version spécifique de votre image. Pour ce faire, vous pouvez ajouter de nouvelles étiquettes d’image chaque fois que vous effectuez une mise à jour de l’image.

Sélectionnez **Ajouter une version d’image** pour inclure une **balise d’image** pointant vers la dernière version de votre image sur toutes les plateformes prises en charge. Il doit également comprendre une étiquette de version (par exemple, commençant par xx.xx.xx, où xx est un nombre). Les clients doivent utiliser des [étiquettes de manifeste](https://github.com/estesp/manifest-tool) pour cibler plusieurs plateformes. Toutes les balises référencées par une balise de manifeste doivent également être ajoutées afin que nous puissions les charger. Toutes les étiquettes de manifeste (sauf l’étiquette « latest ») doivent commencer par X.Y- ou X.Y.Z, où X, Y et Z sont des entiers. Par exemple, si une étiquette « latest » pointe vers `1.0.1-linux-x64`, `1.0.1-linux-arm32` et `1.0.1-windows-arm32`, ces étiquettes doivent être ajoutées à ce champ. Pour plus d’informations sur les étiquettes et le versioning, consultez [Préparer vos ressources techniques Azure Container](azure-container-technical-assets.md).

> [!TIP]
> Ajoutez une étiquette de test à votre image afin de l’identifier plus facilement pendant les tests.

<!-- possible future restore

## Samples

These examples show how the plan listing fields appear in different views.

These are the fields in Azure Marketplace when viewing plan details:

:::image type="content" source="media/azure-container/azure-create-10-plan-details-mtplc.png" alt-text="Illustrates the fields you see when viewing plan details in Azure Marketplace.":::

These are plan details on the Azure portal:

:::image type="content" source="media/azure-container/azure-create-11-plan-details-portal.png" alt-text="Illustrates plan details on the Azure portal.":::

Select **Save draft**, then **← Plan overview**  in the left-nav menu to return to the plan overview page.
-->
## <a name="next-steps"></a>Étapes suivantes

- Pour **co-vendre avec Microsoft** (facultatif), sélectionnez cette option dans le menu de navigation de gauche. Pour plus d’informations, consultez [Engagement en tant que partenaire de co-vente](./co-sell-overview.md).
- Pour **Revendre via des fournisseurs de solutions cloud** (également facultatif), sélectionnez cette option dans le menu de navigation de gauche. Pour plus d’informations, consultez [Revendre via des partenaires CSP](cloud-solution-providers.md).
- Si vous ne configurez aucune de ces option ou que vous avez terminé, vous pouvez [passer en revue et publier votre offre](review-publish-offer.md).