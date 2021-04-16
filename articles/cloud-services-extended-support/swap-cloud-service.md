---
title: Échange/Basculement entre deux services Azure Cloud Services (support étendu)
description: Échange/Basculement entre deux services Azure Cloud Services (support étendu)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 6f96656af9afd9874cc6273a9cea9ed43e8c69cc
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294205"
---
# <a name="swapswitch-between-two-azure-cloud-services-extended-support"></a>Échange/Basculement entre deux services Azure Cloud Services (support étendu)
Cloud Services (support étendu) vous permet de basculer entre deux déploiements de service cloud indépendants. Contrairement à Cloud Services (classique), le concept d’emplacements n’existe pas avec le modèle Azure Resource Manager. Lorsque vous choisissez de déployer une nouvelle version de service cloud (support étendu), vous pouvez la rendre « échangeable » avec un autre service cloud existant (support étendu), ce qui vous permet de préparer et de tester votre nouvelle version à l’aide de ce déploiement. Un service cloud peut être rendu « échangeable » avec un autre service cloud uniquement au moment du déploiement du second service cloud (de la paire). Lors de l’utilisation de la méthode de déploiement basée sur un modèle ARM, cette opération s’effectue en définissant la propriété SwappableCloudService dans le profil réseau de l’objet de service cloud sur l’ID du service cloud associé. 

```
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
```
> [!Note] 
> Vous ne pouvez pas permuter entre un service cloud (classique) et un service cloud (support étendu)

Utilisez **Échanger** pour basculer les URL entre les deux services cloud concernés, en promouvant un nouveau service cloud (intermédiaire) vers la version de production.
Vous pouvez inverser les déploiements à partir de la page Cloud Services ou du tableau de bord.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le service cloud que vous souhaitez mettre à jour. Cette étape ouvre le panneau d'instance de service cloud.
2. Dans le panneau, sélectionnez **Échanger**
   :::image type="content" source="media/swap-cloud-service-1.png" alt-text="Image illustrant l’option d’échange du service cloud":::
   
3. L’invite de confirmation suivante s’ouvre.
   
   :::image type="content" source="media/swap-cloud-service-2.png" alt-text="Image illustrant la permutation du service cloud":::
   
4. Après avoir vérifié les informations de déploiement, sélectionnez OK pour permuter les déploiements.
La permutation se fait rapidement, car la seule chose qui change est l'adresse IP virtuelle (VIP) des deux services cloud.

Pour économiser des coûts de calcul, vous pouvez supprimer l’un des services cloud (désigné comme environnement intermédiaire pour le déploiement de votre application) après avoir vérifié que le service cloud échangé fonctionne comme prévu.

L’API REST permettant d’effectuer un « échange » entre deux déploiements de services cloud (support étendu) est disponible ci-dessous :
```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```
```
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
```
## <a name="common-questions-about-swapping-deployments"></a>Questions courantes sur l’échange de déploiements

### <a name="what-are-the-prerequisites-for-swapping-between-two-cloud-services"></a>Quelles sont les conditions préalables à un échange entre deux services cloud ?
La réussite d’un échange de services cloud (support étendu) repose sur deux conditions préalables :
* Si vous souhaitez utiliser une adresse IP statique/réservée pour l’un des services cloud échangeables, l’autre service cloud doit également utiliser une adresse IP réservée. Sinon, l’échange échoue.
* Toutes les instances de vos rôles doivent être en cours d’exécution pour que vous puissiez effectuer l’échange. Vous pouvez vérifier l’état de vos instances sur le panneau Aperçu du portail Azure. Vous pouvez également utiliser la commande Get-AzRole dans Windows PowerShell.

Les mises à jour du système d’exploitation invité et les opérations de réparation de service peuvent également entraîner l’échec du déploiement. Pour plus d’informations, consultez Résoudre les problèmes de déploiement de service cloud.

### <a name="can-i-perform-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Puis-je échanger des adresses IP virtuelles parallèlement à une autre opération de mutation ?
Non. L’échange d’adresses IP virtuelles relève uniquement d’une modification réseau qui doit être effectuée avant toute autre opération de calcul sur le ou les services cloud. Une opération de mise à jour, de suppression ou de mise à l’échelle automatique sur le ou les services cloud alors qu’un échange d’adresses IP virtuelles est en cours ou le déclenchement d’un échange d’adresses IP virtuelles alors qu’une autre opération de calcul est en cours peut affecter l’état du service cloud, sans récupération possible. 

### <a name="does-a-swap-incur-downtime-for-my-application-how-should-i-handle-it"></a>Un échange implique-t-il un temps d’arrêt pour mon application ? Comment dois-je le gérer ?
Comme décrit dans la section précédente, un échange de services cloud est généralement rapide, car il s’agit simplement d’une modification de configuration dans Azure Load Balancer. Dans certains cas, elle peut prendre au moins 10 secondes et entraîner des échecs de connexion temporaires. Pour limiter l’impact sur vos clients, envisagez d’implémenter la logique de nouvelle tentative client.

## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [prérequis du déploiement](deploy-prerequisite.md) de Cloud Services (support étendu).
- Consultez la [foire aux questions (FAQ)](faq.md) relative à Azure Cloud Services (support étendu).
- Déployez une instance Cloud Services (support étendu) avec le [Portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).
