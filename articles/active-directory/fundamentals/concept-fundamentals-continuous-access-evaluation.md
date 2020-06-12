---
title: Évaluation de l’accès continu dans Azure AD
description: Réponse plus rapide aux modifications de l’état utilisateur grâce à l’évaluation de l’accès continu dans Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf904bb2c0d133ea07cd32274fad5b6601da5d9
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148121"
---
# <a name="continuous-access-evaluation"></a>Évaluation de l’accès continu

Les services Microsoft, comme Azure Active Directory (Azure AD) et Office 365, utilisent des normes et des protocoles ouverts pour optimiser l’interopérabilité. L’un des plus importants est Open ID Connect (OIDC). Lorsqu’une application cliente telle qu’Outlook se connecte à un service comme Exchange Online, les demandes d’API sont autorisées à l’aide de jetons d’accès OAuth 2.0. Par défaut, ces jetons d’accès sont valides pendant une heure. Lorsqu’ils expirent, le client est redirigé vers Azure AD pour les actualiser. Cela donne également l’occasion de réévaluer les stratégies d’accès des utilisateurs : il est possible de choisir de ne pas actualiser le jeton en raison d’une stratégie d’accès conditionnel ou parce que l’utilisateur a été désactivé dans le répertoire. 

L’expiration et l’actualisation des jetons sont un mécanisme standard du secteur. Cela dit, les clients ont exprimé des préoccupations à propos du décalage entre le moment où les conditions de risque changent pour l’utilisateur (par exemple, le passage du bureau d’entreprise au café du coin, ou la découverte d’informations d’identification d’utilisateur sur le marché noir) et le moment où des stratégies peuvent être appliquées en relation avec cette modification. Nous avons fait l’expérience de l’approche radicale des durées de vie de jeton réduites, mais il s’est avéré qu’elles peuvent nuire à l’expérience utilisateur et à la fiabilité sans éliminer les risques.

Une réponse en temps utile aux violations de stratégie ou autres problèmes de sécurité nécessite vraiment une « conversation » entre l’émetteur du jeton, comme Azure AD, et la partie de confiance, comme Exchange Online. Cette conversation bidirectionnelle nous offre deux capacités importantes. La partie de confiance peut détecter des changements, comme un client provenant d’un nouvel emplacement, et en informer l’émetteur du jeton. Et l’émetteur du jeton peut demander à la partie de confiance d’arrêter de respecter les jetons pour un utilisateur donné en raison d’une compromission ou d’une désactivation de compte ou d’autres soucis. Le mécanisme permettant cette conversation est l’Évaluation continue de l’accès.

