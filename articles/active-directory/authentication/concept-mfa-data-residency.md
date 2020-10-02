---
title: Résidence des données Azure Multi-Factor Authentication
description: Découvrez quelles données personnelles et organisationnelles Azure Multi-Factor Authentication conserve sur vous et vos utilisateurs, et quelles données restent dans le pays/la région d’origine.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e5d8dc60ee0a1f4742382b1cec8ef3ed60e8fb3
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90970665"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Résidence des données et données client pour Azure Multi-Factor Authentication

Les données client sont stockées par Azure AD dans un emplacement géographique en fonction de l’adresse fournie par votre organisation lors de l’abonnement à un service Microsoft Online tel que Microsoft 365 ou Azure. Pour obtenir des informations sur le lieu de stockage des données client, vous pouvez utiliser la section [Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) du Centre de gestion de la confidentialité Microsoft.

Azure Multi-Factor Authentication basé sur le cloud et Azure Multi-Factor Authentication Server traitent et stockent un certain nombre de données personnelles et de données organisationnelles. Cet article présente ces données et indique où elles sont stockées.

Le service Azure Multi-Factor Authentication dispose de centres de données aux États-Unis, en Europe et en Asie-Pacifique. Les activités suivantes proviennent des centres de données régionaux, sauf mention contraire :

* Les authentifications multifacteur s’appuyant sur des appels téléphoniques sont issues de centres de données des États-Unis et sont routées par des fournisseurs globaux.
* Les requêtes d'authentification d'utilisateurs à usage général provenant d'autres régions comme l'Europe ou l'Australie sont actuellement traitées à l’emplacement de l’utilisateur.
* Les notifications Push qui utilisent l’application Microsoft Authenticator proviennent des centres de données régionaux selon l’emplacement de l’utilisateur.
    * Les services spécifiques aux fournisseurs d’appareils, comme les notifications push Apple, peuvent se trouver en dehors de l’emplacement de l’utilisateur.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Données personnelles stockées par Azure Multi-Factor Authentication

Les données personnelles représentent des informations au niveau de l'utilisateur associées à une personne spécifique. Les banques de données suivantes contiennent des informations personnelles :

* Utilisateurs bloqués
* Utilisateurs ignorés
* Demandes de modification du jeton de l’appareil Microsoft Authenticator
* Rapports d’activité Multi-Factor Authentication
* Activations Microsoft Authenticator

Ces informations sont conservées pendant 90 jours.

Azure Multi-Factor Authentication ne conserve pas les données personnelles telles que le nom d'utilisateur, le numéro de téléphone ou l'adresse IP, mais un paramètre *UserObjectId* identifie les tentatives Authentification Multi-Factor pour les utilisateurs. Les données de journal sont stockées pendant 30 jours.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Pour les clouds publics Azure, à l'exclusion de l'authentification Azure B2C, de l'extension NPS et de l'adaptateur Windows Server 2016 ou 2019 AD FS, les données personnelles suivantes sont stockées :

| Type d'événement                           | Type de banque de données |
|--------------------------------------|-----------------|
| Jeton OATH                           | Dans les journaux d’activité Multi-Factor Authentication     |
| SMS unidirectionnel                          | Dans les journaux d’activité Multi-Factor Authentication     |
| Appel vocal                           | Dans les journaux d’activité Multi-Factor Authentication<br />Enregistrement dans le magasin de données d’activité de l’authentification multifacteur<br />Utilisateurs bloqués si fraude signalée |
| Notification Microsoft Authenticator | Dans les journaux d’activité Multi-Factor Authentication<br />Enregistrement dans le magasin de données d’activité de l’authentification multifacteur<br />Utilisateurs bloqués si fraude signalée<br />Demandes de modification en cas de changement du jeton de l’appareil Microsoft Authenticator |

> [!NOTE]
> La banque de données du rapport d'activité Multi-Factor Authentication est stockée aux États-Unis pour tous les clouds, quelle que soit la région qui traite la demande d'authentification. Microsoft Azure Germany, Microsoft Azure Operated by 21Vianet, et Microsoft Government Cloud disposent de leurs propres banques de données indépendantes, distinctes des banques de données de la région du cloud public, mais ces données sont toujours stockées aux États-Unis.

Pour Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure Operated by 21Vianet, l’authentification Azure B2C, NPS Extension, et l’adaptateur Windows Server 2016 ou 2019 AD FS, les données personnelles suivantes sont stockées :

