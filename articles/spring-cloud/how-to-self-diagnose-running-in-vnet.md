---
title: Comment auto-diagnostiquer le réseau virtuel Azure Spring Cloud
description: Découvrez comment auto-diagnostiquer et résoudre les problèmes dans Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877358"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>Auto-diagnostic Azure Spring Cloud dans le réseau virtuel
Le diagnostic Azure Spring Cloud prend en charge les applications de dépannage interactives exécutées dans des réseaux virtuels sans configuration. La fonctionnalité de diagnostic Azure Spring Cloud identifie les incidents et vous guide vers les informations qui vous aident au dépannage et à la résolution des problèmes.

## <a name="navigate-to-the-diagnostics-page"></a>Accédez à la page Diagnostics.
La procédure suivante démarre les diagnostics pour les applications en réseau.
1. Connectez-vous au portail Azure.
1. Accédez à la page Vue d’ensemble de votre instance Azure Spring Cloud.
1. Dans le volet de navigation de gauche, sélectionnez **Diagnostiquer et résoudre les problèmes**.
1. Sélectionnez la troisième catégorie, **Mise en réseau**.

   ![Titre de l’auto-diagnostic](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>Afficher un rapport de diagnostic
Une fois que vous avez cliqué sur la catégorie **Mise en réseau**, vous pouvez voir deux problèmes liés à la mise en réseau spécifique à votre réseau virtuel injecté Azure Spring Cloud : la **résolution DNS** et **le trafic sortant requis**.

   ![Options d’auto-diagnostic](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

Sélectionnez votre problème cible pour afficher le rapport de diagnostic. Un résumé des diagnostics s’affiche, par exemple : 

* *La ressource a été supprimée.*
* *La ressource n’est pas déployée dans votre propre réseau virtuel*.

Certains résultats contiennent de la documentation connexe. Les différents sous-réseaux affichent les résultats séparément.

## <a name="dns-resolution"></a>Résolution DNS 
Si vous sélectionnez **Résolution DNS**, les résultats indiquent s’il existe des problèmes DNS avec les applications.  Les applications saines sont répertoriées comme suit :

* *Problèmes DNS résolus sans problème dans le sous-réseau « subnet01 »* .
* *Problèmes DNS résolus sans problème dans le sous-réseau « subnet02 »* .

L’exemple de rapport de diagnostic suivant indique que l’intégrité de l’application est inconnue. Le délai d’exécution de rapports n’inclut pas l’heure à laquelle l’état d’intégrité a été signalé.  Supposons que l’heure de fin du contexte est *2021-03-03T04:20:00Z*. Le TIMESTAMP le plus récent dans les **rendus de la table de résolution DNS** est *2021-03-03T03:39:00Z*, le jour précédent. Le journal de contrôle d’intégrité n’a peut-être pas été envoyé en raison d’un blocage du réseau. 

Les résultats de l’état d’intégrité inconnu contiennent de la documentation.  Vous pouvez cliquer sur le crochet gauche pour afficher la liste déroulante.

   ![DNS inconnu](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

Si vous avez mal configuré votre jeu d’enregistrements de zone DNS privé, vous obtiendrez un résultat critique tel que : `Failed to resolve the Private DNS in subnet xxx`. 

Dans le menu déroulant **Rendus de la table de résolution DNS**, vous trouverez les informations détaillées du message à partir desquelles vous pouvez vérifier votre configuration.

## <a name="required-outbound-traffic"></a>Trafic sortant requis 

Si vous sélectionnez **Trafic sortant requis**, les résultats indiquent s’il existe des problèmes de trafic sortant avec les applications.  Les applications saines sont répertoriées comme suit :

* *Trafic sortant requis résolu sans problème dans le sous-réseau « subnet01 ».
* *Trafic sortant requis résolu sans problème dans le sous-réseau « subnet02 ».

Si un sous-réseau est bloqué par un groupe de sécurité réseau ou des règles de pare-feu et que vous n’avez pas bloqué le journal, les échecs suivants se produisent. Vous pouvez vérifier si vous avez négligé les [Responsabilités du client](spring-cloud-vnet-customer-responsibilities.md).
    
   ![Échec du point de terminaison](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

Si aucune donnée n’est présente dans le `Required Outbound Traffic Table Renderings` dans les 30 minutes, le résultat sera `health status unknown`. Votre réseau est peut-être bloqué ou le service de journalisation est inactif.

   ![Point de terminaison de diagnostic inconnu](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>Voir aussi
* [Comment auto-diagnostiquer Azure Spring Cloud](spring-cloud-howto-self-diagnose-solve.md)