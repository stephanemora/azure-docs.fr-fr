---
title: Basculer entre plusieurs points de terminaison Azure CDN avec Traffic Manager
description: Découvrez comment configurer Azure Traffic Manager avec des points de terminaison Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 3d4f77a0fb9b8005729a6e9d35f254eb522b690e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259848"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Configurer le basculement entre plusieurs points de terminaison Azure CDN avec Azure Traffic Manager

Lorsque vous configurez Azure Content Delivery Network (CDN), vous pouvez sélectionner le fournisseur optimal et le niveau tarifaire adapté à vos besoins. Avec son infrastructure distribuée globalement, Azure CDN crée par défaut une redondance locale et géographique, ainsi qu’un équilibrage de charge global pour améliorer les performances et la disponibilité du service. Si un emplacement n’est pas disponible pour traiter le contenu, les requêtes sont automatiquement acheminées vers un autre emplacement, et le point de présence optimal (en fonction de facteurs tels que l’emplacement de la requête et la charge du serveur) est utilisé pour traiter chaque requête client. 
 
Si vous avez plusieurs profils CDN, vous pouvez améliorer davantage la disponibilité et les performances avec Azure Traffic Manager. Vous pouvez utiliser Azure Traffic Manager avec Azure CDN pour équilibrer la charge parmi plusieurs points de terminaison CDN à des fins de basculement, d’équilibrage de charge géolocalisé et d’autres scénarios. Dans un scénario classique de basculement, toutes les requêtes client sont d’abord dirigés vers le profil CDN principal. Si ce profil n’est pas disponible, les requêtes sont ensuite transmises au profil CDN secondaire jusqu’à ce que le profil CDN principal soit à nouveau en ligne. Une telle utilisation d’Azure Traffic Manager garantit que votre application web est toujours disponible. 

Cet article fournit des conseils et un exemple montrant comment configurer le basculement avec les profils **Azure CDN Standard fourni par Verizon** et **Azure CDN Standard fourni par Akamai**.

## <a name="set-up-azure-cdn"></a>Configurer Azure CDN 
Créez au moins deux points de terminaison et profils Azure CDN avec des fournisseurs différents.

1. Créez un profil **Azure CDN Standard fourni par Verizon** et un profil **Azure CDN Standard fourni par Akamai** en suivant les étapes décrites pour la [création d’un profil CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN - Plusieurs profils](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Dans chacun des nouveaux profils, créez au moins un point de terminaison en suivant les étapes décrites pour la [création d’un point de terminaison CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Configurer Azure Traffic Manager
Créez un profil Azure Traffic Manager et configurez l’équilibrage de charge sur vos points de terminaison CDN. 

1. Créez un profil Azure Traffic Manager en suivant les étapes décrites pour la [création d’un profil Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    Pour **Méthode de routage**, sélectionnez **Priorité**.

2. Ajoutez vos points de terminaison CDN dans votre profil Traffic Manager en suivant les étapes décrites pour l’[ajout de points de terminaison Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints).

    Pour **Type**, sélectionnez **Points de terminaison externes**. Pour **Priorité**, entrez un nombre.

    Par exemple, créez *cdndemo101akamai.azureedge.net* avec une priorité de *1* et *cdndemo101verizon.azureedge.net* avec une priorité de *2*.

   ![CDN - Points de terminaison Traffic Manager](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Configurer un domaine personnalisé sur Azure CDN et Azure Traffic Manager
Après avoir configuré vos profils CDN et Traffic Manager, suivez ces étapes pour ajouter le mappage DNS et inscrire un domaine personnalisé pour les points de terminaison CDN. Pour cet exemple, le nom de domaine personnalisé est *cdndemo101.dustydogpetcare.online*.

1. Accédez au site web du fournisseur de votre domaine personnalisé, tel que GoDaddy, et créez deux entrées DNS CNAME. 

    a. Pour la première entrée CNAME, mappez votre domaine personnalisé, avec le sous-domaine cdnverify, au point de terminaison CDN. Cette entrée est une étape obligatoire pour inscrire le domaine personnalisé auprès du point de terminaison CDN que vous avez ajouté à Traffic Manager à l’étape 2.

      Par exemple : 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Pour la seconde entrée CNAME, mappez votre domaine personnalisé, sans le sous-domaine cdnverify, au point de terminaison CDN. Cette entrée mappe le domaine personnalisé à Traffic Manager. 

      Par exemple : 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Si votre domaine est actuellement en ligne et ne peut pas être interrompu, effectuez cette étape en dernier. Vérifiez que les points de terminaison CDN les et domaines de Traffic Manager sont actifs avant de mettre à jour votre DNS de domaine personnalisé sur Traffic Manager.
    >


2.  À partir de votre profil Azure CDN, sélectionnez le premier point de terminaison CDN (Akamai). Sélectionnez **Ajouter un domaine personnalisé** et saisissez *cdndemo101.dustydogpetcare.online*. Vérifiez que la coche validant le domaine personnalisé est verte. 

    Azure CDN utilise le sous-domaine *cdnverify* pour valider le mappage DNS et terminer ce processus d’inscription. Pour plus d’informations, voir [Créer un enregistrement CNAME DNS](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Cette étape permet à Azure CDN de reconnaître le domaine personnalisé afin de répondre à ses requêtes.
    
    > [!NOTE]
    > Pour activer TLS sur des profils **Azure CDN d’Akamai**, vous devez définir directement le cname du domaine personnalisé sur votre point de terminaison. L’utilisation de cdnverify pour l’activation de TLS n’est pas encore prise en charge. 
    >

3.  Retournez sur le site web du fournisseur de votre domaine personnalisé et mettez à jour le premier mappage DNS, que vous avez créé afin que le domaine personnalisé soit mappé à votre deuxième point de terminaison CDN.
                             
    Par exemple : 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. À partir de votre profil Azure CDN, sélectionnez le deuxième point de terminaison CDN (Verizon) et répétez l’étape 2. Sélectionnez **Ajouter un domaine personnalisé** et saisissez *cdndemo101.dustydogpetcare.online*.
 
Après avoir effectué ces étapes, votre service multi-CDN avec des fonctionnalités de basculement est configuré avec Azure Traffic Manager. Vous aurez accès aux URL de test à partir de votre domaine personnalisé. Pour tester la fonctionnalité, désactivez le point de terminaison CDN principal et vérifiez que la requête est correctement déplacée sur le point de terminaison CDN secondaire. 

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez également configurer d’autres méthodes de routage, telles que le routage géographique, pour équilibrer la charge entre les différents points de terminaison CDN. Pour plus d’informations, voir [Configurer la méthode de routage du trafic géographique à l’aide de Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



