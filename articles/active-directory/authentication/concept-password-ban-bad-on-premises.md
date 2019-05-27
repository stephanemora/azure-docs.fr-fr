---
title: Protection de mot de passe Azure AD - Azure Active Directory
description: Exclure des mots de passe faibles dans Active Directory local à l’aide de protection de mot de passe Azure AD
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
ms.openlocfilehash: 644054960e12979c231bbf50a5979bc12d343f89
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694763"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Appliquer la protection de mot de passe Azure AD pour Windows Server Active Directory

La protection de mot de passe Azure AD est une fonctionnalité qui améliore les stratégies de mot de passe dans une organisation. Le déploiement local de la protection de mot de passe utilise à la fois les listes globales et personnalisées de mot de passe interdit qui sont stockées dans Azure AD. Il effectue les mêmes vérifications lors des changements que ce soit en local ou sur Azure AD.

## <a name="design-principles"></a>Principes de conception

La protection de mot de passe Azure AD est conçue avec ces principes à l’esprit :

* Les contrôleurs de domaine n'ont pas à communiquer directement avec internet.
* Aucun nouveau port réseau ne doit être ouvert sur les contrôleurs de domaine.
* Aucune modification de schéma Active Directory n’est requise. Le logiciel utilise les objets de schéma **conteneur** et **serviceConnectionPoint** existants dans Active Directory.
* Aucun niveau fonctionnel Active Directory minimum n'est requis ni pour le domaine ni pour la forêt (DFL/FFL).
* Le logiciel ne crée pas et n'exige pas de comptes dans les domaines Active Directory qu’il protège.
* Les mots de passe utilisateur en texte clair ne quittent jamais le contrôleur de domaine, que ce soit pendant les opérations de validation de mot de passe ou à tout autre moment.
* Le logiciel ne dépend d'aucune autre fonctionnalité Azure AD ; par exemple la synchronisation du hachage de mot de passe Azure AD n’est pas liée et n’est pas requise pour que fonctionne la protection de mot de passe Azure AD.
* Le déploiement incrémentiel est pris en charge, mais la stratégie de mot de passe est appliquée uniquement où est installé l’Agent de contrôleur de domaine (DC Agent). Consultez la rubrique suivante pour plus d’informations.

## <a name="incremental-deployment"></a>Déploiement incrémentiel

La "protection de mot de passe Azure AD" prend en charge le déploiement incrémentiel entre les contrôleurs de domaine dans un domaine Active Directory, mais il est important de comprendre ce que cela signifie et quels sont les compromis.

L’agent de protection de mot de passe Azure AD permet de valider uniquement les mots de passe lorsqu’il est installé sur un contrôleur de domaine et uniquement pour les modifications de mot de passe qui sont envoyées à ce contrôleur de domaine. Il n’est pas possible de contrôler les contrôleurs de domaine choisis par les ordinateurs clients Windows pour le traitement des modifications de mot de passe utilisateur. Afin de garantir un comportement cohérent et une application universelle de la sécurité et de la protection du mot de passe, l’agent du contrôleur de domaine doit être installé sur tous les contrôleurs de domaine dans un domaine.

De nombreuses organisations souhaiteront effectuer des tests approfondis de protection par mot de passe Azure AD sur un sous-ensemble de leurs contrôleurs de domaine avant de faire un déploiement complet. La protection de mot de passe Azure AD ne prend pas en charge le déploiement partiel, c'est à dire que l’agent installé sur un contrôleur de domaine donné validera activement les mots de passe même si les autres contrôleurs dans le domaine n'ont pas l'agent installé. Les déploiements partiels de ce type ne sont pas sécurisés et ne sont pas recommandés sauf à des fins de test.

## <a name="architectural-diagram"></a>Diagramme architectural

Il est important de comprendre la conception sous-jacente et les concepts de la fonction avant de déployer la protection de mot de passe Azure AD dans un environnement local Active Directory. Le diagramme suivant montre comment les composants de la protection de mot de passe fonctionnent ensemble :

