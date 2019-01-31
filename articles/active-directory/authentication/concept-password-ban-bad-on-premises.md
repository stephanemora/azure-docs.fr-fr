---
title: Protection de mot de passe Azure AD (préversion)
description: Interdisez les mots de passe faibles dans les instances Active Directory locales à l’aide de la protection de mot passe Azure AD en préversion
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: b99c1b99fe87c755d6092876ccd598d926289192
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55077828"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Aperçu : Appliquer la protection de mot de passe Azure AD pour Windows Server Active Directory

|     |
| --- |
| La protection de mot de passe Azure AD et la liste de mots de passe interdits personnalisée sont des fonctionnalités de la préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires des Préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

La protection de mot de passe Azure AD est une nouvelle fonctionnalité disponible en préversion publique et proposée par Azure Active Directory (Azure AD) pour améliorer les stratégies de mot de passe dans une organisation. Le déploiement local de la protection de mot de passe Azure AD utilise des listes de mots de passe interdits aussi bien locales que globales stockées dans Azure AD et effectue les mêmes vérifications en local que les modifications cloud Azure AD.

Trois composants logiciels constituent la protection de mot de passe Azure AD :

* Le service de proxy de protection de mot de passe Azure AD s’exécute sur n’importe quelle machine jointe au domaine dans la forêt Active Directory actuelle. Il transfère les demandes des contrôleurs de domaine à Azure AD et renvoie la réponse d’Azure AD au contrôleur de domaine.
* Le service de l’agent du contrôleur de domaine de protection de mot de passe Azure AD reçoit les demandes de validation de mot de passe de la dll de filtres de mot de passe d’agent de contrôleur de domaine, les traite avec la stratégie de mot de passe locale et renvoie le résultat (réussite/échec). Ce service est chargé d’appeler à des intervalles réguliers (une fois par heure) le service de proxy de protection de mot de passe Azure AD pour récupérer de nouvelles versions de la stratégie de mot de passe. La communication pour les appels en provenance et à destination du service de proxy de protection de mot de passe Azure AD est gérée via RPC sur TCP. Une fois récupérées, les nouvelles stratégies sont stockées dans un dossier sysvol où elles peuvent être répliquées sur d’autres contrôleurs de domaine. Le service de l’agent du contrôleur de domaine surveille également les modifications apportées dans le dossier sysvol dans le cas où d’autres contrôleurs de domaine y auraient écrit de nouvelles stratégies de mot de passe. Si une stratégie récente adaptée est déjà disponible, la vérification du service de proxy de protection de mot de passe Azure AD sera ignorée.
* La dll de filtres de mot de passe de l’agent de contrôleur de domaine reçoit des demandes de validation de mot de passe du système d’exploitation et les transmet au service de l’agent de contrôleur de domaine de protection de mot de passe Azure AD en cours d’exécution localement sur le contrôleur de domaine.

![Comment les composants de protection de mot de passe Azure AD fonctionnent ensemble](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="license-requirements"></a>Conditions de licence :

Les avantages de la liste globale de mots de passe interdits s’appliquent à tous les utilisateurs d’Azure Active Directory (Azure AD).

La liste personnalisée de mots de passe interdits requiert des licences Azure AD Basic.

La protection de mot de passe Azure AD pour Windows Server Active Directory requiert des licences Azure AD Premium.

Vous trouverez des informations de licence supplémentaires, notamment les prix, sur le [site de tarification Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Download

Deux programmes d’installation sont requis pour la protection de mot de passe Azure AD. Vous pouvez les télécharger depuis le [centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="answers-to-common-questions"></a>Réponses à des questions fréquentes

* Aucune connectivité Internet n’est requise pour les contrôleurs de domaine. Les machines exécutant le service de proxy de protection de mot de passe Azure AD sont les seules ayant besoin d’une connectivité Internet.
* Aucun port réseau ne doit être ouvert sur les contrôleurs de domaine.
* Aucune modification de schéma Active Directory n’est requise.
* Le logiciel utilise les objets de schéma serviceConnectionPoint et de conteneur Active Directory existants.
* Aucune configuration minimale n’est requise au niveau fonctionnel de la forêt ou du domaine Active Directory.
* Le logiciel ne crée pas de compte et n’en exige pas dans les domaines Active Directory qu’il protège.
* Le déploiement incrémentiel est pris en charge sous réserve que la stratégie de mot de passe soit uniquement appliquée à l’endroit où l’agent de contrôleur de domaine est installé.
* Il est recommandé d’installer l’agent DC sur tous les contrôleurs de domaine pour assurer l’application de la protection des mots de passe. 
* La protection de mot de passe Azure AD n’est pas un moteur d’application de stratégie en temps réel. Il peut y avoir un décalage entre le moment où la configuration de la stratégie de mot de passe est modifiée et celui où elle atteint et s’applique à tous les contrôleurs de domaine.

## <a name="next-steps"></a>Étapes suivantes

[Déployer la protection par mot de passe d’Azure AD](howto-password-ban-bad-on-premises-deploy.md)
