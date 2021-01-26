---
title: Résidence des données pour l’authentification multifacteur Azure AD
description: Découvrez quelles données personnelles et organisationnelles l’authentification multifacteur Azure AD conserve sur vous et vos utilisateurs, et quelles données restent dans le pays/la région d'origine.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788a666e8ec509bbd29a8dbd503a60b3dddefd6b
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208350"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Résidence des données et données client pour l’authentification multifacteur Azure AD

Les données client sont stockées par Azure AD dans un emplacement géographique en fonction de l’adresse fournie par votre organisation lors de l’abonnement à un service Microsoft Online tel que Microsoft 365 ou Azure. Pour obtenir des informations sur le lieu de stockage des données client, vous pouvez utiliser la section [Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) du Centre de gestion de la confidentialité Microsoft.

L’authentification multifacteur Azure AD basée sur le cloud et le serveur d’authentification multifacteur Azure traitent et stockent un certain nombre de données personnelles et organisationnelles. Cet article présente ces données et indique où elles sont stockées.

Le service d’authentification multifacteur Azure AD dispose de centres de données aux États-Unis, en Europe et en Asie-Pacifique. Les activités suivantes proviennent des centres de données régionaux, sauf mention contraire :

* Les authentifications multifacteur s’appuyant sur des appels téléphoniques sont issues de centres de données des États-Unis et sont routées par des fournisseurs globaux.
* Les requêtes d'authentification d'utilisateurs à usage général provenant d'autres régions comme l'Europe ou l'Australie sont actuellement traitées à l’emplacement de l’utilisateur.
* Les notifications Push qui utilisent l’application Microsoft Authenticator sont actuellement traitées dans les centres de données régionaux en fonction de la localisation de l’utilisateur.
    * Les services spécifiques aux fournisseurs d’appareils, comme les notifications push Apple, peuvent se trouver en dehors de l’emplacement de l’utilisateur.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Données personnelles stockées par l’authentification multifacteur Azure AD

Les données personnelles représentent des informations au niveau de l'utilisateur associées à une personne spécifique. Les banques de données suivantes contiennent des informations personnelles :

* Utilisateurs bloqués
* Utilisateurs ignorés
* Demandes de modification du jeton de l’appareil Microsoft Authenticator
* Rapports d’activité d’authentification multifacteur
* Activations Microsoft Authenticator

Ces informations sont conservées pendant 90 jours.

L’authentification multifacteur Azure AD ne conserve pas de données à caractère personnel telles que le nom d'utilisateur, le numéro de téléphone ou l'adresse IP, mais un paramètre *UserObjectId* identifie les tentatives d'authentification multifacteur aux utilisateurs. Les données de journal sont stockées pendant 30 jours.

### <a name="azure-ad-multifactor-authentication"></a>Authentification multifacteur Azure AD

Pour les clouds publics Azure, à l'exclusion de l'authentification Azure B2C, de l'extension NPS et de l'adaptateur Windows Server 2016 ou 2019 AD FS, les données personnelles suivantes sont stockées :

| Type d'événement                           | Type de banque de données |
|--------------------------------------|-----------------|
| Jeton OATH                           | Dans les journaux d’authentification multifacteur     |
| SMS unidirectionnel                          | Dans les journaux d’authentification multifacteur     |
| Appel vocal                           | Dans les journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur<br />Utilisateurs bloqués si fraude signalée |
| Notification Microsoft Authenticator | Dans les journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur<br />Utilisateurs bloqués si fraude signalée<br />Demandes de modification en cas de changement du jeton de l’appareil Microsoft Authenticator |

Pour Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure Operated by 21Vianet, l’authentification Azure B2C, NPS Extension, et l’adaptateur Windows Server 2016 ou 2019 AD FS, les données personnelles suivantes sont stockées :

| Type d'événement                           | Type de banque de données |
|--------------------------------------|-----------------|
| Jeton OATH                           | Dans les journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur |
| SMS unidirectionnel                          | Dans les journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur |
| Appel vocal                           | Dans les journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur<br />Utilisateurs bloqués si fraude signalée |
| Notification Microsoft Authenticator | Dans les journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur<br />Utilisateurs bloqués si fraude signalée<br />Demandes de modification en cas de changement du jeton de l’appareil Microsoft Authenticator |

