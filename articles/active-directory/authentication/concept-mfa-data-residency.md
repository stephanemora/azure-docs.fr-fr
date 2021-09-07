---
title: Résidence des données pour l’authentification multifacteur Azure AD
description: Découvrez quelles données personnelles et organisationnelles l’authentification multifacteur Azure AD conserve sur vous et vos utilisateurs, et quelles données restent dans le pays/la région d’origine.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa8098192340505ecb7555f407375690c36ecdfb
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2021
ms.locfileid: "111525801"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Résidence des données et données client pour l’authentification multifacteur Azure AD

Azure Active Directory (Azure AD) stocke les données client dans un emplacement géographique en fonction de l’adresse fournie par une organisation lorsqu’il s’abonne à un service Microsoft Online, comme Microsoft 365 ou Azure. Pour plus d’informations sur l’emplacement de stockage de vos données client, consultez la section [Où se trouvent vos données ?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) dans le Centre de gestion de la confidentialité Microsoft.

L’authentification multifacteur Azure AD basée sur le cloud et le serveur MFA traitent et stockent les données personnelles ainsi que les données organisationnelles. Cet article présente ces données et indique où elles sont stockées.

Le service d’authentification multifacteur Azure AD dispose de centres de données aux États-Unis, en Europe et en Asie-Pacifique. Les activités suivantes proviennent des centres de données régionaux, sauf mention contraire :

* Les appels téléphoniques liés à l’authentification multifacteur proviennent des centres de données de la région du client, et sont routés par des fournisseurs situés dans le monde entier. Les appels téléphoniques utilisant des messages d’accueil personnalisés proviennent toujours de centres de données situés aux États-Unis.
* Les requêtes d’authentification d’utilisateurs à usage général provenant d’autres régions sont actuellement traitées à l’emplacement de l’utilisateur.
* Les notifications Push qui utilisent l’application Microsoft Authenticator sont actuellement traitées dans les centres de données régionaux en fonction de la localisation de l’utilisateur. Les services d’appareil spécifiques au fournisseur, tels qu’Apple Push Notification Service, peuvent être en dehors de l’emplacement de l’utilisateur.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Données personnelles stockées par l’authentification multifacteur Azure AD

Les données personnelles représentent des informations au niveau de l’utilisateur associées à une personne spécifique. Les banques de données suivantes contiennent des informations personnelles :

* Utilisateurs bloqués
* Utilisateurs ignorés
* Demandes de modification du jeton de l’appareil Microsoft Authenticator
* Rapports d’activité d’authentification multifacteur. Stocke l’activité d’authentification multifacteur à partir des composants locaux de l’authentification multifacteur : extension NPS, adaptateur AD FS et serveur MFA.
* Activations Microsoft Authenticator

Ces informations sont conservées pendant 90 jours.

L’authentification multifacteur Azure AD n’enregistre pas de données personnelles, comme les noms d’utilisateur, les numéros de téléphone ou les adresses IP. Toutefois, *UserObjectId* identifie les tentatives d’authentification pour les utilisateurs. Les données de journal sont stockées pendant 30 jours.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Données stockées par l’authentification multifacteur Azure AD

Pour les clouds publics Azure, à l’exception de l’authentification Azure AD B2C, de l’extension NPS et de l’adaptateur de services de fédération Active Directory (AD FS) Windows Server 2016 ou 2019, les données personnelles suivantes sont stockées :

| Type d'événement                           | Type de banque de données |
|--------------------------------------|-----------------|
| Jeton OATH                           | Journaux d’authentification multifacteur     |
| SMS unidirectionnel                          | Journaux d’authentification multifacteur     |
| Appel vocal                           | Journaux d’authentification multifacteur<br/>Magasin de données d’activité d’authentification multifacteur<br/>Utilisateurs bloqués (si fraude signalée) |
| Notification Microsoft Authenticator | Journaux d’authentification multifacteur<br/>Magasin de données d’activité d’authentification multifacteur<br/>Utilisateurs bloqués (si fraude signalée)<br/>Demandes de modification en cas de changement du jeton de l’appareil Microsoft Authenticator |

Pour Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure Operated by 21Vianet, l’authentification Azure AD B2C, NPS Extension, et l’adaptateur Windows Server 2016 ou 2019 AD FS, les données personnelles suivantes sont stockées :

| Type d'événement                           | Type de banque de données |
|--------------------------------------|-----------------|
| Jeton OATH                           | Journaux d’authentification multifacteur<br/>Magasin de données d’activité d’authentification multifacteur |
| SMS unidirectionnel                          | Journaux d’authentification multifacteur<br/>Magasin de données d’activité d’authentification multifacteur |
| Appel vocal                           | Journaux d’authentification multifacteur<br/>Magasin de données d’activité d’authentification multifacteur<br/>Utilisateurs bloqués (si fraude signalée) |
| Notification Microsoft Authenticator | Journaux d’authentification multifacteur<br/>Magasin de données d’activité d’authentification multifacteur<br/>Utilisateurs bloqués (si fraude signalée)<br/>Demandes de modification en cas de changement du jeton de l’appareil Microsoft Authenticator |

### <a name="data-stored-by-mfa-server"></a>Données stockées par le serveur MFA

Si vous utilisez le serveur MFA, les données personnelles suivantes sont stockées.

