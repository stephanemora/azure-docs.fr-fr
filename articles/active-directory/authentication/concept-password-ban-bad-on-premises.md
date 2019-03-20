---
title: Protection de mot de passe Azure AD (préversion)
description: Exclure des mots de passe faibles dans Active Directory local à l’aide d’aperçu de protection de mot de passe Azure AD
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
ms.openlocfilehash: 2fdf308ff6178dcb51ec73e46d43b853f62e7777
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840951"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Aperçu : Appliquer la protection de mot de passe Azure AD pour Windows Server Active Directory

|     |
| --- |
| Protection de mot de passe Azure Active Directory (Azure AD) et la liste de mots de passe interdits personnalisés sont des fonctionnalités de préversion publique d’Azure AD. Pour plus d’informations sur les préversions, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Protection de mot de passe Azure AD est une nouvelle fonctionnalité en version préliminaire publique qui améliore les stratégies de mot de passe dans une organisation. Protection de mot de passe de déploiement sur site utilise les deux les globales et personnalisées mot de passe interdits listes qui sont stockés dans Azure AD. Il effectue la même vérifications en local en tant qu’Azure AD pour les modifications de cloud.

## <a name="design-principles"></a>Principes de conception

Protection de mot de passe Azure AD est conçue avec ces principes à l’esprit :

* Contrôleurs de domaine n’ont jamais de communiquer directement avec internet.
* Aucun nouveau port réseau ne doit être ouvert sur les contrôleurs de domaine.
* Aucune modification de schéma Active Directory n’est requise. Le logiciel utilise Active Directory existant **conteneur** et **serviceConnectionPoint** objets de schéma.
* Aucun minimum domaine ou forêt niveau fonctionnel Active Directory (niveau fonctionnel du domaine/FFL) n’est requis.
* Le logiciel ne créer ou requièrent des comptes dans les domaines Active Directory qu’il protège.
* Le contrôleur de domaine ne laissez pas les mots de passe utilisateur en texte clair lors des opérations de validation de mot de passe ou à tout moment.
* Déploiement incrémentiel est pris en charge. Mais la stratégie de mot de passe est appliquée uniquement où est installé l’Agent (Agent contrôleur de domaine) du contrôleur de domaine.
* Nous vous recommandons d’installer l’Agent du contrôleur de domaine sur tous les contrôleurs de domaine pour vous assurer d’application universelle de mot de passe de la sécurité de protection.

## <a name="architectural-diagram"></a>Diagramme architectural

Il est important de comprendre la conception sous-jacente et les concepts de la fonction avant de déployer de protection de mot de passe Azure AD dans un environnement de Active Directory en local. Le diagramme suivant montre comment les composants de protection de mot de passe fonctionnent ensemble :