### <a name="multifactor-authentication-server"></a>Serveur d’authentification multifacteur

Si vous déployez et exécutez un serveur d’authentification multifacteur Azure AD, les données personnelles suivantes sont stockées :

> [!IMPORTANT]
> Depuis le 1er juillet 2019, Microsoft ne propose plus de serveur d’authentification multifacteur pour les nouveaux déploiements. Les nouveaux clients qui souhaitent imposer une authentification multifacteur à leurs utilisateurs doivent utiliser l’authentification multifacteur Azure AD basée sur le cloud. Les clients existants qui ont activé un serveur d’authentification multifacteur avant le 1er juillet peuvent télécharger la dernière version et les futures mises à jour, et générer des informations d’identification d’activation comme d’habitude.

| Type d'événement                           | Type de banque de données |
|--------------------------------------|-----------------|
| Jeton OATH                           | Dans les journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur |
| SMS unidirectionnel                          | Dans les journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur |
| Appel vocal                           | Dans les journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur<br />Utilisateurs bloqués si fraude signalée |
| Notification Microsoft Authenticator | Dans les journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur<br />Utilisateurs bloqués si fraude signalée<br />Demandes de modification en cas de changement du jeton de l’appareil Microsoft Authenticator |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Données organisationnelles stockées par l’authentification multifacteur Azure AD

Les données organisationnelles représentent des informations au niveau du locataire qui pourraient exposer la configuration ou la configuration de l'environnement. Les paramètres de locataire des pages d’authentification multifacteur du portail Azure suivantes peuvent stocker des données organisationnelles telles que des seuils de verrouillage ou des informations d'identification de l'appelant pour les demandes d'authentification téléphonique entrantes :

* Verrouillage de compte
* Alerte de fraude
* Notifications
* Paramètres de l’appel téléphonique

Et pour un serveur d’authentification multifacteur Azure AD, les pages du portail Azure suivantes peuvent contenir des données organisationnelles :

* Paramètres du serveur
* Contournement à usage unique
* Règles de mise en cache
* État du serveur d’authentification multifacteur

## <a name="multifactor-authentication-logs-location"></a>Emplacement des journaux d’authentification multifacteur

Le tableau suivant indique l’emplacement des journaux de service pour les clouds publics.

| Cloud public| Journaux d’activité de connexion | Rapport d’activité d’authentification multifacteur        | Journaux du service d’authentification multifacteur       |
|-------------|--------------|----------------------------------------|------------------------|
| US          | US           | US                                     | US                     |
| Europe      | Europe       | FR                                     | Europe <sup>2</sup>    |
| Australie   | Australie    | US<sup>1</sup>                         | Australie <sup>2</sup> |

<sup>1</sup> Les journaux de code OATH sont stockés en Australie

<sup>2</sup> Les journaux du service d’authentification multifacteur des appels vocaux sont stockés aux États-Unis

Le tableau suivant montre l’emplacement des journaux de service pour les clouds souverains.

| Cloud souverain                      | Journaux d’activité de connexion                         | Rapport d’activité d’authentification multifacteur (inclut des données à caractère personnel)| Journaux du service d’authentification multifacteur |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Germany              | Allemagne                              | US                            | US               |
| Microsoft Azure opéré par 21Vianet | Chine                                | US                            | US               |
| Cloud de Microsoft pour le Secteur Public           | US                                   | US                            | US               |

Les données du rapport d’activité d’authentification multifacteur contiennent des données à caractère personnel, telles que le nom d’utilisateur principal (UPN) et le numéro de téléphone complet.

Les journaux du service d’authentification multifacteur sont utilisés pour exploiter le service.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur les informations utilisateur collectées par la l’authentification multifacteur Azure AD basée sur le cloud et par le serveur d’authentification multifacteur Azure AD, consultez [Collecte de données utilisateur d’authentification multifacteur Azure AD](howto-mfa-reporting-datacollection.md).
