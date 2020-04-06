---
title: Détection des événements à risque dans Azure Active Directory | Microsoft Docs
description: Cet article offre une présentation détaillée des détections des événements à risque.
services: active-directory
keywords: azure active directory identity protection, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité
author: MarkusVi
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4caa248f6972609ecb6bf71dd521c68d78cebd70
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383954"
---
# <a name="azure-active-directory-risk-detections"></a>Détections des événements à risque dans Azure Active Directory

La grande majorité des violations de sécurité ont lieu lorsque des cybercriminels parviennent à accéder à un environnement en volant l’identité d’un utilisateur. Détecter les identités compromises n’est pas chose aisée. Azure Active Directory utilise les algorithmes Machine Learning et des modèles heuristiques adaptatifs pour détecter les actions suspectes liées aux comptes de votre utilisateur. Chaque action suspecte détectée est stockée dans un enregistrement appelé **détection d’événement à risque**.

Deux emplacements vous permettent de passer en revue les détections d’événements à risque :

 - **Génération de rapports Azure AD** : les détections d’événements à risque font partie des rapports de sécurité d’Azure AD. Pour plus d’informations, consultez [Rapport sur la sécurité des utilisateurs courant un risque](concept-user-at-risk.md) et [Rapport de connexions risquées](concept-risky-sign-ins.md).

 - **Azure AD Identity Protection** : les détections d’événements à risque font également partie des fonctionnalités de création de rapports [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

De plus, vous pouvez utiliser l’[API Identity Protection relative aux détections d’événements à risque](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) pour accéder par programmation aux détections de sécurité à l’aide de Microsoft Graph. Pour plus d’informations, consultez [Prise en main d’Azure Active Directory Identity Protection et de Microsoft Graph](../identity-protection/graph-get-started.md). 

À l’heure actuelle, Azure Active Directory détecte six types d’événements à risque :

- [Utilisateurs dont les informations d’identification ont fait l’objet d’une fuite](#leaked-credentials) 
- [Connexions depuis des adresses IP anonymes](#sign-ins-from-anonymous-ip-addresses) 
- [Voyage impossible vers des emplacements inhabituels](#impossible-travel-to-atypical-locations) 
- [Connexions depuis des périphériques infectés](#sign-ins-from-infected-devices) 
- [Connexions depuis des adresses IP avec une activité suspecte](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Connexions depuis des emplacements inconnus](#sign-in-from-unfamiliar-locations) 

![Détection d’événements à risque](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Parfois, vous risquez de trouver une détection d’événement à risque sans entrée correspondante dans le [rapport sur les connexions](concept-sign-ins.md). En effet, Identity Protection évalue le risque pour les connexions **interactives** et les connexions **non interactives**, alors que le rapport sur les connexions ne montre que les connexions interactives.

L’insight que vous obtenez pour une détection des risques est liée à votre abonnement Azure AD. 

* Avec l’édition **Azure AD Premium P2**, vous obtenez des informations très détaillées sur toutes les détections sous-jacentes. 
* Avec **Azure AD Premium P1**, les détections avancées (comme les propriétés de connexion inhabituelles) ne sont pas couvertes par votre licence et apparaissent sous le nom **Connexion avec un risque supplémentaire détectée**. En outre, le niveau de risque et les champs de détail de risque sont masqués.

Bien que la détection des événements à risque représente déjà un aspect important de la protection de vos identités, vous pouvez également les résoudre manuellement ou implémenter des réponses automatisées en configurant des stratégies d’accès conditionnel. Pour plus d’informations, consultez [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-detection-types"></a>Types de détections d’événements à risque

Le **type de détection d’événement à risque** détermine l’action suspecte pour laquelle un enregistrement de détection d’événement à risque a été créé.

Les investissements continus de Microsoft dans le processus de détection assurent :

- Améliorations apportées à la précision de la détection existante des événements à risque 
- Ajout ultérieur de nouveaux types de détections d’événements à risque

### <a name="leaked-credentials"></a>Informations d’identification divulguées

Souvent, lorsque les cybercriminels compromettent les mots de passe valides d’utilisateurs légitimes, ils le font dans le but de les rendre publics. En général, les mots de passe volés sont publiés sur le « dark web », ou bien ils sont échangés ou vendus sur le marché noir. Le service Microsoft chargé des informations d’identification volées acquiert les paires nom d’utilisateur/mot de passe en surveillant les sites publics et les sites du « dark web », et en travaillant avec :

- Des chercheurs
- Les lois en vigueur
- Les équipes de sécurité Microsoft
- D’autres sources approuvées 

Lorsque le service acquiert les paires nom d’utilisateur/mot de passe, celles-ci sont vérifiées par rapport aux informations d’identification valides actuelles des utilisateurs AAD. Quand une correspondance est trouvée, cela signifie que le mot de passe d’un utilisateur a été compromis. Une **détection d’événement à risque lié à une fuite d’informations d’identification** est alors créé.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Connexions depuis des adresses IP anonymes

Ce type de détection d’événement à risque signale les utilisateurs qui se sont connectés depuis une adresse IP ayant été identifiée comme l’adresse IP d’un proxy anonyme. Ces proxys sont utilisés par des individus souhaitant masquer l’adresse IP de leur appareil et peuvent être utilisés dans un but malveillant.

### <a name="impossible-travel-to-atypical-locations"></a>Voyage impossible vers des emplacements inhabituels

Ce type de détection d’événement à risque identifie deux connexions depuis des emplacements géographiquement distants, dont au moins un est inhabituel pour l’utilisateur compte tenu de son comportement passé. Entre autres facteurs, cet algorithme Machine Learning prend en compte le délai écoulé entre les deux connexions et le temps nécessaire pour se déplacer du premier emplacement au second, ce qui indique qu’un autre utilisateur utilise les mêmes informations d’identification.

L’algorithme ignore les « faux positifs » évidents contribuant aux conditions de voyage impossible, tels que les VPN et les emplacements régulièrement utilisés par d’autres membres de l’organisation. Le système présente une période d’apprentissage initiale de 14 jours lui servant à assimiler le comportement de connexion des nouveaux utilisateurs. 

### <a name="sign-in-from-unfamiliar-locations"></a>Connexions depuis des emplacements non connus

Ce type de détection d’événement à risque prend en compte les emplacements de connexion passés (IP, latitude/longitude et NSA) pour déterminer les emplacements non connus/nouveaux. Le système stocke les informations sur les emplacements précédents d’un utilisateur et considère ces emplacements comme « connus ». La détection d’événement à risque est déclenchée quand la connexion a lieu depuis un emplacement qui ne figure pas déjà dans la liste des emplacements connus. Le système a une période d’apprentissage initiale de 30 jours, durant laquelle il ne signale pas les nouveaux emplacements en tant qu’emplacements non connus. Le système ignore également les connexions depuis les appareils connus et les emplacements géographiquement proches d’un emplacement connu. 

Identity Protection détecte les connexions provenant d’emplacements non connus, ainsi que celles ayant fait l’objet d’une authentification de base et celles qui utilisent des protocoles hérités. Étant donné que ces protocoles ne proposent pas les fonctionnalités modernes, telles que l’ID client, les données de télémétrie ne sont pas suffisantes pour réduire le nombre de faux positifs. Pour réduire le nombre d’événements à risque détectés, vous devez passer à une méthode d’authentification moderne.   

> [!NOTE]
> Si le nom d’utilisateur et le mot de passe de connexion ne correspondent pas, la connexion échoue et la détection de risque n’est pas effectuée. Les détections de risque d’une connexion à partir d’un emplacement inhabituel sont déclenchées uniquement si les connexions aboutissent.

### <a name="sign-ins-from-infected-devices"></a>Connexions depuis des appareils infectés

Ce type de détection d’événement à risque identifie les connexions depuis des appareils infectés par des logiciels malveillants, qui sont connus pour communiquer activement avec un serveur robot, grâce à la mise en corrélation des adresses IP des appareils des utilisateurs avec des adresses ayant été en contact avec un serveur robot. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Connexions depuis des adresses IP avec des activités suspectes
Ce type de détection d’événement à risque identifie les adresses IP depuis lesquelles un grand nombre de tentatives de connexion ayant échoué ont été constatées, pour plusieurs comptes d’utilisateurs et sur une courte période. Cela correspond aux modèles de trafic des adresses IP utilisées par les cybercriminels et donne une indication forte que les comptes sont déjà compromis ou sont sur le point de l’être. Il s’agit d’un algorithme d’apprentissage automatique qui ignore les « faux positifs » évidents, tels que les adresses IP régulièrement utilisées par d’autres membres de l’organisation.  Le système présente une période d’apprentissage initiale de 14 jours lui servant à assimiler le comportement de connexion des nouveaux utilisateurs et clients.

## <a name="detection-type"></a>Type de détection

Le type de détection (**Temps réel**, **Hors connexion**) indique les circonstances de détection d’un événement à risque. À l’heure actuelle, la plupart des détections d’événements à risque ont lieu hors connexion dans le cadre d’une opération de post-traitement après la survenue de la détection d’un événement à risque.

Le tableau suivant répertorie le temps nécessaire à un type de détection pour s’afficher dans un rapport associé :

| Type de détection | Latence dans la génération des rapports |
| --- | --- |
| Temps réel | 5 à 10 minutes |
| Hors connexion | 2 à 4 heures |


Les types de détections d’événements à risque par Azure Active Directory sont les suivants :

| Type de détection d’événement à risque | Type de détection |
| :-- | --- | 
| [Utilisateurs dont les informations d’identification ont fait l’objet d’une fuite](#leaked-credentials) | Hors connexion |
| [Connexions depuis des adresses IP anonymes](#sign-ins-from-anonymous-ip-addresses) | Temps réel |
| [Voyage impossible vers des emplacements inhabituels](#impossible-travel-to-atypical-locations) | Hors connexion |
| [Connexions depuis des emplacements inconnus](#sign-in-from-unfamiliar-locations) | Temps réel |
| [Connexions depuis des périphériques infectés](#sign-ins-from-infected-devices) | Hors connexion |
| [Connexions depuis des adresses IP avec une activité suspecte](#sign-ins-from-ip-addresses-with-suspicious-activity) | Hors connexion|


## <a name="risk-level"></a>Niveau de risque

Le niveau de risque d’une détection d’événement à risque (**Haut**, **Moyen** ou **Faible**) indique la gravité et la probabilité d’un tel événement. Cette propriété vous aide à établir la priorité des mesures à prendre. 

Le niveau de gravité de la détection d’événement à risque signalé représente la probabilité qu’une identité soit compromise. La probabilité indique le risque de faux positifs. 

Par exemple, 

* **Haute** : probabilité élevée et gravité élevée de l’événement à risque. Ces événements donnent une indication forte que l’identité de l’utilisateur a été compromise et les comptes d’utilisateurs concernés doivent immédiatement faire l’objet de mesures de correction.

* **Moyenne** : gravité élevée, mais probabilité moindre de l’événement à risque, ou inversement. Ces événements présentent des risques potentiels et les comptes d’utilisateurs concernés doivent faire l’objet de mesures de correction.

* **Faible** : probabilité faible et gravité faible de l’événement à risque. Cet événement peut ne pas nécessiter d’action immédiate, mais l’association à d’autres détections d’événements à risque peut donner une indication forte que l’identité a été compromise.

![Niveau de risque](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Informations d’identification divulguées

Les détections d’événements à risque liés à des informations d’identification divulguées sont définis sur **Haut**, car ils indiquent clairement que le nom d’utilisateur et le mot de passe sont à la disposition de personnes malveillantes.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Connexions depuis des adresses IP anonymes

Le niveau de risque de ce type de détection d’événement à risque est défini sur **Moyen**, car une adresse IP anonyme n’est pas en soi une indication forte qu’un compte a été compromis. Nous vous recommandons de contacter immédiatement l’utilisateur pour vérifier s’il utilisait une adresse IP anonyme.


### <a name="impossible-travel-to-atypical-locations"></a>Voyage impossible vers des emplacements inhabituels

En règle générale, les événements de ce type donnent une bonne indication qu’un pirate est parvenu à se connecter avec le compte correspondant. Cependant, des faux positifs peuvent se produire lorsqu’un utilisateur en déplacement utilise un nouvel appareil ou un VPN qu’aucun autre membre de l’organisation n’utilise. Des faux positifs peuvent également survenir si des applications transmettent incorrectement des adresses IP de serveurs en tant qu’adresses IP de clients, ce qui peut donner l’impression que les connexions se font depuis le centre de données où le serveur principal de cette application est hébergé (il s’agit souvent d’un centre de données Microsoft, ce qui peut donner l’impression que les connexions se font depuis des adresses IP appartenant à Microsoft). En raison de ces faux positifs, le niveau de risque de cette détection d’événement est défini sur **Moyen**.

> [!TIP]
> Vous pouvez réduire la quantité de faux positifs signalés pour ce type de détection d’événement à risque en configurant des [emplacements nommés](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Connexions depuis des emplacements non connus

Les emplacements non connus peuvent donner une indication forte qu’un pirate tente d’utiliser une identité volée. De faux positifs peuvent se produire quand un utilisateur est en déplacement, essaie un nouvel appareil ou utilise un nouveau VPN. C’est pourquoi le niveau de risque de ce type d’événement est défini sur **Moyen**.

### <a name="sign-ins-from-infected-devices"></a>Connexions depuis des appareils infectés

Cette détection d’événement à risque identifie les adresses IP, pas les appareils des utilisateurs. Si plusieurs appareils utilisent une même adresse IP, mais que seuls certains sont contrôlés par un réseau de robots, les connexions depuis les autres appareils peuvent déclencher inutilement cet événement. C’est pourquoi le niveau de risque de ce type de détection d’événement est défini sur **Faible**.  

Nous vous recommandons de contacter l’utilisateur et d’analyser tous ses appareils. Il est également possible que les appareils personnels d’un utilisateur soient infectés ou qu’une tierce personne utilisait un appareil infecté depuis la même adresse IP. Les appareils infectés le sont souvent par des logiciels malveillants qui n’ont pas encore été identifiés par les logiciels antivirus et dont la présence peut être le signe de mauvaises habitudes de la part de l’utilisateur.

Pour plus d'informations sur le traitement des infections de logiciels malveillants, consultez le [Centre de protection contre les programmes malveillants](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Connexions depuis des adresses IP avec des activités suspectes

Nous vous conseillons de contacter l’utilisateur pour vérifier s’il s’est réellement connecté depuis une adresse IP qui a été signalée comme suspecte. Le niveau de risque pour ce type d’événement est défini sur**Moyen**, car il arrive que plusieurs appareils utilisent la même adresse IP, mais que seuls certains soient responsables des activités suspectes. 


## <a name="next-steps"></a>Étapes suivantes

* [Rapport de sécurité sur les utilisateurs à risque](concept-user-at-risk.md)
* [Rapport de sécurité sur les connexions à risque](concept-risky-sign-ins.md)
* [Azure AD Identity Protection](../active-directory-identityprotection.md)