| Type d'événement                           | Type de banque de données |
|--------------------------------------|-----------------|
| Jeton OATH                           | Dans les journaux d’activité Multi-Factor Authentication<br />Enregistrement dans le magasin de données d’activité de l’authentification multifacteur |
| SMS unidirectionnel                          | Dans les journaux d’activité Multi-Factor Authentication<br />Enregistrement dans le magasin de données d’activité de l’authentification multifacteur |
| Appel vocal                           | Dans les journaux d’activité Multi-Factor Authentication<br />Enregistrement dans le magasin de données d’activité de l’authentification multifacteur<br />Utilisateurs bloqués si fraude signalée |
| Notification Microsoft Authenticator | Dans les journaux d’activité Multi-Factor Authentication<br />Enregistrement dans le magasin de données d’activité de l’authentification multifacteur<br />Utilisateurs bloqués si fraude signalée<br />Demandes de modification en cas de changement du jeton de l’appareil Microsoft Authenticator |

### <a name="multi-factor-authentication-server"></a>Serveur Multi-Factor Authentication

Si vous déployez et exécutez Azure Multi-Factor Authentication Server, les données personnelles suivantes sont stockées :

> [!IMPORTANT]
> À compter du 1er juillet 2019, Microsoft ne propose plus Multi-Factor Authentication Server pour les nouveaux déploiements. Les nouveaux clients qui veulent demander à leurs utilisateurs de procéder à une authentification multifacteur doivent utiliser la fonction Azure Multi-Factor Authentication basée sur le cloud. Les clients existants qui ont activé Multi-Factor Authentication Server avant le 1er juillet peuvent télécharger la dernière version et les futures mises à jour, et générer des informations d’identification d’activation comme d’habitude.

| Type d'événement                           | Type de banque de données |
|--------------------------------------|-----------------|
| Jeton OATH                           | Dans les journaux d’activité Multi-Factor Authentication<br />Enregistrement dans le magasin de données d’activité de l’authentification multifacteur |
| SMS unidirectionnel                          | Dans les journaux d’activité Multi-Factor Authentication<br />Enregistrement dans le magasin de données d’activité de l’authentification multifacteur |
| Appel vocal                           | Dans les journaux d’activité Multi-Factor Authentication<br />Enregistrement dans le magasin de données d’activité de l’authentification multifacteur<br />Utilisateurs bloqués si fraude signalée |
| Notification Microsoft Authenticator | Dans les journaux d’activité Multi-Factor Authentication<br />Enregistrement dans le magasin de données d’activité de l’authentification multifacteur<br />Utilisateurs bloqués si fraude signalée<br />Demandes de modification en cas de changement du jeton de l’appareil Microsoft Authenticator |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Données organisationnelles stockées par Azure Multi-Factor Authentication

Les données organisationnelles représentent des informations au niveau du locataire qui pourraient exposer la configuration ou la configuration de l'environnement. Les paramètres de locataire provenant des pages Multi-Factor Authentication du portail Azure suivantes peuvent stocker des données organisationnelles telles que les seuils de verrouillage ou les informations d'identification de l'appelant pour les demandes d'authentification téléphonique entrantes :

* Verrouillage de compte
* Alerte de fraude
* Notifications
* Paramètres de l’appel téléphonique

Et pour Azure Multi-Factor Authentication Server, les pages du portail Azure suivantes peuvent contenir des données organisationnelles :

* Paramètres du serveur
* Contournement à usage unique
* Règles de mise en cache
* État du serveur Multi-Factor Authentication

## <a name="log-data-location"></a>Emplacement des données de journal

L’emplacement où sont stockées les informations du journal dépend de la région dans laquelle elles sont traitées. La plupart des zones géographiques ont des capacités Azure Multi-Factor Authentication natives ; par conséquent, les données de journal sont stockées dans la même région qui traite la requête Multi-Factor Authentication. Dans les zones géographiques sans prise en charge native d'Azure Multi-Factor Authentication, ces données sont approvisionnées par les zones géographiques des États-Unis ou de l'Europe et les données de journal sont stockées dans la même région qui traite la requête Multi-Factor Authentication.

Certaines données du journal d'authentification de base ne sont stockées qu'aux États-Unis. Microsoft Azure Germany et Microsoft Azure Operated by 21Vianet sont toujours stockés dans leur cloud respectif. Les données du journal Microsoft Government Cloud sont toujours stockées aux États-Unis.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les informations utilisateur collectées par Azure Multi-Factor Authentication basé sur le cloud et Azure Multi-Factor Authentication Server, consultez [Collecte de données utilisateur via Azure Multi-Factor Authentication](howto-mfa-reporting-datacollection.md).
