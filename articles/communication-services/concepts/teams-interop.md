---
title: Interopérabilité des réunions Teams
titleSuffix: An Azure Communication Services concept document
description: Participer aux réunions Teams
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e884079bc159dbbc76d6443dc0e095c4d8f596ab
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114668093"
---
# <a name="teams-interoperability"></a>Interopérabilité de Teams

> [!IMPORTANT]
> L’interopérabilité des BYOI est disponible en version préliminaire publique et largement disponible à la demande. Pour activer/désactiver l’[interopérabilité des locataires Teams](../concepts/teams-interop.md), remplissez [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).
>
> L'interopérabilité authentifiée de Microsoft 365 est en phase de prévisualisation privée et limitée, à l'aide de contrôles de service, aux premiers utilisateurs d'Azure Communication Services. Pour participer au programme d'accès anticipé, remplissez [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8MfnD7fOYZEompFbYDoD4JUMkdYT0xKUUJLR001ODdQRk1ITTdOMlRZNSQlQCN0PWcu).
>
> Les API et kits de développement logiciel (SDK) en préversion sont fournis sans contrat de niveau de service, et déconseillés pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Communication Services peut être utilisé pour créer des applications personnalisées qui interagissent avec Microsoft Teams. Les utilisateurs finaux de vos applications Communication Services peuvent interagir avec des participants Teams via l’audio, la vidéo, la conversation et le partage d’écran. La vidéo suivante illustre l’utilisation de cette fonctionnalité :


<br>
<br>


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWGTqQ]


Azure Communication Services prend en charge deux types d’interopérabilité Teams en fonction de l’identité de l’utilisateur final :

- **Apportez votre propre identité.** Vous contrôlez l’authentification utilisateur et les utilisateurs de vos applications personnalisées n’ont pas besoin d’avoir des identités Azure Active Directory ou des licences Teams pour rejoindre des réunions sur Teams. Teams traite votre application comme un utilisateur externe anonyme.
- **Identité Teams Microsoft 365.** Votre application agit pour le compte de l’identité Microsoft 365 d’un utilisateur final et de ses ressources configurées Teams. Ces applications authentifiées peuvent effectuer des appels et joindre des réunions de manière transparente pour le compte des utilisateurs Microsoft 365.

Les applications peuvent implémenter les deux schémas d’authentification et laissent le choix de l’authentification à l’utilisateur final.

## <a name="bring-your-own-identity"></a>Apportez votre propre identité
BYOI (Bring your own identity) est le modèle commun pour l’utilisation d’Azure Communication Services et de l’interopérabilité avec Teams. Il prend en charge tous les fournisseurs d’identité et tous les schémas d’authentification. Votre application peut rejoindre les réunions Microsoft Teams et Teams traitera ces utilisateurs comme des comptes externes anonymes. Le nom des utilisateurs d’Azure Communication Services est affiché dans Teams est configurable via le kit de développement logiciel (SDK) d’Azure Communication Services.

Cette fonctionnalité est idéale pour les applications interentreprises qui rassemblent les employés (familiarisés avec Teams) et les utilisateurs externes (à l’aide d’une expérience d’application personnalisée) dans une réunion. Les détails de la réunion qui doivent être partagés avec les utilisateurs externes de votre application peuvent être récupérés via l’API Graph ou à partir du calendrier dans Microsoft Teams.

Les utilisateurs externes pourront utiliser les fonctionnalités audio, vidéo, de partage d’écran et de conversation de base via les kits de développement logiciel (SDK) d’Azure communication services. Les fonctionnalités telles que Lever la main, le mode ensemble et les salles de pause ne seront disponibles que pour les utilisateurs Teams. Les utilisateurs d’Azure Communication Services peuvent envoyer et recevoir des messages uniquement lorsqu’ils sont présents dans la réunion Teams et si la réunion n’est pas planifiée pour un canal précis.

Votre application personnalisée doit envisager l’authentification des utilisateurs et d’autres mesures de sécurité pour protéger les réunions Teams. Pensez à ce que cela implique en terme de sécurité si vous autorisez les utilisateurs anonymes à rejoindre des réunions et utilisez le [Guide sur la sécurité de Teams](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) pour configurer les fonctionnalités disponibles pour les utilisateurs anonymes.

Vous trouverez des informations supplémentaires sur les flux requis pour la participation à des réunions Teams sur la [page architecture du client et du serveur](client-and-server-architecture.md). L'[exemple d'appel de groupe Hero](../samples/calling-hero-sample.md) fournit un code d'exemple pour rejoindre une réunion Teams à partir d'une application Web.

## <a name="microsoft-365-teams-identity"></a>Identité Teams Microsoft 365
Le kit de développement logiciel (SDK) d’Azure Communication Services peut être utilisé avec les identités Teams de Microsoft 365 pour prendre en charge les expériences de l’interopérabilité avec Teams. Les identités de Teams Microsoft 365 sont fournies et authentifiées par Azure Active Directory. Votre application peut effectuer ou accepter des appels avec une identité de Microsoft 365 régulière. Tous les attributs et les détails relatifs à l’utilisateur sont liés à l’utilisateur Azure Active Directory.