Microsoft a été l’un des premiers participants à l’initiative CAEP (Continuous Access Evaluation Protocol) dans le cadre du groupe de travail [Shared Signals and Events](https://openid.net/wg/sse/) de la fondation OpenID. Les fournisseurs d’identité et les parties de confiance pourront tirer parti des événements et des signaux de sécurité définis par le groupe de travail pour autoriser de nouveau l’accès ou y mettre fin. Il s’agit d’un travail passionnant qui améliorera la sécurité sur de nombreuses plateformes et applications.

Les avantages en matière de sécurité étant si importants, nous déployons une première implémentation spécifique à Microsoft, parallèlement à la poursuite de nos travaux au sein des organismes de normalisation. Alors que nous travaillons au déploiement de ces capacités d’évaluation d’accès continu (EAC) dans les services Microsoft, nous avons beaucoup appris et nous partageons ces informations avec la communauté de normalisation. Nous espérons que notre expérience en matière de déploiement pourra contribuer à l’élaboration d’une norme encore plus performante et nous nous engageons à mettre en œuvre cette norme une fois qu’elle aura été ratifiée, afin que tous les services participants puissent en bénéficier.

## <a name="how-does-cae-work-in-microsoft-services"></a>Comment l’EAC fonctionne-t-elle dans les services Microsoft ?

Nous concentrons notre première implémentation de l’évaluation de l’accès continu à Exchange et Teams. Nous espérons étendre la prise en charge à d’autres services Microsoft à l’avenir. Nous allons commencer à activer l’évaluation de l’accès continu uniquement pour les locataires sans aucune stratégie d’accès conditionnel. Nous utiliserons les enseignements tirés de cette phase d’EAC pour informer notre lancement continu de l’EAC.

## <a name="service-side-requirements"></a>Configuration requise côté service

L’évaluation de l’accès continu est implémentée en permettant aux services (fournisseurs de ressources) de s’abonner à des événements cruciaux dans Azure AD afin que ces événements puissent être évalués et appliqués en quasi-temps réel. Les événements suivants seront appliqués dans ce premier lancement de l’EAC :

- Le compte d’utilisateur est supprimé ou désactivé
- Le mot de passe d’un utilisateur a été modifié ou réinitialisé
- L’administrateur révoque tous les jetons d’actualisation d’un utilisateur
- Risque utilisateur élevé détecté par Azure AD Identity Protection

À l’avenir, nous espérons ajouter d’autres événements, notamment des événements tels que les changements d’emplacement et d’état des appareils. **Bien que notre objectif soit que la mise en œuvre soit instantanée, dans certains cas, une latence pouvant aller jusqu’à 15 minutes peut être observée en raison du temps de propagation de l’événement**. 

## <a name="client-side-claim-challenge"></a>Contestation de revendication côté client

Avant l’évaluation de l’accès continu, les clients essayaient toujours de relire le jeton d’accès à partir de leur cache tant qu’il n’avait pas expiré. Avec l’EAC, nous introduisons un nouveau cas où un fournisseur de ressources peut rejeter un jeton même s’il n’a pas expiré. Pour informer les clients qu’ils doivent contourner leur cache, même si les jetons mis en cache n’ont pas expiré, nous introduisons un mécanisme appelé **contestation de revendication**. L’EAC requiert une mise à jour du client pour comprendre la constestation de revendication. La dernière version des applications suivantes prend en charge la contestation de revendication :

- Outlook pour Windows 
- Outlook pour iOS 
- Outlook pour Android 
- Outlook pour Mac 
- Teams pour Windows
- Teams pour iOS 
- Teams pour Android 
- Teams pour Mac 

## <a name="token-lifetime"></a>Durée de vie du jeton

Étant donné que les risques et les stratégies sont évalués en temps réel, les clients qui négocient des sessions prenant en charge l’évaluation de l’accès continu s’appuient sur l’EAC au lieu des stratégies existantes de durée de vie des jetons d’accès statiques, ce qui signifie que la stratégie configurable de durée de vie des jetons n’est plus respectée pour les clients prenant en charge l’EAC.

Nous augmenterons la durée de vie des jetons d’accès à 24 heures dans les sessions d’EAC. La révocation est motivée par les événements cruciaux et l’évaluation de la stratégie, et non par une durée arbitraire. Cette modification augmente la stabilité de vos applications sans compromettre votre posture de sécurité. 

## <a name="example-flows"></a>Exemples de flux

### <a name="user-revocation-event-flow"></a>Flux d’événements de révocation des utilisateurs :

![Flux d’événements de révocation des utilisateurs](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Un client compatible avec l’EAC présente des informations d’identification ou un jeton d’actualisation à AAD afin de demander un jeton d’accès pour certaines ressources.
1. Un jeton d’accès est retourné au client avec d’autres artefacts.
1. Un administrateur [révoque explicitement tous les jetons d’actualisation d’un utilisateur](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Un événement de révocation sera envoyé au fournisseur de ressources à partir d’Azure AD.
1. Un jeton d’accès est présenté au fournisseur de ressources. Le fournisseur de ressources évalue la validité du jeton et vérifie s’il existe un événement de révocation pour l’utilisateur. Le fournisseur de ressources utilise ces informations pour décider d’accorder ou non l’accès à la ressource.
1. Dans ce cas, le fournisseur de ressources refuse l’accès et envoie une contestation de revendication 401+ au client.
1. Le client compatible avec l’EAC comprend la contestation de revendication 401+. Il contourne les caches et revient à l’étape 1, en renvoyant à Azure AD son jeton d’actualisation avec la contestation de revendication. Azure AD réévalue ensuite toutes les conditions et invite l’utilisateur à se réauthentifier dans ce cas.
 
## <a name="faqs"></a>FAQ

### <a name="what-is-the-lifetime-of-my-access-token"></a>Quelle est la durée de vie de mon jeton d’accès ?

Si vous n’utilisez pas de clients compatibles avec l’EAC, la durée de vie par défaut de votre jeton d’accès sera toujours d’une heure, sauf si vous l’avez configuré avec la fonctionnalité d’évaluation [Durée de vie du jeton configurable (CTL)](../develop/active-directory-configurable-token-lifetimes.md).

Si vous utilisez des clients compatibles avec l’EAC qui négocient des sessions prenant en charge l’EAC, vos paramètres CTL pour la durée de vie des jetons d’accès seront remplacés et la durée de vie du jeton d’accès sera de 24 heures.

### <a name="how-quick-is-enforcement"></a>Quelle est la rapidité de la mise en œuvre ?

Bien que notre objectif soit que la mise en œuvre soit instantanée, dans certains cas, une latence pouvant aller jusqu’à 15 minutes peut être observée en raison du temps de propagation de l’événement.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Comment l’EAC fonctionnera-t-elle avec la fréquence de connexion ?

La fréquence de connexion sera respectée avec ou sans EAC.

## <a name="next-steps"></a>Étapes suivantes

[Annonce de l’évaluation de l’accès continu](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
