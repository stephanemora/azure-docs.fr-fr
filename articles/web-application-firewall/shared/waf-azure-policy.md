---
title: Pare-feu d’applications web Azure et Azure Policy
description: Le pare-feu d’applications web (WAF) Azure associé à Azure Policy peut aider à appliquer les normes organisationnelles et à évaluer la conformité à l’échelle des ressources WAF
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 564af59c473d5c3f55bef059905500496adf060c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165908"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Pare-feu d’applications web Azure et Azure Policy

Le pare-feu d’applications web (WAF) Azure associé à Azure Policy peut aider à appliquer les normes organisationnelles et à évaluer la conformité à l’échelle des ressources WAF. La stratégie Azure est un outil de gouvernance qui fournit une vue agrégée permettant d’évaluer l’état général de l’environnement, avec la possibilité d’explorer au niveau de chaque ressource et stratégie. La stratégie Azure vous aide également à mettre vos ressources en conformité par le biais de la correction en bloc pour les ressources existantes et de la correction automatique pour les nouvelles ressources.

## <a name="azure-policies-for-web-application-firewall"></a>Stratégies Azure pour le pare-feu d’applications web

Il existe plusieurs stratégies Azure intégrées pour gérer les ressources WAF. La répartition des stratégies et leurs fonctionnalités sont les suivantes :

1. **Le pare-feu d’applications web (WAF) doit être activé pour le service Azure Front Door** : Les services Azure Front Door sont évalués si un WAF est présent sur la création de ressources. La stratégie a trois effets : Audit, refus et désactivation. L’« Audit » permet de suivre les cas où un service Azure Front Door n’a pas de WAF et permet aux utilisateurs de voir ce que le service Azure Front Door ne respecte pas. Le « Refus » empêche la création d’un service Azure Front Door si un WAF n’est pas attaché. La « Désactivation » désactive cette stratégie.

2. **Le pare-feu d’applications web (WAF) doit être activé pour Application Gateway** : Les passerelles d’applications sont évaluées si un WAF est présent sur la création de ressources. La stratégie a trois effets : Audit, refus et désactivation. L’« Audit » permet de suivre les cas où une Application Gateway n’a pas de WAF et permet aux utilisateurs de voir ce que la passerelle d’application ne respecte pas. Le « Refus » empêche la création d’Application Gateway si un WAF n’est pas attaché. La « Désactivation » désactive cette stratégie.

3. **Le pare-feu d’applications web (WAF) doit utiliser le mode spécifié pour le service Azure Front Door** : Impose l’utilisation du mode de « détection » ou de « blocage » pour être actif sur toutes les stratégies de pare-feu d’applications web pour le service Azure Front Door. La stratégie a trois effets : Audit, refus et désactivation. L’« Audit » permet de suivre les cas où un WAF ne correspond pas au mode spécifié. Le « Refus » empêche la création d’un WAF s’il n’est pas dans le bon mode. La « Désactivation » désactive cette stratégie.

4. **Le pare-feu d’applications web (WAF) doit utiliser le mode spécifié pour Application Gateway** : Impose l’utilisation du mode de « détection » ou de « blocage » pour être actif sur toutes les stratégies de pare-feu d’applications web pour Application Gateway. La stratégie a trois effets : Audit, refus et désactivation. L’« Audit » permet de suivre les cas où un WAF ne correspond pas au mode spécifié. Le « Refus » empêche la création d’un WAF s’il n’est pas dans le bon mode. La « Désactivation » désactive cette stratégie.


## <a name="launch-an-azure-policy"></a>Lancement d’Azure Policy


1.  Sur la page d’accueil Azure, taper « Stratégie » dans la barre de recherche, puis cliquer sur l’icône Azure Policy

2.  Dans le service de la stratégie Azure, sous **Création** , sélectionnez **Affectations** .

[!div class="mx-imgBorder"]
![Pare-feu d’applications web (WAF) d’Azure](../media/waf-azure-policy/policy-home.png)

3.  Sur la page « Affectations », sélectionnez l’icône **Attribuer une stratégie** en haut.

[!div class="mx-imgBorder"]
![Capture d’écran montrant l’onglet « niveau De base » sur la page Affecter une stratégie.](../media/waf-azure-policy/assign-policy.png)

4.  Dans l’onglet « Informations de base » de la page « Attribuer une stratégie », mettez à jour les champs suivants :
    1.  **Étendue**  : Sélectionnez les abonnements Azure et les groupes de ressources qui doivent être affectés par Azure Policy.
    2.  **Exclusions** : Sélectionner toutes les ressources de l’étendue à exclure de la stratégie 
    3.  **Définition de la stratégie** : Sélectionnez la stratégie Azure à appliquer à l’étendue avec des exclusions. Tapez « pare-feu d’applications web » dans la barre de recherche pour choisir la stratégie Azure du pare-feu d’applications Web appropriée.

[!div class="mx-imgBorder"]
![Capture d’écran montrant les définitions de stratégie disponible.](../media/waf-azure-policy/policy-listing.png)


5.  Sélectionnez l’onglet **Paramètres** et mettez à jour les paramètres des stratégies. Pour plus de précisions sur la fonction du paramètre, survolez l’icône d’informations à côté du nom du paramètre.

6.  Sélectionnez **Vérifier + créer** pour finaliser votre stratégie Azure. La stratégie Azure prend environ 15 minutes jusqu’à ce qu’elle soit active pour les nouvelles ressources.
