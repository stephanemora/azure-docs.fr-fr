---
title: Informations de référence sur les enrichissements UEBA Azure Sentinel | Microsoft Docs
description: Cet article présente les enrichissements d’entité générés par l’analyse du comportement des entités d’Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901599"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Informations de référence sur les enrichissements UEBA Azure Sentinel

Ces tableaux répertorient et décrivent les enrichissements d’entités qui peuvent être utilisés pour cibler et affiner votre analyse des incidents de sécurité.

Les deux premiers tableaux, **Insights sur les utilisateurs** et **Insights sur les appareils**, contiennent des informations sur les entités provenant des sources Active Directory/Azure AD et Microsoft Threat Intelligence.

<a name="baseline-explained"></a>Le reste des tableaux, sous **Tableaux d’insights sur les activités**, contiennent des informations sur les entités basées sur les profils de comportement établis par l’analyse du comportement des entités d’Azure Sentinel. Les activités sont analysées par rapport à une ligne de base qui est compilée dynamiquement à chaque utilisation. Chaque activité a sa période de référence définie à partir de laquelle cette base de référence dynamique est dérivée. Cette période est spécifiée dans la colonne [**Ligne de base**](#activity-insights-tables) de ce tableau.

> [!NOTE] 
> Le champ **Nom d’enrichissement** dans les trois tableaux affiche deux lignes d’informations. La première, en **gras**, est le « nom convivial » de l’enrichissement. La deuxième, *(en italiques et entre parenthèses)* , est le nom de champ de l’enrichissement, tel qu’il est stocké dans le [**tableau d’analyse comportementale**](identify-threats-with-entity-behavior-analytics.md#data-schema).

## <a name="user-insights-table"></a>Tableau d’insights sur les utilisateurs

| Nom d’enrichissement | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Nom complet du compte**<br>*(AccountDisplayName)* | Nom complet du compte de l’utilisateur. | Admin, Hayden Cook |
| **Domaine du compte**<br>*(AccountDomain)* | Nom de domaine du compte de l’utilisateur. |  |
| **ID d’objet du compte**<br>*(AccountObjectID)* | ID de l’objet du compte de l’utilisateur. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Rayon d'impact**<br>*(BlastRadius)* | Le rayon d’impact est calculé en fonction de plusieurs facteurs : la position de l’utilisateur dans l’arborescence de l’organisation et les rôles et autorisations Azure Active Directory de l’utilisateur. | Faible, moyen, élevé |
| **Est un compte dormant**<br>*(IsDormantAccount)* | Le compte n’a pas été utilisé au cours des 180 derniers jours. | True, False |
| **Est administrateur local**<br>*(IsLocalAdmin)* | Le compte dispose de privilèges d’administrateur local. | True, False |
| **Est un nouveau compte**<br>*(IsNewAccount)* | Le compte a été créé au cours des 30 derniers jours. | True, False |
| **ID de sécurité local**<br>*(OnPremisesSID)* | ID de sécurité local de l’utilisateur associé à l’action. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>Tableau d’insights sur les appareils

| Nom d’enrichissement | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Browser**<br>*(Browser)* | Navigateur utilisé dans l’action. | Edge, Chrome |
| **Famille de périphériques**<br>*(DeviceFamily)* | Famille d’appareils utilisée dans l’action. | Windows |
| **Type d’appareil**<br>*(DeviceType)* | Type d’appareil client utilisé dans l’action. | Bureau |
| **ISP**<br>*(ISP)* | Fournisseur de services Internet utilisé dans l’action. |  |
| **Système d’exploitation**<br>*(OperatingSystem)* | Système d’exploitation utilisé dans l’action. | Windows 10 |
| **Description de l’indicateur Renseignement sur les menaces**<br>*(ThreatIntelIndicatorDescription)* | Description de l’indicateur de menace observé résolu à partir de l’adresse IP utilisée dans l’action. | L’hôte est membre de botnet: azorult |
| **Type de l’indicateur Renseignement sur les menaces**<br>*(ThreatIntelIndicatorType)* | Type de l’indicateur de menace résolu à partir de l’adresse IP utilisée dans l’action. | Botnet, C2, CryptoMining, Darknet, Ddos, MaliciousUrl, Malware, Phishing, Proxy, PUA, Watchlist |
| **Agent utilisateur**<br>*(UserAgent)* | Agent utilisateur utilisé dans l’action. | Bibliothèque de client Microsoft Azure Graph 1.0,<br>Swagger-Codegen/1.4.0.0/csharp,<br>EvoSTS |
| **Famille d’agents utilisateur**<br>*(UserAgentFamily)* | Famille d’agents utilisateur utilisée dans l’action. | Chrome, Edge, Firefox |
|

## <a name="activity-insights-tables"></a>Tableaux d’insights sur les activités

#### <a name="action-performed"></a>Action effectuée

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Action effectuée pour la première fois par l’utilisateur**<br>*(FirstTimeUserPerformedAction)* | 180 | L’action a été effectuée pour la première fois par l’utilisateur. | True, False |
| **Action effectuée de façon inhabituelle par l’utilisateur**<br>*(ActionUncommonlyPerformedByUser)* | 10 | L’action n’est généralement pas effectuée par l’utilisateur. | True, False |
| **Action effectuée de façon inhabituelle parmi les pairs**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | L’action n’est généralement pas effectuée parmi les pairs de l’utilisateur. | True, False |
| **Action effectuée pour la première fois dans le locataire**<br>*(FirstTimeActionPerformedInTenant)* | 180 | L’action a été effectuée pour la première fois par toute personne de l’organisation. | True, False |
| **Action effectuée de façon inhabituelle dans le locataire**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | L’action n’est généralement pas effectuée dans l’organisation. | True, False |
|

#### <a name="app-used"></a>Application utilisée

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Application utilisée pour la première fois par l’utilisateur**<br>*(FirstTimeUserUsedApp)* | 180 | L’application a été utilisée pour la première fois par l’utilisateur. | True, False |
| **Application utilisée de façon inhabituelle par l’utilisateur**<br>*(AppUncommonlyUsedByUser)* | 10 | L’application n’est généralement pas utilisée par l’utilisateur. | True, False |
| **Application utilisée de façon inhabituelle parmi les pairs**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | L’application n’est généralement pas utilisée parmi les pairs de l’utilisateur. | True, False |
| **Application observée pour la première fois dans le locataire**<br>*(FirstTimeAppObservedInTenant)* | 180 | L’application a été observée pour la première fois dans l’organisation. | True, False |
| **Application utilisée de façon inhabituelle dans le locataire**<br>*(AppUncommonlyUsedInTenant)* | 180 | L’application n’est généralement pas utilisée dans l’organisation. | True, False |
| 

#### <a name="browser-used"></a>Navigateur utilisé

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Première connexion de l’utilisateur via le navigateur**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | Le navigateur a été observé pour la première fois par l’utilisateur. | True, False |
| **Navigateur utilisé de façon inhabituelle par l’utilisateur**<br>*(BrowserUncommonlyUsedByUser)* | 10 | Le navigateur n’est généralement pas utilisé par l’utilisateur. | True, False |
| **Navigateur utilisé de façon inhabituelle parmi les pairs**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | Le navigateur n’est généralement pas utilisé parmi les pairs de l’utilisateur. | True, False |
| **Navigateur observé pour la première fois dans le locataire**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | Le navigateur a été observé pour la première fois dans l’organisation. | True, False |
| **Navigateur utilisé de façon inhabituelle dans le locataire**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | Le navigateur n’est généralement pas utilisé dans l’organisation. | True, False |
| 

#### <a name="country-connected-from"></a>Pays à partir duquel la connexion est établie

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Première connexion de l’utilisateur à partir de ce pays**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | L’emplacement géographique, tel que résolu à partir de l’adresse IP, a été utilisé pour la première fois par l’utilisateur pour établir la connexion. | True, False |
| **Pays d’où l’utilisateur s’est connecté de façon inhabituelle**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | L’emplacement géographique, tel que résolu à partir de l’adresse IP, n’est généralement pas utilisé par l’utilisateur pour établir la connexion. | True, False |
| **Pays d’où la connexion est inhabituelle parmi les pairs**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | L’emplacement géographique, tel que résolu à partir de l’adresse IP, n’est généralement pas utilisé parmi les pairs de l’utilisateur pour établir une connexion. | True, False |
| **Première connexion depuis ce pays observée dans le locataire**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | La connexion a été établie pour la première fois depuis ce pays par une personne de l’organisation. | True, False |
| **Pays d’où la connexion est inhabituelle dans le locataire**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | L’emplacement géographique, tel que résolu à partir de l’adresse IP, n’est généralement pas utilisé dans l’organisation pour établir une connexion. | True, False |
| 

#### <a name="device-used-to-connect"></a>Appareil utilisé pour se connecter

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Première connexion de l’utilisateur à partir de l’appareil**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | L’appareil source a été utilisé pour la première fois par l’utilisateur pour se connecter. | True, False |
| **Appareil utilisé de façon inhabituelle par l’utilisateur**<br>*(DeviceUncommonlyUsedByUser)* | 10 | L’appareil n’est généralement pas utilisé par l’utilisateur. | True, False |
| **Appareil utilisé de façon inhabituelle parmi les pairs**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | L’appareil n’est généralement pas utilisé parmi les pairs de l’utilisateur. | True, False |
| **Appareil observé pour la première fois dans le locataire**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | L’appareil a été observé pour la première fois dans l’organisation. | True, False |
| **Appareil utilisé de façon inhabituelle dans le locataire**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | L’appareil n’est généralement pas utilisé dans l’organisation. | True, False |
| 

#### <a name="other-device-related"></a>Autres insights relatifs aux appareils

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Session ouverte pour la première fois par l’utilisateur sur l’appareil**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | L’utilisateur s’est connecté à l’appareil de destination pour la première fois. | True, False |
| **Famille d’appareils utilisée de façon inhabituelle dans le locataire**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | La famille d’appareils n’est généralement pas utilisée dans l’organisation. | True, False |
| 

#### <a name="internet-service-provider-used-to-connect"></a>Fournisseur de services Internet utilisé pour la connexion

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Première connexion de l’utilisateur via un ISP**<br>*(FirstTimeUserConnectedViaISP)* | 30 | L’ISP a été observé pour la première fois par l’utilisateur. | True, False |
| **ISP utilisé de façon inhabituelle par l’utilisateur**<br>*(ISPUncommonlyUsedByUser)* | 10 | L’ISP n’est généralement pas utilisé par l’utilisateur. | True, False |
| **ISP utilisé de façon inhabituelle parmi les pairs**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | L’ISP n’est généralement pas utilisé parmi les pairs de l’utilisateur. | True, False |
| **Première connexion via l’ISP dans le locataire**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | L’ISP a été observé pour la première fois dans l’organisation. | True, False |
| **ISP utilisé de façon inhabituelle dans le locataire**<br>*(ISPUncommonlyUsedInTenant)* | 30 | L’ISP n’est généralement pas utilisé dans l’organisation. | True, False |
| 

#### <a name="resource-accessed"></a>Ressource consultée

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Ressource consultée pour la première fois par l’utilisateur**<br>*(FirstTimeUserAccessedResource)* | 180 | La ressource a été consultée pour la première fois par l’utilisateur. | True, False |
| **Ressource consultée de façon inhabituelle par l’utilisateur**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | La ressource n’est généralement pas consultée par l’utilisateur. | True, False |
| **Ressource consultée de façon inhabituelle parmi les pairs**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | La ressource n’est généralement pas consultée parmi les pairs de l’utilisateur. | True, False |
| **Ressource consultée pour la première fois dans le locataire**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | La ressource a été consultée pour la première fois par une personne de l’organisation. | True, False |
| **Ressource consultée de façon inhabituelle dans le locataire**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | La ressource n’est généralement pas consultée dans l’organisation. | True, False |
| 

#### <a name="miscellaneous"></a>Divers

| Nom d’enrichissement | [Ligne de base](#baseline-explained) (jours) | Description | Exemple de valeur |
| --- | --- | --- | --- |
| **Action effectuée pour la dernière fois par l’utilisateur**<br>*(LastTimeUserPerformedAction)* | 180 | Dernière fois que l’utilisateur a effectué la même action. | <Timestamp> |
| **Une action similaire n’a pas été effectuée dans le passé**<br>*(SimilarActionWasn’tPerformedInThePast)* | 30 | Aucune action dans le même fournisseur de ressources n’a été effectuée par l’utilisateur. | True, False |
| **Emplacement IP source**<br>*(SourceIPLocation)* | *N/A* | Pays résolu à partir de l’adresse IP source de l’action. | [Surrey, Royaume-Uni] |
| **Volume d’opérations inhabituellement élevé**<br>*(UncommonHighVolumeOfOperations)* | 7 | Un utilisateur a effectué une rafale d’opérations similaires au sein du même fournisseur. | True, False |
| **Nombre inhabituel d’échecs de l’accès conditionnel Azure AD**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | Un nombre inhabituel d’utilisateurs n’a pas pu s’authentifier en raison de l’accès conditionnel | True, False |
| **Nombre inhabituel d’appareils ajoutés**<br>*(UnusualNumberOfDevicesAdded)* | 5 | Un utilisateur a ajouté un nombre inhabituel d’appareils. | True, False |
| **Nombre inhabituel d’appareils supprimés**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | Un utilisateur a supprimé un nombre inhabituel d’appareils. | True, False |
| **Nombre inhabituel d’utilisateurs ajoutés au groupe**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | Un utilisateur a ajouté un nombre d’utilisateurs à un groupe. | True, False |
|