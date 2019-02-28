---
title: Protection de mot de passe Azure AD (préversion)
description: Interdisez les mots de passe faibles dans les instances Active Directory locales à l’aide de la protection de mot passe Azure AD en préversion
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1beae186f6eb276b9aa302d3d51f0ba8688e591
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415746"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Aperçu : Appliquer la protection de mot de passe Azure AD pour Windows Server Active Directory

|     |
| --- |
| La protection de mot de passe Azure AD et la liste de mots de passe interdits personnalisée sont des fonctionnalités de la préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires des Préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

La protection de mot de passe Azure AD est une nouvelle fonctionnalité disponible en préversion publique et proposée par Azure Active Directory (Azure AD) pour améliorer les stratégies de mot de passe dans une organisation. Le déploiement local de la protection de mot de passe Azure AD utilise des listes de mots de passe interdits aussi bien locales que globales stockées dans Azure AD et effectue les mêmes vérifications en local que les modifications cloud Azure AD.

## <a name="design-principles"></a>Principes de conception

La protection par mot de passe Azure AD pour Active Directory est conçue en gardant les principes suivants à l’esprit :

* Les contrôleurs de domaine ne sont jamais nécessaires pour communiquer directement avec Internet
* Aucun nouveau port réseau ne doit être ouvert sur les contrôleurs de domaine.
* Aucune modification de schéma Active Directory n’est requise. Le logiciel utilise les objets de schéma serviceConnectionPoint et de conteneur Active Directory existants.
* Aucun niveau fonctionnel minimal du domaine ou de la forêt (DFL\FFL) Active Directory n’est exigé.
* Le logiciel ne crée pas de compte et n’en exige pas dans les domaines Active Directory qu’il protège.
* Les mots de passe utilisateur en texte clair ne quittent jamais le contrôleur de domaine (que ce soit pendant les opérations de validation de mot de passe ou à tout autre moment).
* Le déploiement incrémentiel est pris en charge sous réserve que la stratégie de mot de passe soit uniquement appliquée à l’endroit où l’agent de contrôleur de domaine est installé.
* Il est recommandé d’installer l’agent contrôleur de domaine sur tous les contrôleurs de domaine, afin de garantir l’application d’une sécurité omniprésente basée sur la protection par mot de passe.

## <a name="architectural-diagram"></a>Diagramme architectural

Il est important de comprendre la conception sous-jacente et les concepts fonctionnels avant de déployer la protection par mot de passe Azure AD dans un environnement Active Directory en local. Le schéma suivant montre comment les composants de la protection par mot de passe Azure AD opèrent ensemble :

