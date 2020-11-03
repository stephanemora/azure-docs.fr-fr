---
title: Obtenir de l’aide pour les échecs d’approvisionnement ou de déploiement d’Azure VMware Solution
description: Comment obtenir les informations dont vous avez besoin auprès de votre cloud privé Azure VMware Solution afin de traiter une demande de service pour les échecs de déploiement ou d’approvisionnement d’Azure VMware Solution.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 1f46dde895db417fd2b488a6203d5482e73d3c5e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779490"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Obtenir de l’aide pour les échecs d’approvisionnement ou de déploiement d’Azure VMware Solution

Cet article vous aide à résoudre les problèmes de déploiement et d’approvisionnement d’Azure VMware Solution. Lorsque vous rencontrez des échecs sur votre cloud privé, vous devez ouvrir une [demande de support](https://rc.portal.azure.com/#create/Microsoft.Support) dans le portail Azure. 

Toutefois, vous devez commencer par collecter des informations clés dans le portail Azure :

- ID de corrélation :
- ID de circuit ExpressRoute

## <a name="collect-the-correlation-id"></a>Collecter l’ID de corrélation
 
Un ID de corrélation est généré lorsque vous créez un cloud privé ou une ressource quelconque dans Azure. Chaque déploiement d’Azure Resource Manager génère également un ID de corrélation. Cet ID permet d’accélérer la création et la résolution d’une demande de service. 
 
Voici un exemple de la sortie d’un déploiement de cloud privé ayant échoué, avec l’ID de corrélation mis en surbrillance.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Échec du déploiement du cloud privé avec l’ID de corrélation.":::

Copiez et enregistrez cet ID de corrélation pour l’inclure dans la demande de service. Pour plus d’informations, consultez [Créer votre demande de support](#create-your-support-request) à la fin de cet article.

Si l’échec se produit lors des étapes de prévalidation, aucun ID de corrélation n’est généré. Dans ce cas, vous pouvez fournir les informations que vous avez utilisées lors de la création du cloud privé Azure VMware Solution, à savoir :

- Emplacement
- Resource group
- Nom de la ressource
 
### <a name="collect-a-summary-of-errors"></a>Collecter un résumé des erreurs

Les détails des erreurs peuvent également vous aider à résoudre votre problème. Dans l’écran précédent, sélectionnez le message d’avertissement pour afficher un résumé des erreurs.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Résumé des options.":::

Recopiez et réenregistrez ce résumé pour l’inclure dans la demande de service.
 
### <a name="retrieve-past-deployments"></a>Récupérer les déploiements passés

Vous pouvez récupérer les déploiements passés, notamment ceux qui ont échoué, en effectuant une recherche dans le journal d’activité de déploiement auquel vous accédez en sélectionnant l’icône des notifications.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Ouvrez les notifications.":::

Dans Notifications, sélectionnez **Plus d’événements dans le journal d’activité**.

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Lien : Plus d’événements dans le journal d’activité.":::

Recherchez ensuite le nom de la ressource ou d’autres informations utilisées pour créer la ressource afin de trouver le déploiement qui a échoué et son ID de corrélation. L’exemple suivant montre les résultats de recherche sur une ressource de cloud privé (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Recherchez les déploiements de d’Azure VMware Solution ayant échoué dans le passé.":::
 
Si vous sélectionnez le nom d’opération du déploiement qui a échoué, une fenêtre contenant des détails s’ouvre. Sélectionnez l’onglet JSON et recherchez correlationId. Copiez et incluez dans la demande de service. 
 
## <a name="collect-the-expressroute-id-uri"></a>Collecter l’ID ExpressRoute (URI)
 
Il se peut que vous tentiez de mettre à l’échelle ou d’appairer un cloud privé existant avec le circuit ExpressRoute de cloud privé, et que cela échoue. Dans ce cas, vous avez besoin de l’ID ExpressRoute. 

Dans le portail Azure, sélectionnez **Connectivité > ExpressRoute** , puis copiez l’ **ID ExpressRoute** dans le Presse-papiers.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Copiez l’ID ExpressRoute dans le presse-papiers."::: 
 
> [!NOTE]
> Les vérifications de prévalidation peuvent échouer avant un déploiement et les seules informations disponibles sont les messages d’erreur et d’échec. Elles peuvent être utiles dans de nombreux cas d’échec, par exemple, pour des problèmes de quota, et il est important d’inclure ces messages dans la demande de support. Pour les collecter, consultez la section précédente, [Collecter un résumé des erreurs](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Créer votre demande de support

Pour obtenir des conseils généraux sur la création d’une demande de support, consultez [Comment créer une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Voici des conseils spécifiques pour la création d’une demande de support pour les échecs d’approvisionnement ou de déploiement d’Azure VMware Solution.

1. Sélectionnez l’icône **Aide** puis **+ Nouvelle demande de support**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Collectez un ID ExpressRoute pour votre DS.":::

2. Renseignez tous les champs obligatoires et, sous l’onglet **Informations de base**  :

    - Pour **Type de problème** , sélectionnez **Problèmes de configuration et d’installation**.

    - Pour **Sous-type de problème** , sélectionnez **Configurer un cloud privé**.

3. Sous l’onglet **Détails**  :

    - Renseignez tous les champs obligatoires.

    - Collez votre ID de corrélation ou ExpressRoute dans les champs spécifiques fournis. Si vous ne voyez pas les champs spécifiques, vous pouvez les coller dans la zone de texte sous **Fournissez des détails sur le problème**.

    - Collez les détails de l’erreur, notamment le résumé des erreurs que vous avez copié, dans la zone de texte sous **Fournissez des détails sur le problème.**

4. Vérifiez, puis sélectionnez **Créer** pour créer votre demande de service.
