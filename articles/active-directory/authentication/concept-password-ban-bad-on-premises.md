---
title: Protection par mot de passe Azure AD – Azure Active Directory
description: Interdisez les mots de passe faibles dans Active Directory en local en utilisant la protection par mot de passe Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848644"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Appliquer la protection de mot de passe Azure AD pour Windows Server Active Directory

La protection par mot de passe Azure AD est une fonctionnalité qui améliore les stratégies de mot de passe au sein d’une organisation. Un déploiement local de protection par mot de passe utilise les listes globale et personnalisée de mots de passe interdits qui sont stockées dans Azure AD. Il effectue les mêmes vérifications locales qu’Azure AD pour les modifications basées sur le cloud. Ces vérifications sont effectuées lors des modifications de mots de passe et des scénarios de réinitialisation de mot de passe.

## <a name="design-principles"></a>Principes de conception

La protection par mot de passe Azure AD est conçue dans le respect des principes suivants :

* Les contrôleurs de domaine ne doivent jamais communiquer directement avec Internet.
* Aucun nouveau port réseau ne doit être ouvert sur les contrôleurs de domaine.
* Aucune modification de schéma Active Directory n’est requise. Le logiciel utilise le **conteneur** Active Directory et les objets de schéma **serviceConnectionPoint** existants.
* Aucun niveau fonctionnel minimal de domaine ou de forêt (DFL\FFL) Active Directory n’est requis.
* Le logiciel ne crée pas de compte et n’en exige pas dans les domaines Active Directory qu’il protège.
* Les mots de passe utilisateur en texte clair ne quittent jamais le contrôleur de domaine, que ce soit pendant les opérations de validation de mot de passe ou à tout autre moment.
* Le logiciel ne dépend pas d’autres fonctionnalités Azure AD. Par exemple, aucune synchronisation du hachage de mot de passe Azure AD n’est associée et ou requise pour que la protection par mot de passe Azure AD fonctionne.
* Le déploiement incrémentiel est pris en charge, sous réserve que la stratégie de mot de passe soit appliquée uniquement à l’endroit où l’agent du contrôleur de domaine est installé. Pour plus d’informations, voir la rubrique suivante.

## <a name="incremental-deployment"></a>Déploiement incrémentiel

La protection par mot de passe Azure AD prend en charge le déploiement incrémentiel sur les contrôleurs de domaine dans un domaine Active Directory, mais il est important de comprendre ce que cela signifie vraiment et les compromis que cela entraîne.

Le logiciel de l’agent du contrôleur de domaine pour la protection par mot de passe Azure AD ne peut valider les mots de passe que s’il est installé sur un contrôleur de domaine, et uniquement pour des changements de mot de passe envoyés à ce contrôleur de domaine. Il n’est pas possible de contrôler les contrôleurs de domaine que les ordinateurs clients Windows choisissent pour le traitement des changements de mot de passe utilisateur. Afin de garantir un comportement cohérent et l’application universelle de la sécurité de protection par mot de passe, le logiciel de l’agent du contrôleur de domaine DOIT être installé sur tous les contrôleurs de domaine au sein d’un domaine.

De nombreuses organisations tiennent à tester soigneusement la protection par mot de passe Azure AD sur un sous-ensemble de leurs contrôleurs de domaine avant d’opérer un déploiement complet. La protection par mot de passe Azure AD ne prend pas en charge un déploiement partiel. Cela signifie que le logiciel de l’agent du contrôleur de domaine sur un contrôleur de domaine donné valide activement les mots de passe, même si ce logiciel n’est pas installé sur d’autres contrôleurs de domaine dans le domaine. Des déploiements partiels de ce type ne sont PAS SÉCURISÉS et ne sont PAS RECOMMANDÉS à des fins autres que de test.

## <a name="architectural-diagram"></a>Diagramme architectural

Avant de déployer la protection par mot de passe Azure AD dans un environnement Active Directory local, il est important d’en comprendre la conception et les concepts fonctionnels sous-jacents. Le diagramme suivant montre comment les composants de la protection par mot de passe Azure AD opèrent ensemble :