> [!IMPORTANT]
> Depuis le 1er juillet 2019, Microsoft ne propose plus MFA Server pour les nouveaux déploiements. Les nouveaux clients qui souhaitent imposer une authentification multifacteur à leurs utilisateurs doivent utiliser l’authentification multifacteur Azure AD basée sur le cloud. Les clients existants qui ont activé le serveur d’authentification multifacteur avant le 1er juillet 2019 peuvent télécharger la dernière version et les dernières mises à jour, et générer des informations d’identification d’activation comme à leur habitude.

| Type d'événement                           | Type de banque de données |
|--------------------------------------|-----------------|
| Jeton OATH                           | Journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur |
| SMS unidirectionnel                          | Journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur |
| Appel vocal                           | Journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur<br />Utilisateurs bloqués (si fraude signalée) |
| Notification Microsoft Authenticator | Journaux d’authentification multifacteur<br />Magasin de données d’activité d’authentification multifacteur<br />Utilisateurs bloqués (si fraude signalée)<br />Demandes de modification en cas de changement du jeton de l’appareil Microsoft Authenticator |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Données organisationnelles stockées par l’authentification multifacteur Azure AD

Les données organisationnelles représentent des informations au niveau du locataire qui pourraient exposer la configuration ou la configuration de l’environnement. Les paramètres de locataire des pages d’authentification multifacteur du portail Azure suivantes peuvent stocker des données organisationnelles telles que des seuils de verrouillage ou des informations d’identification de l’appelant pour les demandes d’authentification téléphonique entrantes :

* Verrouillage de compte
* Alerte de fraude
* Notifications
* Paramètres de l’appel téléphonique

Pour le serveur MFA, les pages suivantes du portail Azure peuvent contenir des données organisationnelles :

* Paramètres du serveur
* Contournement à usage unique
* Règles de mise en cache
* État du serveur d’authentification multifacteur

## <a name="multifactor-authentication-activity-reports-for-public-cloud"></a>Rapports d’activité de l’authentification multifacteur pour le cloud public

Les rapports d’activité de l’authentification multifacteur stockent les informations relatives à l’activité à partir des composants locaux : extension NPS, adaptateur AD FS et serveur MFA. Les journaux du service d’authentification multifacteur sont utilisés pour exploiter le service.
Les sections suivantes montrent l’emplacement de stockage des rapports d’activité et des journaux des services pour les méthodes d’authentification spécifiques à chaque composant dans les différentes régions des clients. Les appels vocaux standard peuvent faire l’objet d’un basculement vers une région distincte.

>[!NOTE]
>Les rapports d’activité d’authentification multifacteur contiennent des données à caractère personnel, telles que le nom d’utilisateur principal (UPN) et le numéro de téléphone complet.

### <a name="nps-extension-and-ad-fs-adapter"></a>Extension NPS et adaptateur AD FS

| Méthode d'authentification                                                             | Région du client                      | Emplacement du rapport d’activité | Emplacement du journal de service |
|-----------------------------------------------------------------------------------|--------------------------------------|--------------------------|----------------------|
| Jetons logiciels et matériels OATH                                                 | Australie et Nouvelle Zélande            | Australie/Nouvelle-Zélande    | Cloud régional      |
| Jetons logiciels et matériels OATH                                                 | En dehors de l’Australie et de la Nouvelle-Zélande | États-Unis            | Cloud régional      |
| Appels vocaux sans messages d’accueil personnalisés ainsi que toutes les autres méthodes d’authentification à l’exception des jetons logiciels et matériels OATH  | Quelconque                               | États-Unis            | Cloud régional      |
| Appels vocaux avec messages d’accueil personnalisés                                         | Quelconque                                  | États-Unis            | Serveur back-end MFA aux États-Unis |

### <a name="mfa-server-and-cloud-based-mfa"></a>Serveur MFA et authentification MFA basée sur le cloud

| Composant  | Méthode d'authentification                          | Région du client                      | Emplacement du rapport d’activité        | Emplacement du journal de service         |
|------------|------------------------------------------------|--------------------------------------|---------------------------------|------------------------------|
| Serveur MFA | Toutes les méthodes                                    | Quelconque                                  | États-Unis                   | Serveur back-end MFA aux États-Unis |
| Authentification MFA cloud  | Appels vocaux standard ainsi que toutes les autres méthodes     | Quelconque                                  | Journaux de connexion Azure AD de la région | Cloud régional              |
| Authentification MFA cloud  | Appels vocaux avec messages d’accueil personnalisés              | Quelconque                                  | Journaux de connexion Azure AD de la région | Serveur back-end MFA aux États-Unis |

## <a name="multifactor-authentication-activity-reports-for-sovereign-clouds"></a>Rapports d’activité de l’authentification multifacteur pour les clouds souverains

Le tableau suivant montre l’emplacement des journaux de service pour les clouds souverains.

| Cloud souverain                      | Journaux d’activité de connexion                         | Rapport d’activité d’authentification multifacteur | Journaux du service d’authentification multifacteur |
|--------------------------------------|--------------------------------------|--------------------------------------------|-----------------------------------------|
| Microsoft Azure Germany              | Allemagne                              | États-Unis                              | États-Unis                           |
| 21Vianet Azure - Chine                 | Chine                                | États-Unis                              | États-Unis                           |
| Cloud de Microsoft pour le Secteur Public           | États-Unis                        | États-Unis                              | États-Unis                           |

## <a name="next-steps"></a>Étapes suivantes

Pour plus de détails sur les informations utilisateur collectées par l’authentification multifacteur Azure AD basée sur le cloud et le serveur MFA, consultez [Collecte de données utilisateur de l’authentification multifacteur Azure AD](howto-mfa-reporting-datacollection.md).