![Comment les composants de protection de mot de passe Azure AD fonctionnent ensemble](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Le service proxy de la protection par mot de passe Azure AD s’exécute sur toute machine jointe au domaine dans la forêt Active Directory actuelle. Son principal objectif consiste à transférer les demandes de téléchargement de stratégie de mot de passe à partir de contrôleurs de domaine à Azure AD. Il retourne ensuite les réponses à partir d’Azure AD au contrôleur de domaine.
* La DLL de l’Agent du contrôleur de domaine de filtre de mot de passe reçoit les demandes de validation de mot de passe utilisateur à partir du système d’exploitation. Il les transmet au service Agent du contrôleur de domaine qui s’exécute localement sur le contrôleur de domaine.
* Le service de contrôleur de domaine Agent de protection de mot de passe reçoit les demandes de validation de mot de passe à partir de la DLL de l’Agent du contrôleur de domaine de filtre de mot de passe. Il les traite à l’aide de la stratégie de mot de passe (disponible localement) actuel et retourne le résultat : *passer* ou *échouer*.

## <a name="how-password-protection-works"></a>Fonctionne de la protection de mot de passe

Chaque instance du service Proxy de Protection de mot de passe Azure AD se signale aux contrôleurs de domaine dans la forêt en créant un **serviceConnectionPoint** objet dans Active Directory.

Chaque service de l’Agent du contrôleur de domaine pour la protection de mot de passe crée également un **serviceConnectionPoint** objet dans Active Directory. Cet objet est utilisé principalement pour la création de rapports et de diagnostics.

Le service Agent du contrôleur de domaine est chargé de lancer le téléchargement d’une nouvelle stratégie de mot de passe à partir d’Azure AD. La première étape consiste à localiser un service de Proxy de Protection de mot de passe Azure AD en interrogeant la forêt pour le proxy **serviceConnectionPoint** objets. Lorsqu’un service de proxy disponible est trouvé, l’Agent du contrôleur de domaine envoie une demande de téléchargement de stratégie de mot de passe pour le service de proxy. Le service de proxy envoie à son tour la demande à Azure AD. Le service de proxy renvoie ensuite la réponse pour le service Agent du contrôleur de domaine.

Une fois que le service Agent du contrôleur de domaine reçoit une nouvelle stratégie de mot de passe à partir d’Azure AD, le service stocke la stratégie dans un dossier dédié à la racine de son domaine *sysvol* partage du dossier. Le service de contrôleur de domaine Agent surveille également ce dossier au cas où des stratégies plus récente répliquer dans d’autres services de l’Agent du contrôleur de domaine dans le domaine.

Le service Agent du contrôleur de domaine demande toujours une nouvelle stratégie au démarrage du service. Une fois que le service Agent du contrôleur de domaine est démarré, il vérifie l’âge de la stratégie actuelle disponible localement toutes les heures. Si la stratégie est antérieure à une heure, l’Agent du contrôleur de domaine demande une nouvelle stratégie à partir d’Azure AD, comme décrit précédemment. Si la stratégie actuelle n’est pas antérieure à une heure, l’Agent du contrôleur de domaine continue d’utiliser cette stratégie.

Chaque fois qu’une stratégie de mot de passe de protection de mot de passe Azure AD est téléchargée, cette stratégie est spécifique à un locataire. En d’autres termes, les stratégies de mot de passe sont toujours une combinaison de la liste de mot de passe interdit Microsoft globale et la liste de mot de passe interdits personnalisées par client.

L’Agent du contrôleur de domaine communique avec le service de proxy via RPC sur TCP. Le service de proxy écoute pour ces appels sur un port RPC dynamique ou statique, en fonction de la configuration.

L’Agent du contrôleur de domaine est jamais à l’écoute sur un port réseau disponibles.

Le service de proxy n’appelle jamais le service Agent du contrôleur de domaine.

Le service de proxy est sans état. Il met en cache jamais les stratégies ou tout autre état téléchargé à partir d’Azure.

Le service Agent du contrôleur de domaine utilise toujours la stratégie de mot de passe localement disponibles plus récent pour évaluer un mot de passe utilisateur. Si aucune stratégie de mot de passe n’est disponible sur le contrôleur de domaine local, le mot de passe est accepté automatiquement. Lorsque cela se produit, un message d’événement est enregistré pour avertir l’administrateur.

Protection de mot de passe Azure AD n’est pas un moteur d’application de stratégie en temps réel. Il peut y avoir un délai entre quand une modification de configuration de stratégie de mot de passe est effectuée dans Azure AD et lorsque que modifier atteint et si elle est appliquée sur tous les contrôleurs de domaine.

## <a name="foresttenant-binding-for-password-protection"></a>Liaison de forêt ou de client pour la protection de mot de passe

Déploiement de la protection de mot de passe Azure AD dans une forêt Active Directory nécessite l’inscription de cette forêt avec Azure AD. Chaque service de proxy qui est déployée doit également être inscrit auprès d’Azure AD. Ces inscriptions de forêt et de proxy sont associées à spécifique à un locataire Azure AD, qui est implicitement identifié par les informations d’identification qui sont utilisées pendant l’inscription.

La forêt Active Directory et tous les services de proxy déployé au sein d’une forêt doivent être inscrits avec le même client. Il n’est pas pris en charge pour une forêt Active Directory ou les services de proxy dans cette forêt en cours d’inscription à Azure AD différents locataires. Symptômes d’un tel déploiement mal configuré l’impossibilité de télécharger des stratégies de mot de passe.

## <a name="license-requirements"></a>Conditions de licence :

Les avantages de la liste de mots de passe interdits global s’appliquent à tous les utilisateurs d’Azure AD.

La liste de mot de passe interdits personnalisée requiert des licences Azure AD Basic.

La protection de mot de passe Azure AD pour Windows Server Active Directory requiert des licences Azure AD Premium.

Pour obtenir des informations de licence supplémentaires, consultez [tarification Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Téléchargement

Les deux programmes d’installation d’agent requis pour la protection de mot de passe Azure AD sont disponibles à partir de la [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Étapes suivantes
[Déployer la protection par mot de passe d’Azure AD](howto-password-ban-bad-on-premises-deploy.md)