![Comment les composants de protection de mot de passe Azure AD fonctionnent ensemble](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Le service proxy de la protection par mot de passe Azure AD s’exécute sur toute machine jointe au domaine dans la forêt Active Directory actuelle. Son principal objectif est de transférer les demandes de téléchargement de stratégie de mot de passe des contrôleurs de domaine à Azure AD. Il retourne ensuite les réponses d’Azure AD au contrôleur de domaine.
* La DLL de filtre de mot de passe de l’agent du contrôleur de domaine reçoit les demandes de validation de mot de passe utilisateur du système d’exploitation. Elle les transmet au service de l’agent du contrôleur de domaine qui s’exécute localement sur le contrôleur de domaine.
* Le service de protection par mot de passe de l’agent du contrôleur de domaine reçoit les demandes de validation de mot de passe en provenance de la DLL de filtre de mot de passe de l’agent du contrôleur de domaine. Il les traite en appliquant la stratégie de mot de passe en vigueur (disponible localement) et retourne le résultat : *pass* (réussite) ou *fail* (échec).

## <a name="how-password-protection-works"></a>Fonctionnement de la protection par mot de passe

Chaque instance du service proxy de protection par mot de passe Azure AD se signale aux contrôleurs de domaine dans la forêt en créant un objet **serviceConnectionPoint** dans Active Directory.

Chaque service de l’agent du contrôleur de domaine pour la protection par mot de passe crée également un objet **serviceConnectionPoint** dans Active Directory. Cet objet est principalement utilisé pour la création de rapports et les diagnostics.

Le service de l’agent du contrôleur de domaine est chargé d’initier le téléchargement d’une nouvelle stratégie de mot de passe à partir d’Azure AD. La première étape consiste à localiser un service proxy de protection par mot de passe Azure AD en recherchant dans la forêt des objets **serviceConnectionPoint** proxy. Quand un service proxy disponible est trouvé, l’agent du contrôleur de domaine envoie une demande de téléchargement de stratégie de mot de passe à ce service proxy. Le service proxy envoie à son tour la demande à Azure AD. Le service proxy renvoie ensuite la réponse au service de l’agent du contrôleur de domaine.

Après réception d’une nouvelle stratégie de mot de passe d’Azure AD, le service de l’agent du contrôleur de domaine stocke la stratégie dans un dossier dédié à la racine du partage de dossiers *sysvol* de son domaine. Le service de l’agent du contrôleur de domaine supervise également ce dossier, au cas où des stratégies plus récentes s’y répliqueraient à partir d’autres services de l’agent du contrôleur de domaine dans le domaine.

Le service de l’agent du contrôleur de domaine demande toujours une nouvelle stratégie au démarrage. Après démarrage, le service de l’agent du contrôleur de domaine vérifie toutes les heures l’âge de la stratégie en vigueur disponible localement. Si la stratégie a plus d’une heure, l’agent du contrôleur de domaine demande à Azure AD une nouvelle stratégie via le service proxy, comme décrit précédemment. Si la stratégie actuelle n’a pas plus d’une heure, l’agent du contrôleur de domaine continue de l’utiliser.

Chaque fois qu’une stratégie de protection par mot de passe Azure AD est téléchargée, elle est spécifique d’un locataire. En d’autres termes, les stratégies de mot de passe sont toujours une combinaison de la liste globale des mots de passe interdits de Microsoft et de la liste des mot de passe interdits personnalisée par le client.

L’agent du contrôleur de domaine communique avec le service proxy via RPC sur TCP. Le service proxy écoute ces appels sur un port RPC statique ou dynamique en fonction de la configuration.

L’agent du contrôleur de domaine n’écoute jamais un port réseau disponible.

Le service proxy n’appelle jamais le service de l’agent du contrôleur de domaine.

Le service proxy est sans état. Il ne met jamais en cache les stratégies ou tout autre état téléchargé à partir d’Azure.

Le service de l’agent du contrôleur de domaine utilise toujours la stratégie de mot de passe la plus récente disponible localement pour évaluer le mot de passe d’un utilisateur. Si aucune stratégie de mot de passe n’est disponible sur le contrôleur de domaine local, le mot de passe est accepté automatiquement. Lorsque cela se produit, un message d’événement est journalisé pour avertir l’administrateur.

La protection par mot de passe Azure AD n’est pas un moteur d’application de stratégie en temps réel. Il peut exister un décalage entre le moment où la configuration de la stratégie de mot de passe est modifiée dans Azure AD et celui où cette modification atteint tous les contrôleurs de domaine et y est appliquée.

La protection par mot de passe Azure AD complète les stratégies de mot de passe Active Directory existantes. Elle ne les remplace pas. Cela englobe toutes les autres DLL de filtre de mot de passe tierces qui peuvent être installées. Avant d’accepter un mot de passe, Active Directory exige toujours que tous les composants de validation de mot de passe marquent leur accord.

## <a name="foresttenant-binding-for-password-protection"></a>Liaison forêt\locataire pour la protection par mot de passe

Le déploiement de la protection par mot de passe Azure AD dans une forêt Active Directory nécessite l’inscription de cette forêt auprès d’Azure AD. Chaque service proxy déployé doit également être inscrit auprès d’Azure AD. Ces inscriptions de forêt et de proxy sont associées avec un locataire Azure AD spécifique, qui est implicitement identifié par les informations d’identification utilisées lors de l’inscription.

La forêt Active Directory et tous les services proxy déployés au sein d’une forêt doivent être inscrits auprès du même locataire. Il n’est pas possible d’avoir une forêt Active Directory ou des services proxy au sein de cette forêt inscrits auprès de différents locataires Azure AD. La configuration erronée d’un déploiement se traduit notamment par l’impossibilité de télécharger des stratégies de mot de passe.

## <a name="download"></a>Téléchargement

Les deux programmes d’installation d’agent requis pour la protection par mot de passe Azure AD sont disponibles dans le[Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Étapes suivantes
[Déployer la protection par mot de passe d’Azure AD](howto-password-ban-bad-on-premises-deploy.md)
