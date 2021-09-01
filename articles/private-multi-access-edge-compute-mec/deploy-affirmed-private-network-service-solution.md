---
title: Déployer Affirmed Private Network Service sur Azure
description: Découvrez comment déployer la solution Affirmed Private Network Service sur Azure
author: KumudD
ms.service: private-multi-access-edge-compute-mec
ms.topic: how-to
ms.date: 06/16/2021
ms.author: hollycl
ms.openlocfilehash: d3c027a7d4006a947f520dee8406e40a30f1b304
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112458449"
---
# <a name="deploy-affirmed-private-network-service-on-azure"></a>Déployer Affirmed Private Network Service sur Azure

Cet article fournit une vue d’ensemble du processus de déploiement de la solution APNS (Affirmed Private Network Service) sur un appareil Azure Stack Edge via la Place de marché Microsoft Azure.

Le diagramme suivant illustre l’architecture système de Affirmed Private Network Service, y compris les ressources requises pour le déploiement.

![Déploiement Affirmed Private Network Service](media/deploy-affirmed-private-network-service/deploy-affirmed-private-network-service.png)

## <a name="collect-required-information"></a>Collecter les informations nécessaires

pour déployer APNS, vous devez disposer des ressources suivantes :

- Un Gestionnaire de fonction réseau Azure configuré - Un objet d’appareil qui sert de jumeau numérique de l’appareil Azure Stack Edge. 

- Une instance Azure Stack Edge entièrement déployée avec une machine virtuelle NetFoundry. 

- Approbation de l’abonnement pour l’offre de machines virtuelles Affirmed Management Systems et l’application managée APNS. 

- Un compte Azure avec un abonnement actif et un accès aux éléments suivants :  

    - Le rôle intégré **Propriétaire** pour votre groupe de ressources. 

    - Le rôle intégré **Contributeur d’applications managées** pour votre abonnement. 

    - Un réseau virtuel et un sous-réseau à joindre (ouvrez les ports TCP/443 et TCP/8443). 

    - 5 adresses IP sur le sous-réseau virtuel. 

    - Un jeton SAS valide fourni par Affirmed Release Engineering.  

    - Nom d’utilisateur/mot de passe d’administration à programmer pendant le déploiement. 
    
## <a name="deploy-apns"></a>Déployer APNS

Pour déployer automatiquement l’application managée APNS avec toutes les ressources requises et les informations pertinentes nécessaires, sélectionnez l’application managée APNS à partir de la Place de marché Microsoft Azure. Lorsque vous déployez APNS, toutes les ressources requises sont automatiquement créées pour vous et sont contenues dans un groupe de ressources managé.

Procédez comme suit pour déployer APNS :
1.  Ouvrez le portail Azure et sélectionnez **Créer une ressource**.
2.  Entrez *APNS* dans la barre de recherche et appuyez sur Entrée.
3.  Sélectionnez **Voir les offres privées**. 
    > [!NOTE]
    > L’application managée APNS n’apparaîtra pas jusqu’à ce que vous sélectionniez **Afficher les offres privées**.
4.  Sélectionnez **Créer** dans le menu déroulant d’**Offre privée**, puis sélectionnez l’option à déployer.
5.  Terminez la configuration de l’application, les paramètres réseau, puis vérifiez et créez.
6.  Sélectionnez **Déployer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des instructions étape par étape sur le déploiement d’APNS et la configuration des paramètres NetFoundry sur un appareil Azure Stack Edge, consultez le [Guide de déploiement d’Affirmed Private Network Service](https://go.microsoft.com/fwlink/?linkid=2165732).
- Pour des informations concernant le portail programmatique basé sur une interface graphique que les opérateurs utilisent pour déployer, surveiller et gérer les réseaux centraux mobiles privés, consultez [Guide de l’utilisateur du gestionnaire Affirmed Private Network Service](https://go.microsoft.com/fwlink/?linkid=2165932).
- En savoir plus sur [Affirmed Private Network Service sur Azure](affirmed-private-network-service-overview.md).
