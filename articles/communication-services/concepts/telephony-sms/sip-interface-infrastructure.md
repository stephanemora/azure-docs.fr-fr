---
title: Exigences d’infrastructure de l’interface SIP - Azure Communication Services
description: Familiarisez-vous avec les exigences d’infrastructure pour la configuration de l’interface SIP d’Azure Communication Services
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ede650ae072ef53ed40a9372a292ab69fe8cc1af
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492725"
---
# <a name="sip-interface-infrastructure-requirements"></a>Exigences d’infrastructure de l’interface SIP 

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

 
Cet article décrit les détails de connectivité de l’infrastructure, des licences et des contrôleurs de frontière de session (SBC, Session Border Controller) que vous devez garder à l’esprit quand vous planifiez votre déploiement de l’interface SIP.


## <a name="infrastructure-requirements"></a>Exigences de l’infrastructure
Les exigences en matière d’infrastructure pour les contrôleurs de frontière de session (SBC) et pour les domaines, et les autres exigences de connectivité réseau pour le déploiement de l’interface SIP, sont listées dans le tableau suivant :  

|Exigence d’infrastructure|Vous avez besoin de ceci|
|:--- |:--- |
|Contrôleur de frontière de session (SBC)|Un SBC pris en charge. Pour plus d’informations, consultez [SBC pris en charge](#supported-session-border-controllers-sbcs).|
|Réseaux téléphoniques connectés au SBC|Un ou plusieurs réseaux téléphoniques connectés au SBC. À une extrémité, le SBC se connecte à Azure Communication Services via l’interface SIP. Le SBC peut également se connecter à des entités de téléphonie de tiers, comme des PABX, des adaptateurs de téléphonie analogique, etc. N’importe quelle option de connectivité RTCP connectée au SBC va fonctionner. (Pour la configuration des réseaux RTCP pour le SBC, reportez-vous aux fournisseurs SBC ou aux fournisseurs de réseau téléphonique.)|
|Abonnement Azure|Un abonnement Azure que vous utilisez pour créer des ressources ACS, et la configuration et la connexion au SBC.|
|Jeton d’accès à Communication Services|Pour passer des appels, vous avez besoin d’un jeton d’accès valide avec l’étendue `voip`. Consultez [Jetons d’accès](../identity-model.md#access-tokens)|
|Adresse IP publique pour le SBC|Adresse IP publique qui peut être utilisée pour se connecter au SBC. En fonction du type de SBC, celui-ci peut utiliser la traduction d’adresses réseau (NAT).|
|Nom de domaine complet (FQDN) pour le SBC|Nom de domaine complet pour le SBC, où la partie « domaine » du nom de domaine complet ne correspond pas aux domaines inscrits dans votre organisation Microsoft 365 ou Office 365. Pour plus d’informations, consultez [Noms de domaine SBC](#sbc-domain-names).|
|Entrée DNS publique pour le SBC |Entrée DNS publique mappant le nom de domaine complet du SBC à l’adresse IP publique. |
|Certificat public approuvé pour le SBC |Un certificat pour le SBC, à utiliser pour toutes les communications avec l’interface SIP. Pour plus d’informations, consultez [Certificat public approuvé pour le SBC](#public-trusted-certificate-for-the-sbc).|
|Adresses IP et ports du pare-feu pour la signalisation SIP et le multimédia |Le SBC communique avec les services suivants dans le cloud :<br/><br/>Proxy SIP, qui gère la signalisation<br/>Processeur multimédia, qui gère le multimédia<br/><br/>Ces deux services ont des adresses IP distinctes dans Microsoft Cloud, décrites plus loin dans ce document.


## <a name="sbc-domain-names"></a>Noms de domaine SBC

Les clients sans Office 365 peuvent utiliser n’importe quel nom de domaine pour lequel ils peuvent obtenir un certificat public.

Le tableau suivant présente des exemples de noms DNS inscrits pour le locataire, indique si le nom peut être utilisé comme nom de domaine complet (FQDN) pour le SBC et contient des exemples de noms de domaine complets valides :

|Nom DNS|Peut être utilisé pour le nom de domaine complet SBC|Exemples de noms de domaine complets|
|:--- |:--- |:--- |
contoso.com|Oui|**Noms valides :**<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|Non|L’utilisation de domaines *.onmicrosoft.com n’est pas prise en charge pour les noms SBC

Si vous êtes client Office 365, le nom de domaine SBC ne doit pas correspondre à celui inscrit dans les domaines du locataire Office 365. Voici l’exemple de la coexistence d’Office 365 et d’Azure Communication Services :

|Domaine inscrit dans Office 365|Exemples de nom de domaine complet SBC dans Teams|Exemples de nom de domaine complet SBC dans ACS|
|:--- |:--- |:--- |
**contoso.com** (domaine de second niveau)|**sbc.contoso.com** (nom dans le domaine de second niveau)|**sbc.acs.contoso.com** (nom dans le domaine de troisième niveau)<br/>**sbc.fabrikam.com** (n’importe quel nom dans un domaine différent)|
|**o365.contoso.com** (domaine de troisième niveau)|**sbc.o365.contoso.com** (nom dans le domaine de troisième niveau)|**sbc.contoso.com** (nom dans le domaine de second niveau)<br/>**sbc.acs.o365.contoso.com** (nom dans le domaine de quatrième niveau)<br/>**sbc.fabrikam.com** (n’importe quel nom dans un domaine différent)

L’appairage SBC fonctionne sur le niveau de ressource de Communication Services, ce qui signifie que vous pouvez appairer plusieurs SBC à une même ressource Communication Services, mais que vous ne pouvez pas appairer un même SBC à plusieurs ressources Communication Services. Des noms de domaine complets SBC uniques sont nécessaires pour l’appairage à différentes ressources.

## <a name="public-trusted-certificate-for-the-sbc"></a>Certificat public approuvé pour le SBC

Microsoft vous recommande de demander le certificat pour le SBC en générant une demande de signature de certification (CSR). Pour obtenir des instructions spécifiques sur la génération d’une demande de signature de certification pour un SBC, reportez-vous aux instructions d’interconnexion ou à la documentation des fournisseurs de votre SBC. 

  > [!NOTE]
  > La plupart des autorités de certification demandent que la taille de la clé privée soit au moins égale à 2 048. Gardez cela à l’esprit lors de la génération de la demande de signature de certification.

Le certificat doit avoir le nom de domaine complet SBC pour le champ Nom commun (CN) ou Autre nom du sujet (SAN). Le certificat doit être émis directement depuis une autorité de certification et non pas depuis un fournisseur intermédiaire.

L’interface SIP de Communication Services prend également en charge un caractère générique dans le champ CN et/ou SAN, et ce caractère générique doit être conforme au standard [RFC HTTP sur TLS](https://tools.ietf.org/html/rfc2818#section-3.1). 

Un exemple serait l’utilisation de `\*.contoso.com` qui correspondrait au nom de domaine complet SBC `sbc.contoso.com`, mais ne correspondrait pas à `sbc.test.contoso.com`.

Le certificat doit être généré par une des autorités de certification racines suivantes :

- AffirmTrust
- AddTrust External CA Root
- Baltimore CyberTrust Root*
- Buypass
- Cybertrust
- Class 3 Public Primary Certification Authority
- Comodo Secure Root CA
- Deutsche Telekom 
- DigiCert Global Root CA
- DigiCert High Assurance EV Root CA
- Entrust
- GlobalSign
- Go Daddy
- GeoTrust
- Verisign, Inc. 
- SSL.com
- Starfield
- Symantec Enterprise Mobile Root for Microsoft 
- SwissSign
- Thawte Timestamping CA
- Trustwave
- TeliaSonera 
- T-Systems International GmbH (Deutsche Telekom)
- QuoVadis

Microsoft travaille sur l’ajout d’autorités de certification supplémentaires en fonction des demandes des clients. 

## <a name="sip-signaling-fqdns"></a>Signalisation SIP : Noms de domaine complets 

Les points de connexion pour l’interface SIP de Communication Services sont les trois noms de domaine complets suivants :

- **sip.pstnhub.microsoft.com** - Nom de domaine complet global - doit être essayé en premier. Quand le SBC envoie une demande pour résoudre ce nom, les serveurs DNS Microsoft Azure retournent une adresse IP pointant vers le centre de données Azure principal affecté au SBC. L’affectation est basée sur les métriques de performance des centres de données et sur la proximité géographique du SBC. L’adresse IP retournée correspond au nom de domaine complet principal.
- **sip2.pstnhub.microsoft.com** - Nom de domaine complet secondaire - mappé géographiquement à la région de deuxième priorité.
- **sip3.pstnhub.microsoft.com** - Nom de domaine complet tertiaire - mappé géographiquement à la région de troisième priorité.

Placer ces trois noms de domaine complets dans l’ordre est nécessaire pour :

- Offrir une expérience optimale (moins chargée et plus proche du centre de centres SBC affecté en interrogeant le premier nom de domaine complet).
- Fournir un basculement lorsque la connexion depuis un SBC est établie avec un centre de données qui rencontre un problème temporaire. Pour plus d’informations, consultez [Mécanisme de basculement](#failover-mechanism-for-sip-signaling) ci-dessous.  

Les noms de domaine complets - sip2.pstnhub.microsoft.com et sip3.pstnhub.microsoft.com - sont résolus en une des adresses IP suivantes :

- `52.114.148.0`
- `52.114.132.46`
- `52.114.75.24` 
- `52.114.76.76` 
- `52.114.7.24` 
- `52.114.14.70`
- `52.114.16.74`
- `52.114.20.29`

Ouvrez les ports du pare-feu pour ces adresses IP afin d’autoriser le trafic entrant et sortant vers et depuis les adresses pour la signalisation. Si votre pare-feu prend en charge les noms DNS, le nom de domaine complet `sip-all.pstnhub.microsoft.com` est résolu en toutes ces adresses IP. 

## <a name="sip-signaling-ports"></a>Signalisation SIP : Ports

Utilisez les ports suivants pour l’interface SIP de Communication Services :

|Trafic|Du|À|Port source|Port de destination|
|:--- |:--- |:--- |:--- |:--- |
|SIP/TLS|Proxy SIP|SBC|1 024 – 65 535|Défini sur le SBC (pour Office 365 GCC High/DoD, seul le port 5061 doit être utilisé)|
SIP/TLS|SBC|Proxy SIP|Défini sur le SBC|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>Mécanisme de basculement pour la signalisation SIP

Le SBC fait une requête DNS pour résoudre sip.pstnhub.microsoft.com. En fonction de l’emplacement du SBC et des métriques de performance du centre de données, le centre de données principal est sélectionné. Si le centre de données principal rencontre un problème, le SBC essaie sip2.pstnhub.microsoft.com, dont la résolution aboutit au second centre de données affecté, et, dans les rares cas où les centres de données de deux régions ne sont pas disponibles, le SBC réessaye le dernier nom de domaine complet (sip3.pstnhub.microsoft.com), qui fournit l’adresse IP du centre de données tertiaire.

## <a name="media-traffic-ip-and-port-ranges"></a>Traffic multimédia : Plages d’adresses IP et de ports

Le trafic multimédia transite vers et depuis un service distinct appelé Processeur multimédia. Au moment de la publication de Processeur multimédia pour ACS, vous pouvez utiliser n’importe quelle adresse IP Azure. Téléchargez [la liste complète des adresses](https://www.microsoft.com/download/details.aspx?id=56519).

### <a name="port-range"></a>Plage de ports
La plage de ports des processeurs multimédias est indiquée dans le tableau suivant : 

|Trafic|Du|À|Port source|Port de destination|
|:--- |:--- |:--- |:--- |:--- |
|UDP/SRTP|Processeur multimédia|SBC|3478 - 3481 et 49152 - 53247|Défini sur le SBC|
|UDP/SRTP|SBC|Processeur multimédia|Défini sur le SBC|3478 - 3481 et 49152 - 53247|

  > [!NOTE]
  > Microsoft recommande au moins deux ports par appel simultané sur le SBC.


## <a name="media-traffic-media-processors-geography"></a>Traffic multimédia : Emplacement géographique des processeurs multimédias

Le trafic multimédia transite par des composants appelés processeurs multimédias. Les processeurs multimédias sont placés dans les mêmes centres de données que les proxys SIP. En outre, il existe d’autres processeurs multimédias pour optimiser le flux multimédia. Par exemple, nous n’avons pas de composant proxy SIP en Australie (SIP transite via Singapour ou Hong Kong (R.A.S.)), mais nous avons un processeur multimédia local en Australie. La nécessité d’utiliser des processeurs multimédias localement est dictée par la latence que nous expérimentons en envoyant du trafic longue distance, par exemple de l’Australie à Singapour ou à Hong-Kong (R.A.S.). Bien que la latence dans l’exemple de trafic transitant de l’Australie à Hong Kong (R.A.S.) ou à Singapour soit acceptable pour préserver une qualité d’appel correcte pour le trafic SIP, ce n’est pas le cas pour le trafic multimédia en temps réel.

Emplacements où les composants Proxy SIP et Processeur multimédia sont tous deux déployés :
- USA (deux dans les centres de données USA Ouest et USA Est)
- Europe (centres de données d’Amsterdam et de Dublin)
- Asie (centre de données de Singapour et de Hong Kong (R.A.S.))
- Australie (centres de données Australie Est et Sud-Ouest)

Emplacements où seuls des processeurs multimédias sont déployés (SIP transite via le centre de données le plus proche listé ci-dessus) :
- Japon (centres de données Japon Est et Ouest)


## <a name="media-traffic-codecs"></a>Traffic multimédia : Codecs

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>Tronçon entre SBC et un processeur multimédia cloud ou un client Microsoft Teams.
S’applique à la fois aux cas de dérivation et de non-dérivation du multimédia.

L’interface de routage direct sur le tronçon entre le SBC et le processeur multimédia cloud peut utiliser les codecs suivants :

- SILK, G.711, G.722, G.729

Vous pouvez forcer l’utilisation du codec spécifique sur le SBC en excluant de l’offre les codecs indésirables.

### <a name="leg-between-acs-sdk-app-and-cloud-media-processor"></a>Tronçon entre l’application SDK ACS et le processeur multimédia Cloud

Sur le tronçon entre le processeur multimédia cloud et l’application SDK ACS, SILK ou G.722 est utilisé. Le choix du codec sur ce tronçon est basé sur des algorithmes Microsoft, qui prennent en compte plusieurs paramètres. 

## <a name="supported-session-border-controllers-sbcs"></a>Contrôleurs de SBC

La certification est en cours. Pendant ce temps, les clients peuvent utiliser des [contrôleurs SBC certifiés pour Teams](/MicrosoftTeams/direct-routing-border-controllers). 

## <a name="next-steps"></a>Étapes suivantes

### <a name="conceptual-documentation"></a>Documentation conceptuelle

- [Concepts de la téléphonie](./telephony-concept.md)
- [Types de numéros de téléphone dans Azure Communication Services](./plan-solution.md)
- [Tarification](../pricing.md)

### <a name="quickstarts"></a>Démarrages rapides

- [Appel à un téléphone](../../quickstarts/voice-video-calling/pstn-call.md)