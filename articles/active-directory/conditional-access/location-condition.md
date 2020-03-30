---
title: Conditions d’emplacement dans l’accès conditionnel Azure Active Directory
description: Découvrez comment utiliser la condition d’emplacement pour contrôler l’accès à vos applications cloud basées sur un emplacement réseau de l’utilisateur.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79230777"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Qu’est-ce que la condition d’emplacement dans l’accès conditionnel Azure Active Directory ? 

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), vous pouvez contrôler la façon dont les utilisateurs autorisés peuvent accéder à vos applications cloud. La condition d’emplacement d’une stratégie d’accès conditionnel vous permet de lier des paramètres de contrôle d’accès aux emplacements réseau de vos utilisateurs.

Cet article vous fournit les informations dont vous avez besoin pour configurer la condition d’emplacement.

## <a name="locations"></a>Emplacements

Azure AD active l’authentification unique aux appareils, applications et des services depuis n’importe quel endroit sur l’Internet public. Avec la condition d’emplacement, vous pouvez contrôler l’accès à vos applications cloud basées sur l’emplacement réseau d’un utilisateur. Les cas d’utilisation courants de la condition d’emplacement sont :

- Exiger l’authentification multifacteur pour les utilisateurs accédant à un service lorsqu’ils sont en dehors du réseau d’entreprise.
- Bloquer l’accès pour les utilisateurs accédant à un service à partir de pays ou régions spécifiques.

Un emplacement correspond à une étiquette pour un emplacement réseau qui représente un emplacement nommé ou des adresses IP approuvées par une authentification multifacteur.

## <a name="named-locations"></a>Emplacements nommés

Avec les emplacements nommés, vous pouvez créer des regroupements logiques de plages d’adresses IP ou de pays et régions.

Vous pouvez accéder à vos emplacements nommés dans la section **Gérer** de la page d’accès conditionnel.

![Emplacements nommés dans l’accès conditionnel](./media/location-condition/02.png)

Un emplacement nommé se compose des éléments suivants :

![Créer un nouvel emplacement nommé](./media/location-condition/42.png)

- **Nom** : nom complet d’un emplacement nommé.
- **Plages d’adresses IP** : une ou plusieurs plages d’adresses IPv4 au format CIDR. La définition d’une plage d’adresses IPv6 n’est pas prise en charge.

   > [!NOTE]
   > Les plages d’adresses IPv6 ne peuvent actuellement pas être incluses dans un emplacement nommé. Elles ne peuvent donc pas non plus être exclues d’une stratégie d’accès conditionnel.