![Comment les composants de protection de mot de passe Azure AD fonctionnent ensemble](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Le service proxy de la protection par mot de passe Azure AD s’exécute sur toute machine jointe au domaine de la forêt Active Directory actuelle. Son principal objectif consiste à transférer les demandes de téléchargement de stratégie de mot de passe depuis les contrôleurs de domaine vers Azure AD. Il retourne ensuite les réponses depuis Azure AD vers le contrôleur de domaine.
* La DLL de filtrage de mot de passe de l’Agent reçoit les demandes de validation de mot de passe utilisateur à partir du système d’exploitation. Elle les transmet au service de l'Agent du contrôleur de domaine qui s’exécute localement sur le contrôleur de domaine.
* Le service de l'Agent de protection de mot de passe reçoit les demandes de validation de mot de passe à partir de la DLL de filtrage de mot de passe de l’Agent du contrôleur de domaine. Il les traite à l’aide de la stratégie actuelle de mot de passe (disponible localement) et retourne le résultat : *Réussi* ou *Echec*.

## <a name="how-password-protection-works"></a>Fonctionnement de la protection de mot de passe

Chaque instance du service Proxy de Protection de mot de passe Azure AD se signale aux contrôleurs de domaine dans la forêt en créant un objet **serviceConnectionPoint** dans Active Directory.

Chaque service de l’Agent du contrôleur de domaine pour la protection de mot de passe crée également un objet **serviceConnectionPoint** dans Active Directory. Cet objet est utilisé principalement pour la création de rapports et de diagnostics.

Le service Agent du contrôleur de domaine est chargé de lancer le téléchargement d’une nouvelle stratégie de mot de passe à partir d’Azure AD. La première étape consiste à localiser un service de Proxy de Protection de mot de passe Azure AD en interrogeant la forêt pour les objets Proxy de type **serviceConnectionPoint**. Lorsqu’un service de proxy disponible est trouvé, l’Agent du contrôleur de domaine envoie une demande de téléchargement de stratégie de mot de passe pour le service de proxy. Le service de proxy envoie à son tour la demande à Azure AD. Le service de proxy renvoie ensuite la réponse pour le service Agent du contrôleur de domaine.

Une fois que le service Agent du contrôleur de domaine reçoit une nouvelle stratégie de mot de passe à partir d’Azure AD, le service stocke la stratégie dans un dossier dédié à la racine de son domaine dans le partage de dossier *sysvol*. Le service de contrôleur de domaine Agent surveille également ce dossier au cas où des stratégies plus récentes seraient répliquées depuis d’autres services de l’Agent DC dans le domaine.

Le service Agent du contrôleur de domaine demande toujours une nouvelle stratégie au démarrage du service. Une fois que le service Agent du contrôleur de domaine est démarré, il vérifie l’âge de la stratégie actuelle disponible localement toutes les heures. Si la stratégie est antérieure à une heure, l’Agent du contrôleur de domaine demande une nouvelle stratégie à partir d’Azure AD via le service de proxy, comme décrit précédemment. Si la stratégie actuelle n’est pas antérieure à une heure, l’Agent du contrôleur de domaine continue d’utiliser cette stratégie.

Chaque fois qu’une stratégie de mot de passe de protection de mot de passe Azure AD est téléchargée, cette stratégie est spécifique à un locataire. En d’autres termes, les stratégies de mot de passe sont toujours une combinaison de la liste de mots de passe interdits Microsoft globale et la liste de mots de passe interdits personnalisée par locataire.

L’Agent du contrôleur de domaine communique avec le service de proxy via RPC sur TCP. Le service de proxy écoute pour ces appels sur un port RPC dynamique ou statique, en fonction de la configuration.

L’Agent du contrôleur de domaine n'est jamais à l’écoute sur un port réseau disponible.

Le service de proxy n’appelle jamais le service Agent du contrôleur de domaine.

Le service de proxy est sans état. Il ne met jamais en cache les stratégies ou tout autre état téléchargé à partir d’Azure.

Le service Agent du contrôleur de domaine utilise toujours la stratégie de mot de passe la plus récente disponible localement pour évaluer un mot de passe utilisateur. Si aucune stratégie de mot de passe n’est disponible sur le contrôleur de domaine local, le mot de passe est accepté automatiquement. Lorsque cela se produit, un message d’événement est enregistré pour avertir l’administrateur.

La protection de mot de passe Azure AD n’est pas un moteur d’application de stratégie en temps réel. Il peut y avoir un délai entre la modification de configuration de stratégie de mot de passe effectuée dans Azure AD et le moment où la modification atteint tous les contrôleurs de domaine et est appliquée.

La protection de mot de passe Azure AD agit comme un supplément pour les stratégies de mot de passe Active Directory existantes, et n'est pas un remplacement. Cela inclut toutes les autres DLL de filtrage de mot de passe tierces qui peuvent être installées. Active Directory nécessite toujours que tous les composants de validation de mot de passe soient d’accord avant d’accepter un mot de passe.

## <a name="foresttenant-binding-for-password-protection"></a>Liaison de forêt ou de client pour la protection de mot de passe

Déploiement de la protection de mot de passe Azure AD dans une forêt Active Directory nécessite l’inscription de cette forêt avec Azure AD. Chaque service de proxy qui est déployée doit également être inscrit auprès d’Azure AD. Ces inscriptions de forêt et de proxy sont associées à spécifique à un locataire Azure AD, qui est implicitement identifié par les informations d’identification qui sont utilisées pendant l’inscription.

La forêt Active Directory et tous les services de proxy déployé au sein d’une forêt doivent être inscrits avec le même client. Il n’est pas pris en charge pour une forêt Active Directory ou les services de proxy dans cette forêt en cours d’inscription à Azure AD différents locataires. Symptômes d’un tel déploiement mal configuré l’impossibilité de télécharger des stratégies de mot de passe.

## <a name="download"></a>Téléchargement

Les deux programmes d’installation d’agent requis pour la protection de mot de passe Azure AD sont disponibles à partir de la [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Étapes suivantes
[Déployer la protection par mot de passe d’Azure AD](howto-password-ban-bad-on-premises-deploy.md)
