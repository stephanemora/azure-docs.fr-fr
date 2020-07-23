---
title: Obtenir de l’aide pour les échecs d’approvisionnement ou de déploiement d’Azure VMware Solution
description: Comment obtenir les informations dont vous avez besoin auprès de votre cloud privé Azure VMware Solution (AVS) afin de traiter une demande de service pour les échecs de déploiement ou d’approvisionnement d’AVS.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 05c9dee088b37c37cdcdee7e745cdcd2222b63e0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514575"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Obtenir de l’aide pour les échecs d’approvisionnement ou de déploiement d’Azure VMware Solution

Cet article vous apprend à obtenir de l’aide pour les échecs d’approvisionnement ou de déploiement d’Azure VMware Solution (AVS) sur votre cloud privé en ouvrant une demande de service sur le portail Azure. Toutefois, vous devez d’abord collecter des informations clés dans le portail Azure. Dans la plupart des cas, vous avez besoin de :

- ID de corrélation (du déploiement ayant échoué)
- ID de circuit ExpressRoute (en cas d’échec de la tentative de mise à l’échelle ou d’appairage d’un cloud privé existant avec le circuit ExpressRoute du cloud privé)

## <a name="collect-the-correlation-id"></a>Collecter l’ID de corrélation
 
Examinons d’abord l’ID de corrélation. Lorsque vous créez un cloud privé (ou n’importe quelle ressource dans Azure), un ID de corrélation associé est généré. Chaque déploiement d’Azure Resource Manager génère également un ID de corrélation unique. Cet ID permet d’accélérer la création et la résolution d’une demande de service. 
 
Voici un exemple de la sortie d’un déploiement de cloud privé ayant échoué, avec l’ID de corrélation mis en surbrillance.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Échec du déploiement du cloud privé avec l’ID de corrélation.":::

Copiez et enregistrez cet ID de corrélation pour l’inclure dans la demande de service. Pour plus d’informations, consultez [Créer votre demande de support](#create-your-support-request) à la fin de cet article.

Si l’échec se produit lors des étapes préalables à la validation, avant le déploiement d’un cloud privé, aucun ID de corrélation n’est généré. Dans ce cas, vous pouvez simplement fournir les informations que vous avez utilisées lors de la création du cloud privé AVS, notamment :

- Emplacement
- Resource group
- Nom de la ressource
 
### <a name="collect-a-summary-of-errors"></a>Collecter un résumé des erreurs

Les détails des erreurs peuvent également être utiles pour résoudre votre problème. Dans l’écran précédent, sélectionnez **Cliquez ici pour plus de détails** (en surbrillance). Un résumé des erreurs s’ouvre, comme affiché dans la capture d’écran suivante.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Résumé des options.":::

Recopiez et réenregistrez ce résumé pour l’inclure dans la demande de service.
 
### <a name="retrieve-past-deployments"></a>Récupérer les déploiements passés

Vous pouvez récupérer les déploiements passés, notamment ceux qui ont échoué, en effectuant une recherche dans le journal d’activité de déploiement auquel vous accédez en sélectionnant l’icône des notifications.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Ouvrez les notifications.":::

Dans Notifications, sélectionnez **Plus d’événements dans le journal d’activité**.

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Lien : Plus d’événements dans le journal d’activité.":::

Recherchez ensuite le nom de la ressource, ou une autre information unique que vous avez utilisée lors de la création de la ressource en vue de trouver le déploiement ayant échoué et son ID de corrélation. L’exemple suivant montre les résultats de recherche sur une ressource de cloud privé (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Recherchez les anciens déploiements d’AVS ayant échoué.":::
 
Si vous sélectionnez le nom d’opération du déploiement qui a échoué, une fenêtre contenant des détails s’ouvre. Sélectionnez l’onglet JSON et recherchez correlationId. Copiez et incluez dans la demande de service. 
 
## <a name="collect-the-expressroute-id-uri"></a>Collecter l’ID ExpressRoute (URI)
 
Vous disposez peut-être déjà d’un cloud privé et vous rencontrez un problème lorsque vous essayez de le mettre à l’échelle ou de l’appairer avec le circuit d’ExpressRoute du cloud privé. Dans ce cas, l’ID ExpressRoute du cloud privé peut être utilisé pour l’identifier lors de la création d’une demande de service.

Lors de l’affichage d’un cloud privé dans le portail, sélectionnez **Connectivité > ExpressRoute** et copiez l’**ID ExpressRoute** dans le Presse-papiers.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Collectez un ID ExpressRoute."::: 
 
Collez l’ID ExpressRoute dans le champ approprié de la nouvelle demande de support. Pour plus d’informations, consultez la section suivante, [Créer votre demande de support](#create-your-support-request).
 
> [!NOTE]
> Les vérifications préalables à la validation peuvent parfois échouer avant un déploiement et les seules informations disponibles sont les messages d’erreur et/ou d’échec. Elles peuvent être utiles dans un certain nombre d’échecs, par exemple pour des problèmes liés au quota, et il est important d’inclure ces messages dans la demande de support. Pour les collecter, consultez la section précédente, [Collecter un résumé des erreurs](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Créer votre demande de support

Pour obtenir des conseils généraux sur la création d’une demande de support, consultez [Comment créer une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Voici des conseils supplémentaires spécifiques à la création d’une demande de service pour les échecs d’approvisionnement ou de déploiement de l’AVS.

1. Sélectionnez l’icône **Aide** puis **+ Nouvelle demande de support**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Collectez un ID ExpressRoute.":::

2. Renseignez tous les champs obligatoires et, sous l’onglet **Informations de base** :

    - Pour **Type de problème**, sélectionnez **Problèmes de configuration et d’installation**.

    - Pour **Sous-type de problème**, sélectionnez **Configurer un cloud privé**.

3. Sous l’onglet **Détails** :

    - Renseignez tous les champs obligatoires.

    - Collez votre ID de corrélation ou ExpressRoute dans les champs spécifiques fournis. Si vous ne voyez pas de champ spécifique, vous pouvez les coller dans la zone de texte sous **Fournissez des détails sur le problème.**

    - Collez les détails de l’erreur, notamment le résumé des erreurs que vous avez copié, dans la zone de texte sous **Fournissez des détails sur le problème.**

4. Vérifiez, puis sélectionnez **Créer** pour créer votre demande de service.