- **Marquer comme emplacement approuvé** : indicateur que vous pouvez définir pour un emplacement nommé afin d’indiquer un emplacement approuvé. Les emplacements approuvés sont généralement des zones de réseau qui sont contrôlées par votre service informatique. En plus de l’accès conditionnel, les emplacements nommés approuvés sont également utilisés par les rapports de sécurité Azure Identity Protection et Azure AD afin de réduire les [faux positifs](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Pays/régions** : cette option vous permet de sélectionner un ou plusieurs pays ou régions pour définir un emplacement nommé.
- **Inclure les zones inconnues** : certaines adresses IP ne sont pas mappées à un pays ou une région spécifique. Cette option vous permet de choisir si ces adresses IP doivent être incluses dans l’emplacement nommé. Utilisez ce paramètre lorsque la stratégie utilisant l’emplacement nommé doit s’appliquer à des emplacements inconnus.

Le nombre d’emplacements nommés que vous pouvez configurer est limité par la taille de l’objet associé dans Azure AD. Vous pouvez configurer des emplacements selon les limitations suivantes :

- Un emplacement nommé avec 1200 plages d’adresses IP maximum.
- Un maximum de 90 emplacements nommés avec une plage d’adresses IP assignée à chacun d’eux.

La stratégie d’accès conditionnel s’applique au trafic IPv4 et IPv6. Actuellement, les emplacements nommés ne permettent pas la configuration des plages IPv6. Cette limitation entraîne les situations suivantes :

- La stratégie d’accès conditionnel ne peut pas cibler des plages IPv6 spécifiques.
- La stratégie d’accès conditionnel ne peut pas exclure des plages IPv6 spécifiques.

Si une stratégie est configurée pour être appliquée à « N’importe quel emplacement », elle s’applique au trafic IPv4 et IPv6. Les emplacements nommés configurés pour des régions et des pays spécifiés prennent uniquement en charge les adresses IPv4. Le trafic IPv6 est uniquement inclus si l’option « Inclure les zones inconnues » est sélectionnée.

## <a name="trusted-ips"></a>Adresses IP approuvées

Vous pouvez également configurer des plages d’adresses IP représentant l’intranet local de votre organisation dans les [paramètres du service d’authentification multifacteur](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Cette fonctionnalité vous permet de configurer jusqu’à 50 plages d’adresses IP. Les plages d’adresses IP sont au format CIDR. Pour plus d’informations, consultez [Adresses IP approuvées](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Si vous avez approuvé des adresses IP configurées, elles apparaissent en tant qu’**adresses IP approuvées MFA** dans la liste des emplacements de la condition d’emplacement.

### <a name="skipping-multi-factor-authentication"></a>Ignorer l’authentification multifacteur

Sur la page des paramètres du service d’authentification multifacteur, vous pouvez identifier les utilisateurs de l’intranet d’entreprise en sélectionnant **Ignorer l’authentification multifacteur pour les demandes issues d’utilisateurs fédérés provenant de mon intranet**. Ce paramètre indique que la revendication de réseau d’entreprise interne, qui est émise par AD FS, doit être approuvée et utilisée pour identifier l’utilisateur comme étant sur le réseau d’entreprise. Pour plus d’informations, consultez [Activer la fonctionnalité Adresses IP approuvées à l’aide de l’accès conditionnel](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Après avoir activé cette option, qui inclut l’emplacement nommé, les **Adresses IP approuvées MFA** s’appliqueront à toutes les stratégies contenant cette option sélectionnée.

Pour les applications mobiles et de bureau avec des durées de session active longues, l’accès conditionnel est réévalué régulièrement. La valeur par défaut est une fois une heure. Lorsque la revendication de réseau d’entreprise interne n’est émise qu’au moment de l’authentification initiale, Azure AD peut ne pas contenir de liste de plages d’adresses IP approuvées. Dans ce cas, il est plus difficile de déterminer si l’utilisateur est toujours sur le réseau d’entreprise :

1. Vérifiez si l’adresse IP de l’utilisateur se trouve dans l’une des plages d’adresses IP approuvées.
2. Vérifiez si les trois premiers octets de l’adresse IP de l’utilisateur correspondent aux trois premiers octets de l’adresse IP de l’authentification initiale. L’adresse IP est comparée à l’authentification initiale lorsque la revendication de réseau d’entreprise interne a été émise à l’origine et que l’emplacement de l’utilisateur a été validé.

Si les deux étapes échouent, un utilisateur est considéré comme n’étant plus sur une adresse IP approuvée.

## <a name="location-condition-configuration"></a>Configuration de la condition d’emplacement

Lorsque vous configurez la condition d’emplacement, vous avez la possibilité de faire la distinction entre :

- N’importe quel emplacement
- Tous les emplacements approuvés
- Des emplacements sélectionnés

![Configuration de la condition d’emplacement](./media/location-condition/01.png)

### <a name="any-location"></a>N’importe quel emplacement

Par défaut, la sélection de **N’importe quel emplacement** entraîne l’application d’une stratégie à toutes les adresses IP, à savoir n’importe quelle adresse sur Internet. Ce paramètre ne se limite pas aux adresses IP que vous avez configurées en tant qu’emplacement nommé. Lorsque vous sélectionnez **N’importe quel emplacement**, vous pouvez toujours exclure des emplacements spécifiques d’une stratégie. Par exemple, vous pouvez appliquer une stratégie à tous les emplacements, sauf des emplacements approuvés, afin de définir l’étendue à tous les emplacements, à l’exception du réseau d’entreprise.

### <a name="all-trusted-locations"></a>Tous les emplacements approuvés

Cette option s’applique à :

- Tous les emplacements qui ont été marqués comme emplacement approuvé
- **Adresses IP approuvées MFA** (si configurées)

### <a name="selected-locations"></a>Des emplacements sélectionnés

Avec cette option, vous pouvez sélectionner un ou plusieurs emplacements nommés. Pour une stratégie avec ce paramètre à appliquer, un utilisateur doit se connecter à partir de l’un des emplacements sélectionnés. Lorsque vous cliquez sur **Sélectionner**, le contrôle de sélection du réseau nommé qui affiche la liste des réseaux nommés s’ouvre. La liste indique également si l’emplacement réseau a été marqué comme approuvé. L’emplacement nommé appelé **Adresses IP approuvées MFA** est utilisé pour inclure les paramètres IP pouvant être configurés dans la page des paramètres du service d’authentification multifacteur.

## <a name="what-you-should-know"></a>Ce que vous devez savoir

### <a name="when-is-a-location-evaluated"></a>Quand un emplacement est-il évalué ?

Les stratégies d’accès conditionnel sont évaluées lorsque :

- Un utilisateur se connecte initialement à une application web, mobile ou de bureau.
- Une application mobile ou de bureau qui utilise une authentification moderne utilise un jeton d’actualisation pour acquérir un nouveau jeton d’accès. Par défaut, la vérification est effectuée une fois par heure.

Ce qui signifie que pour des applications mobiles et de bureau utilisant l’authentification moderne, un changement d’emplacement sera détecté dans l’heure du changement de l’emplacement réseau. Pour les applications mobiles et de bureau n’utilisant pas l’authentification moderne, la stratégie est appliquée sur chaque requête de jeton. La fréquence de la requête peut varier selon l’application. Il en va de même pour les applications web. La stratégie est appliquée à la connexion initiale et est valide pour toute la durée de la session dans l’application web. En raison des différences de durées de session dans les applications, le temps d’évaluation de la stratégie varie aussi. À chaque fois que l’application demande un nouveau jeton de connexion, la stratégie est appliquée.

Par défaut, Azure AD émet un jeton toutes les heures. Après une sortie du réseau d’entreprise, la stratégie est appliquée dans l’heure qui suit pour les applications utilisant l’authentification moderne.

### <a name="user-ip-address"></a>Adresse IP utilisateur

L’adresse IP qui est utilisée dans l’évaluation de la stratégie correspond à l’adresse IP publique de l’utilisateur. Pour les appareils sur un réseau privé, il ne s’agit pas de l’adresse IP cliente de l’appareil de l’utilisateur sur l’intranet ; il s’agit de l’adresse utilisée par le réseau pour se connecter à l’Internet public.

> [!WARNING]
> Si votre appareil ne dispose que d’une adresse IPv6, la configuration de la condition de l’emplacement n’est pas prise en charge.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Chargement et téléchargement en bloc d’emplacements nommés

Lorsque vous créez ou mettez à jour des emplacements nommés, pour effectuer des mises à jour en bloc, vous pouvez charger ou télécharger un fichier CSV contenant les plages d’adresses IP. Un chargement remplace les plages d’adresses IP de la liste par celles du fichier. Chaque ligne du fichier contient une plage d’adresses IP au format CIDR.

### <a name="cloud-proxies-and-vpns"></a>Proxies cloud et réseaux privés virtuels

Lorsque vous utilisez un proxy hébergé dans le cloud ou une solution VPN, l’adresse IP Azure AD utilisée pendant l’évaluation d’une stratégie correspond à l’adresse IP du proxy. L’en-tête X-Forwarded-For (XFF) qui contient l’adresse IP publique de l’utilisateur n’est pas utilisé car il n’existe aucune validation provenant d’une source approuvée, il pourrait donc permettre de falsifier une adresse IP.

Lorsqu’un proxy cloud est disponible, une stratégie utilisée pour exiger un appareil joint à un domaine peut être utilisée, ou la revendication de réseau d’entreprise interne à partir d’AD FS.

### <a name="api-support-and-powershell"></a>Prise en charge des API et PowerShell

Les API et PowerShell ne sont pas encore pris en charge pour les emplacements nommés, ou pour les stratégies d’accès conditionnel.

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, voir [Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel Azure Active Directory](app-based-mfa.md).
- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md).