![Comment les composants de protection de mot de passe Azure AD fonctionnent ensemble](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Le schéma ci-dessus montre les trois composants logiciels de base qui constituent la protection par mot de passe Azure AD :

* Le service proxy de la protection par mot de passe Azure AD s’exécute sur toute machine jointe au domaine dans la forêt Active Directory actuelle. Sa première finalité est de transférer les requêtes de téléchargement de stratégie de mot de passe, à partir des contrôleurs de domaine vers Azure AD, et de retourner la réponse, d’Azure AD au contrôleur de domaine.
* La dll-filtre de mot de passe de l’agent contrôleur de domaine de la protection par mot de passe Azure AD reçoit du système d’exploitation les requêtes de validation de mot de passe. Elle les transmet ensuite au service d’agent contrôleur de domaine de la protection par mot de passe Azure AD qui s’exécute localement sur le contrôleur de domaine.
* Le service d’agent contrôleur de domaine de la protection par mot de passe Azure AD reçoit les requêtes de validation de mot de passe de la dll-filtre, les traite avec la stratégie de mot de passe (disponible localement) et retourne le résultat (réussite\échec).

## <a name="theory-of-operations"></a>Principes de fonctionnement

Si l’on considère les principes de conception et le schéma présentés plus haut, comment la protection par mot de passe Azure AD fonctionne-t-elle en réalité ?

Chaque service proxy de la protection par mot de passe Azure AD se signale aux contrôleurs de domaine dans la forêt en créant un objet serviceConnectionPoint dans Active Directory.

Chaque service d’agent contrôleur de domaine de la protection par mot de passe Azure AD crée également un objet serviceConnectionPoint dans Active Directory. Son utilisation, toutefois, est principalement réservée à la création de rapports et de diagnostics.

Le service d’agent contrôleur de domaine de la protection par mot de passe est chargé d’initier le téléchargement d’une nouvelle stratégie de mot de passe à partir d’Azure AD. La première étape consiste à localiser un service proxy de la protection par mot de passe Azure AD en recherchant dans la forêt des objets proxy serviceConnectionPoint. Dès qu’un service proxy disponible est trouvé, une requête de téléchargement de stratégie de mot de passe est envoyée du service d’agent contrôleur de domaine au service proxy, qui à son tour l’envoie à Azure AD, puis retourne la réponse au service d’agent contrôleur de domaine. À réception d’une nouvelle stratégie de mot de passe provenant d’Azure AD, le service d’agent contrôleur de domaine stocke la stratégie dans un dossier dédié, à la racine de son partage sysvol de domaine. Le service d’agent contrôleur de domaine supervise également ce dossier, au cas où des stratégies plus récentes s’y répliqueraient à partir d’autres services d’agent contrôleur de domaine dans le domaine.

Le service d’agent contrôleur de domaine de la protection par mot de passe Azure AD demande toujours une nouvelle stratégie au démarrage du service. Dès qu’il est démarré, le service d’agent contrôleur de domaine vérifie périodiquement (toutes les heures) l’âge de la stratégie actuelle disponible localement. Si cette stratégie actuelle est plus vieille d’une heure, le service d’agent contrôleur de domaine demande une nouvelle stratégie auprès d’Azure AD, comme décrit ci-dessus ; si tel n’est pas le cas, l’agent contrôleur de domaine continue d’utiliser la stratégie actuelle.

Le service d’agent contrôleur de domaine de la protection par mot de passe Azure AD communique avec le service proxy de la protection par mot de passe Azure AD via RPC (Appel de procédure distante) sur TCP. Le service proxy écoute ces appels sur un port RPC statique ou dynamique (tel que configuré).

L’agent contrôleur de domaine de la protection par mot de passe Azure AD n’écoute jamais sur un port de réseau disponible, et le service proxy n’essaie jamais d’appeler le service d’agent contrôleur de domaine.

Le service proxy de la protection par mot de passe Azure AD est sans état ; il ne met jamais en cache les stratégies ni aucun autre état téléchargé à partir d’Azure.

Le service d’agent contrôleur de domaine de la protection par mot de passe Azure AD n’évalue un mot de passe utilisateur qu’avec la stratégie de mot de passe la plus récente disponible localement. Si aucune stratégie de mot de passe n’est disponible sur le contrôleur de domaine local, le mot de passe est accepté automatiquement et un message de journal des événements est journalisé pour avertir l’administrateur.

La protection par mot de passe Azure AD n’est pas un moteur d’application de stratégie en temps réel. Il peut exister un décalage entre le moment où la configuration de la stratégie de mot de passe est modifiée dans Azure AD et celui où elle atteint tous les contrôleurs de domaine pour y être appliquée.

## <a name="foresttenant-binding-for-azure-ad-password-protection"></a>Liaison forêt\locataire de la protection par mot de passe Azure AD

Le déploiement de la protection par mot de passe Azure AD dans une forêt Active Directory nécessite l’inscription auprès d’Azure AD de la forêt Active Directory et des services proxy de la protection par mot de passe Azure AD déployés. Ces deux inscriptions (forêt et proxys) sont associées à un locataire Azure AD particulier, qui est implicitement identifié par le biais des informations d’identification utilisées lors de l’inscription. Chaque fois qu’une stratégie de mot de passe de la protection par mot de passe Azure AD est téléchargée, elle est toujours spécifique à ce locataire (autrement dit, cette stratégie est toujours une combinaison de la liste globale des mots de passe interdits de Microsoft avec la liste personnalisée des mots de passe interdits par le locataire). La configuration de différents domaines ou proxys dans une forêt devant être liée à différents locataires Azure AD n’est pas prise en charge.

## <a name="license-requirements"></a>Conditions de licence :

Les avantages de la liste globale de mots de passe interdits s’appliquent à tous les utilisateurs d’Azure Active Directory (Azure AD).

La liste personnalisée de mots de passe interdits requiert des licences Azure AD Basic.

La protection par mot de passe d’Azure AD pour Windows Server Active Directory nécessite des licences Azure AD Premium.

Vous trouverez des informations de licence supplémentaires, notamment les prix, sur le [site de tarification Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Téléchargement

Les deux programmes d’installation d’agent nécessaires à la protection par mot de passe Azure AD peuvent être téléchargés à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="next-steps"></a>Étapes suivantes

[Déployer la protection par mot de passe d’Azure AD](howto-password-ban-bad-on-premises-deploy.md)
