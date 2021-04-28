---
title: Échanger ou basculer des déploiements dans Azure Cloud Services (support étendu)
description: Découvrez comment échanger des déploiements ou basculer entre eux dans Azure Cloud Services (support étendu).
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f5e01075ffb460c7ddd70b40a6b19f7ea70dd776
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748815"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>Échanger ou basculer des déploiements dans Azure Cloud Services (support étendu)

Vous pouvez basculer entre deux déploiements de service cloud indépendants dans Azure Cloud Services (support étendu). Contrairement à Azure Cloud Services (classique), le modèle Azure Resource Manager d’Azure Cloud Services (support étendu) n’utilise pas d’emplacements de déploiement. Dans Azure Cloud Services (support étendu), lorsque vous déployez une nouvelle version d’un service cloud, vous pouvez rendre le service cloud « échangeable » avec un service cloud existant dans Azure Cloud Services (support étendu).

Une fois que vous avez échangé les déploiements, vous pouvez déplacer votre nouvelle version vers un environnement intermédiaire et la tester à l’aide du nouveau déploiement de service cloud. En effet, l’échange permet de promouvoir un nouveau service cloud intermédiaire vers la version de production.

> [!NOTE]
> Vous ne pouvez pas échanger un déploiement Azure Cloud Services (classique) et un déploiement Azure Cloud Services (support étendu).

Vous devez rendre un service cloud échangeable avec un autre service cloud lorsque vous déployez le deuxième service d’une paire de services cloud.

Vous pouvez échanger les déploiements en utilisant un modèle Azure Resource Manager (modèle ARM), le portail Azure ou l’API REST.

## <a name="arm-template"></a>Modèle ARM

Si vous utilisez une méthode de déploiement par modèle ARM, pour rendre les services cloud échangeables, définissez la propriété `SwappableCloudService` dans `networkProfile` dans l’objet `cloudServices` sur l’ID du service cloud associé :

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>Portail Azure

Pour échanger un déploiement sur le portail Azure :

1. Dans le menu du portail, sélectionnez **Azure Cloud Services (support étendu)** ou **Tableau de bord**.
1. Sélectionnez le service cloud que vous souhaitez mettre à jour.
1. Dans **Vue d’ensemble** du service cloud, sélectionnez **Échanger** :

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="Capture d’écran montrant l’onglet Échanger pour le service cloud.":::

1. Dans le volet de confirmation de l’échange, vérifiez les informations des déploiements, puis sélectionnez **OK** pour échanger les déploiements :

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="Capture d’écran montrant la confirmation des informations de l’échange de déploiements.":::

Les déploiements s’échangent rapidement, car la seule chose qui change est l’adresse IP virtuelle du service cloud qui est déployé.

Pour économiser des coûts de calcul, vous pouvez supprimer l’un des services cloud (désigné comme environnement intermédiaire pour le déploiement de votre application) après avoir vérifié que le service cloud échangé fonctionne comme prévu.

## <a name="rest-api"></a>API REST

Pour utiliser l’API REST pour échanger avec un nouveau déploiement de service cloud dans Azure Cloud Services (support étendu), utilisez la commande et la configuration JSON suivantes :

```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```

```json
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
}
```

## <a name="common-questions-about-swapping-deployments"></a>Questions courantes sur l’échange de déploiements

Vérifiez les réponses aux questions courantes sur les échanges de déploiements dans Azure Cloud Services (support étendu).

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>Quelles sont les conditions préalables à l’échange avec un nouveau déploiement de services Cloud ?

Vous devez remplir deux conditions préalables essentielles pour qu’un échange de déploiements réussisse dans Azure Cloud Services (support étendu) :

* Si vous souhaitez utiliser une adresse IP statique ou réservée pour l’un des services cloud échangeables, l’autre service cloud doit également utiliser une adresse IP réservée. Sinon, l’échange échoue.
* Toutes les instances de vos rôles doivent être en cours d’exécution pour que l’échange réussisse. Pour vérifier l’état de vos instances, dans le portail Azure, accédez à la section **Vue d’ensemble** du service cloud nouvellement déployé ou utilisez la commande `Get-AzRole` dans Windows PowerShell.

Les mises à jour du système d’exploitation invité et les opérations de réparation de service peuvent entraîner l’échec d’un échange de déploiements. Pour plus d’informations, consultez [Résoudre les déploiements de service cloud](../cloud-services/cloud-services-troubleshoot-deployment-problems.md).

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Puis-je effectuer un échange d’adresses IP virtuelles simultanément à une autre opération de mutation ?

Non. Un échange d’adresses IP virtuelles est une modification uniquement liée au réseau qui doit être terminée avant le lancement de toute autre opération de calcul sur un service cloud. Le lancement d’une opération de mise à jour, de suppression ou de mise à l’échelle automatique pour un service cloud alors qu’un échange d’adresses IP virtuelles est en cours ou le déclenchement d’un échange d’adresses IP virtuelles alors qu’une autre opération de calcul est en cours peut entraîner une erreur irrécupérable dans le service cloud.

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>Un échange implique-t-il un temps d’arrêt pour mon application, et comment dois-je le gérer ?

L’échange de services cloud est généralement rapide, car il s’agit seulement d’une modification de la configuration de l’équilibreur de charge Azure. Dans certains cas, l’échange peut prendre au moins 10 secondes et entraîner des échecs de connexion temporaires. Pour limiter l’effet de l’échange sur les utilisateurs, envisagez d’implémenter une logique de nouvelle tentative du client.

## <a name="next-steps"></a>Étapes suivantes 

* Consultez les [prérequis du déploiement](deploy-prerequisite.md) d’Azure Cloud Services (support étendu).
* Consultez la [FAQ](faq.md) pour Azure Cloud Services (support étendu).
* Déployez un service cloud Azure Cloud Services (support étendu) en utilisant l’une des options suivantes :
  * [Azure portal](deploy-portal.md)
  * [PowerShell](deploy-powershell.md)
  * [Modèle ARM](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)
