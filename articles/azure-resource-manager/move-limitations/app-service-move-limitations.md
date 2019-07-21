---
title: Déplacer des ressources Azure App Service vers un nouveau groupe de ressources ou abonnement
description: Utilisez Azure Resource Manager pour déplacer des ressources App Service vers un nouveau groupe de ressources ou abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: c1a09ff4c29a2fedfea2c165a95c042985b3c83a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723373"
---
# <a name="move-guidance-for-app-service-resources"></a>Conseils de déplacement de ressources App Service

Les étapes de déplacement des ressources App Service diffèrent selon que les ressources sont déplacées au sein d’un abonnement ou vers un nouvel abonnement.

## <a name="move-in-same-subscription"></a>Déplacer dans le même abonnement

Lorsque vous déplacez une application web _au sein du même abonnement_, vous ne pouvez pas déplacer les certificats SSL tiers. Toutefois, vous pouvez déplacer une application web vers le nouveau groupe de ressources sans emporter son certificat tiers, et la fonctionnalité SSL de votre application peut continuer de fonctionner.

Si vous souhaitez déplacer le certificat SSL avec l’application web, suivez ces étapes :

1. Supprimer le certificat tiers de l’application web, mais en conserver une copie
2. Déplacer l’application web
3. Charger le certificat tiers sur l’application web déplacée.

## <a name="move-across-subscriptions"></a>Déplacer entre différents abonnements

Lors du déplacement d’une application Web _entre des abonnements_, les limites suivantes s’appliquent :

- Le groupe de ressources de destination ne doit avoir aucune ressource App Service existante. Les ressources App Service comprennent :
    - Web Apps
    - Plans App Service
    - Certificats SSL chargés ou importés
    - Environnements App Service
- Toutes les ressources App Service du groupe de ressources doivent être déplacées simultanément.
- Les ressources App Service ne peuvent être déplacées qu’à partir du groupe de ressources dans lequel elles ont été créées à l’origine. Si une ressource App Service ne se trouve plus dans son groupe de ressources d’origine, redéplacez-la vers ce groupe. Déplacez ensuite la ressource entre les abonnements.

Si vous ne connaissez pas le groupe de ressources d’origine, recherchez-le dans les diagnostics. Pour votre application web, sélectionnez **Diagnostiquer et résoudre les problèmes**. Ensuite, sélectionnez **Configuration et gestion**.

![Sélectionner les diagnostics](./media/app-service-move-limitations/select-diagnostics.png)

Sélectionnez **Options de migration**.

![Sélectionner les options de migration](./media/app-service-move-limitations/select-migration.png)

Sélectionnez l’option des étapes recommandées pour déplacer l’application web.

![Sélectionner les étapes recommandées](./media/app-service-move-limitations/recommended-steps.png)

Vous voyez alors les actions recommandées à faire avant de déplacer les ressources. Les informations indiquent le groupe de ressources d’origine de l’application web.

![Recommandations](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Déplacer App Service Certificate

Vous pouvez déplacer votre App Service Certificate vers un nouveau groupe de ressources ou abonnement. Si votre App Service Certificate est lié à une application web, vous devez prendre certaines mesures avant de déplacer les ressources vers un nouvel abonnement. Supprimez la liaison SSL et le certificat privé de l’application web avant de déplacer les ressources. L’App Service Certificate n’a pas besoin d’être supprimé, seulement le certificat privé de l’application web.

## <a name="move-support"></a>Prise en charge du déplacement

Pour connaître les ressources App Service pouvant être déplacées, consultez l'état de la prise en charge pour :

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../resource-group-move-resources.md).