Ce modèle d’identité est idéal pour les cas d’usage où une interface utilisateur personnalisée est nécessaire, où le client Teams n’est pas disponible pour votre plateforme, ou où le client Teams ne prend pas en charge un niveau de personnalisation suffisant. Par exemple, une application peut être utilisée pour répondre aux appels téléphoniques au nom de l’utilisateur final via le numéro rtpc configuré dans Teams et disposer d’une interface utilisateur optimisée pour un processus d’entreprise de réceptionniste ou de centre d’appels.  

Les fonctionnalités d’appel et de partage d’écran sont disponibles via le kit de développement logiciel (SDK) d’Azure Communication Services. La gestion des appels est disponible via l’API Graph, la configuration dans le client Teams ou le portail d’administration Teams. La fonctionnalité de conversation est disponible via l’API Graph.

Les utilisateurs Teams sont authentifiés via la bibliothèque MSAL par rapport aux utilisateurs Azure Active Directory de l’application cliente. Les jetons d’authentification sont échangés pour Microsoft 365 via le kit de développement logiciel (SDK) d’Azure Communication Services. Il est recommandé d’implémenter un échange de jetons dans vos services principaux, car les demandes d’échanges sont signées par des informations d’identification pour Azure Communication Services. Dans vos services backend, vous pouvez exiger une authentification supplémentaire.

Pour en savoir plus sur cette fonctionnalité, rejoignez notre programme TAP pour un accès en avant-première en complétant [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8MfnD7fOYZEompFbYDoD4JUMkdYT0xKUUJLR001ODdQRk1ITTdOMlRZNSQlQCN0PWcu).

## <a name="comparison"></a>Comparaison

|Critères|Apportez votre propre identité| Identité Teams Microsoft 365|
|---|---|---|
|Applicable| Dans les scénarios interentreprises pour les applications grand public | Dans les scénarios interentreprises ou entreprise à consommateur sur les applications métier |
|Fournisseur d’identité|Quelconque|Azure Active Directory|
|Authentification et autorisation|Personnalisé*| Azure Active Directory et personnalisé *|
|Appel disponible via | Des SDK d’appel avec Azure Communication Services | Des SDK d’appel avec Azure Communication Services |
|Conversation disponible via | SDK de conversation avec Azure Communication Services | API Graph |
|Prise en charge RTC| appel vocal sortant, routage direct sortant, [détails](./telephony-sms/telephony-concept.md) | appel entrant affecté à l’identité Teams, appel sortant à l’aide du plan appelant|

La logique du serveur \* émettant des jetons d’accès peut effectuer toute authentification et autorisation personnalisée de la demande.

## <a name="privacy"></a>Confidentialité
L’interopérabilité entre Azure Communication Services et Microsoft Teams permet à vos applications et utilisateurs de participer à des appels, réunions et conversations Teams. Il vous incombe de vous assurer que les utilisateurs de votre application sont avertis quand l’enregistrement ou la transcription sont activés dans le cadre d’un appel ou d’une réunion Teams.

Microsoft vous indique par le biais de l’API Azure Communication Services que l’enregistrement ou la transcription a commencé et vous devez communiquer ce fait, en temps réel, à vos utilisateurs dans l’interface utilisateur de votre application. Vous acceptez d’indemniser Microsoft pour tous les coûts et dommages résultant de votre non-respect de cette obligation.

## <a name="pricing"></a>Tarifs
Toute utilisation des API et SDK Azure communication services incrémente les [compteurs de facturation d’ Azure Communication Service](https://azure.microsoft.com/pricing/details/communication-services/). Les Interactions avec Microsoft Teams, telles que la participation à une réunion ou le lancement d’un appel téléphonique à l’aide d’un numéro Teams alloué, incrémentent ces compteurs, mais il n’y a pas de frais supplémentaires pour la fonctionnalité d’interopérabilité Teams elle-même, et il n’existe aucune distinction entre les options d’authentification BYOI et Microsoft 365.

Si votre application Azure a un utilisateur final consacrant 10 minutes à une réunion avec un utilisateur de Microsoft Teams, ces deux utilisateurs ont consommé 20 minutes d’appel. Les 10 minutes exercées par le biais de l’application personnalisée et l’utilisation des API et SDK Azure seront facturées à votre ressource. Toutefois, les 10 minutes consommées par l’utilisateur final dans l’application Teams native sont couvertes par la licence Teams applicable et ne sont pas limitées par Azure.

## <a name="teams-in-government-clouds-gcc"></a>Teams dans les clouds gouvernementaux (GCC)
L’interopérabilité d’Azure Communication Services n’est pour l’instant pas compatible avec les déploiement Teams utilisant des [clouds gouvernementaux (GCC) Microsoft 365](/MicrosoftTeams/plan-for-government-gcc).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Associer une application d'appel BYOI à une réunion Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)
> [Authentifier les utilisateurs de Microsoft 365](../quickstarts/manage-teams-identity.md)
