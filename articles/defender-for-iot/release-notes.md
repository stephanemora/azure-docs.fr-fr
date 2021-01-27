---
title: Nouveautés d’Azure Defender pour IoT
description: Cet article vous présente les nouveautés de la dernière version de Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/06/2021
ms.author: shhazam
ms.openlocfilehash: 72a6e50134647194679055a886b50b01f42e212d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629977"
---
# <a name="whats-new"></a>Nouveautés

Defender pour IoT 10.0 fournit des améliorations de fonctionnalités qui renforcent la sécurité et optimisent la gestion ainsi que la convivialité.

## <a name="security"></a>Sécurité

Des améliorations ont été apportées à la récupération des certificats et des mots de passe pour cette version.

### <a name="certificates"></a>Certificats
  
Cette version vous permet de :

- Charger les certificats SSL directement dans les capteurs et les consoles de gestion locales.
- Effectuer la validation entre la console de gestion locale et les capteurs connectés, et entre une console de gestion et une console de gestion haute disponibilité. La validation repose sur les dates d’expiration, l’authenticité de l’autorité de certification racine et les listes de révocation de certificats.  Si la validation échoue, la session est interrompue.

Pour les mises à niveau :

- Aucune modification n’est apportée au certificat SSL ou à la fonctionnalité de validation lors de la mise à niveau.
- Après la mise à niveau, les utilisateurs administratifs de la console de gestion locale et du capteur peuvent remplacer les certificats SSL ou activer la validation de certificat SSL à partir de la fenêtre Paramètres système > Certificat SSL.  

Pour les nouvelles installations :

- À la première connexion, les utilisateurs doivent utiliser un certificat SSL (recommandé) ou un certificat auto-signé généré localement (non recommandé)
- La validation des certificats est activée par défaut pour les nouvelles installations.

### <a name="password-recovery"></a>Récupération de mot de passe
  
Les utilisateurs administratifs de la console de gestion locale et du capteur peuvent désormais récupérer les mots de passe à partir du portail Azure Defender pour IoT. La récupération du mot de passe nécessitait auparavant l’intervention de l’équipe du support technique.

## <a name="onboarding"></a>Mise en route

### <a name="on-premises-management-console---committed-devices"></a>Console de gestion locale - appareils validés

Après la connexion initiale à la console de gestion locale, les utilisateurs doivent désormais charger un fichier d’activation. Le fichier contient le nombre agrégé d’appareils à surveiller sur le réseau de l’organisation. Ce nombre est désigné sous le terme de nombre d’appareils validés.
Les appareils validés sont définis au cours du processus d’intégration sur le portail Azure Defender pour IoT, où le fichier d’activation est généré.
Les nouveaux utilisateurs et ceux qui effectuent une mise à niveau doivent charger le fichier d’activation.
Après l’activation initiale, le nombre d’appareils détectés sur le réseau peut dépasser le nombre d’appareils validés. Cet événement peut se produire, par exemple, si vous connectez d’autres capteurs à la console de gestion. S’il y a une différence entre le nombre d’appareils détectés et le nombre d’appareils validés, un avertissement s’affiche dans la console de gestion. Si cet événement se produit, vous devez charger un nouveau fichier d’activation.

### <a name="pricing-page-options"></a>Options de la page de tarification

La page de tarification vous permet d’intégrer de nouveaux abonnements à Azure Defender pour IoT et de définir les appareils validés dans votre réseau.  
En outre, elle vous permet désormais de gérer les abonnements existants associés à un capteur et de mettre à jour la validation de l’appareil.

### <a name="view-and-manage-onboarded-sensors"></a>Afficher et gérer les capteurs intégrés

Une nouvelle page du portail dédiée au site et aux capteurs vous permet de :

- Ajouter des informations détaillées relatives au capteur. Par exemple, une zone associée au capteur ou des étiquettes de texte libre.
- Afficher et filtrer les informations sur le capteur. Par exemple, affichez des détails sur les capteurs connectés au cloud ou gérés localement, ou des informations sur les capteurs dans une zone spécifique.  

## <a name="usability"></a>Facilité d'utilisation

### <a name="azure-sentinel-new-connector-page"></a>Page du nouveau connecteur Azure Sentinel

La page du connecteur de données Azure Defender pour IoT dans Azure Sentinel a été repensée. Le connecteur de données est désormais basé sur des abonnements plutôt que sur IoT Hubs, ce qui permet aux clients de mieux gérer leur connexion de configuration à Azure Sentinel.

### <a name="azure-portal-permission-updates"></a>Mises à jour des autorisations du portail Azure  

La prise en charge du Lecteur de sécurité et de l’Administrateur de la sécurité a été ajoutée.

## <a name="other-updates"></a>Autres mises à jour

### <a name="access-group---zone-permissions"></a>Groupe d’accès - autorisations de zone
  
Les règles de groupe d’accès à la console de gestion locale n’incluent pas l’option permettant d’accorder l’accès à une zone spécifique. La définition des règles qui utilisent des sites, des régions et des unités commerciales reste inchangée.   Après la mise à niveau, les groupes d’accès qui contenaient des règles autorisant l’accès à des zones spécifiques seront modifiés pour autoriser l’accès au site parent, y compris toutes ses zones.

### <a name="terminology-changes"></a>Changements terminologiques

Le terme ressource a été renommé appareil dans le capteur et la console de gestion locale, les rapports et d’autres interfaces de la solution.
Dans les alertes de la console de gestion locale et du capteur, le terme Gérer cet événement a été nommé Étapes de correction.

## <a name="next-steps"></a>Étapes suivantes

[Bien démarrer avec Defender pour IoT](getting-started.md)
