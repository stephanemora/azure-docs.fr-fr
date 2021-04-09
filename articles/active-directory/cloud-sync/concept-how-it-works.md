---
title: Exploration approfondie de la synchronisation cloud Azure AD Connect – Fonctionnement
description: Cette rubrique fournit des informations détaillées sur le fonctionnement de la synchronisation cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612558"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>Exploration approfondie de la synchronisation cloud – Fonctionnement

## <a name="overview-of-components"></a>Présentation des composants

![Fonctionnement](media/concept-how-it-works/how-1.png)

La synchronisation cloud est basée sur les services Azure AD et comporte deux composants clés :

- **Agent d’approvisionnement** : L’agent d’approvisionnement cloud d’Azure AD Connect est le même agent que celui entrant de Workday et est basé sur la même technologie côté serveur que le proxy d’application et l’authentification directe. Il ne nécessite qu’une connexion sortante et les agents sont mis à jour automatiquement. 
- **Service d’approvisionnement** : Même service d’approvisionnement que l’approvisionnement sortant et l’approvisionnement entrant de Workday qui utilise un modèle basé sur le planificateur. Dans le cas de la synchronisation cloud, les modifications sont approvisionnées toutes les deux minutes.


## <a name="initial-setup"></a>Configuration initiale
Lors de la configuration initiale, quelques opérations sont effectuées pour permettre la synchronisation cloud.  Ces règles sont les suivantes : 

- **Pendant l’installation de l’agent** : Vous configurez l’agent pour les domaines AD à partir desquels vous souhaitez approvisionner.  Cette configuration inscrit les domaines dans le service d’identité hybride et établit une connexion sortante vers le bus de service à l’écoute des demandes.
- **Lorsque vous activez l’approvisionnement** : Vous sélectionnez le domaine AD et activez l’approvisionnement qui s’exécute toutes les deux minutes. Si vous le souhaitez, vous pouvez désélectionner la synchronisation du hachage de mot de passe et définir une adresse e-mail de notification. Vous pouvez également gérer la transformation des attributs à l’aide d’API Microsoft Graph.


## <a name="agent-installation"></a>Installation de l’agent
Voici une description pas à pas de ce qui se produit lorsque l’agent d’approvisionnement cloud est installé.

- Tout d’abord, le programme d’installation installe les fichiers binaires de l’agent et le service d’agent s’exécutant sous le compte de service virtuel (NETWORK SERVICE\AADProvisioningAgent).  Un compte de service virtuel est un type spécial de compte qui ne dispose pas d’un mot de passe et qui est géré par Windows.
- Le programme d’installation démarre ensuite l’Assistant.
- L’Assistant vous invite à entrer les informations d’identification d’Azure AD, puis s’authentifie et récupère un jeton.
- L’Assistant demande ensuite les informations d’identification des administrateurs de domaine de l’ordinateur actuel.
- Grâce à ces informations d’identification, le compte de service géré général de l’agent (GMSA) pour ce domaine est soit créé, soit localisé et réutilisé s’il existe déjà.
- Le service de l’agent est maintenant reconfiguré pour s’exécuter sous le GMSA.
- L’Assistant demande à présent la configuration du domaine avec le compte Administrateur d’entreprise (EA)/Administrateur de domaine (DA) pour chaque domaine que l’agent doit traiter.
- Le compte GMSA est ensuite mis à jour avec des autorisations qui lui permettent d’accéder à chaque domaine entré ci-dessus.
- Ensuite, l’Assistant déclenche l’inscription de l’agent.
- L’agent crée un certificat et utilise le jeton Azure AD, s’inscrit lui-même et le certificat auprès du service d’inscription du service d’identité hybride (HIS).
- L’Assistant déclenche un appel AgentResourceGrouping. Cet appel au service d’administration du HIS consiste à attribuer l’agent à un ou plusieurs domaines AD dans la configuration du HIS.
- L’Assistant redémarre maintenant le service de l’agent.
- L’agent appelle un service de démarrage au redémarrage (et toutes les 10 minutes par la suite) pour vérifier les mises à jour de la configuration.  Le service de démarrage valide l’identité de l’agent.  Il met également à jour la dernière heure de démarrage.  Cela est important, car si les agents ne démarrent pas, ils n’obtiennent pas les points de terminaison Service Bus mis à jour et peuvent ne pas être en mesure de recevoir les demandes. 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Qu’est-ce que SCIM (System for Cross-Domain Identity Management) ?

La [spécification SCIM](https://tools.ietf.org/html/draft-scim-core-schema-01) est une norme utilisée pour automatiser l’échange d’informations d’identité d’utilisateur ou de groupe entre des domaines d’identité tels qu’Azure AD. SCIM devient de facto la norme pour l’approvisionnement et, lorsqu’elle est utilisée conjointement avec des normes de fédération comme SAML ou OpenID Connect, fournit aux administrateurs une solution de bout en bout basée sur des normes pour la gestion des accès.

L’agent d’approvisionnement cloud d’Azure AD Connect utilise SCIM avec Azure AD pour attribuer et supprimer les privilèges d’accès d’utilisateurs et de groupes.

## <a name="synchronization-flow"></a>Flux de synchronisation
![approvisionnement](media/concept-how-it-works/provisioning-4.png) Une fois que vous avez installé l’agent et activé l’approvisionnement, le processus suivant se produit.

1.  Une fois configuré, le service d’approvisionnement Azure AD appelle le service hybride Azure AD pour ajouter une requête au bus de service. L’agent maintient en permanence une connexion sortante vers le bus de service à l’écoute des demandes et récupère immédiatement la demande SCIM (System for Cross-domain Identity Management). 
2.  L’agent décompose la demande en plusieurs requêtes distinctes basées sur le type d’objet. 
3.  Active Directory renvoie le résultat à l’agent, et l’agent filtre ces données avant de les envoyer à Azure AD.  
4.  L’agent renvoie la réponse SCIM à Azure AD.  Ces réponses sont basées sur le filtrage qui s’est produit au sein de l’agent.  L’agent utilise l’étendue pour filtrer les résultats. 
5.  Le service d’approvisionnement écrit les modifications dans Azure AD.
6. S’il s’agit d’une synchronisation différentielle par opposition à une synchronisation complète, le cookie/filigrane est utilisé. Les nouvelles requêtes recevront les modifications à partir de ce cookie/filigrane.

## <a name="supported-scenarios"></a>Scénarios pris en charge :
Les scénarios suivants sont pris en charge pour la synchronisation cloud.


- **Client hybride actuel avec une nouvelle forêt** : La synchronisation d’Azure AD Connect est utilisée pour les forêts principales. La synchronisation cloud est utilisée pour l’approvisionnement à partir d’une forêt AD (y compris déconnectée). Pour plus d’informations, consultez le tutoriel [ici](tutorial-existing-forest.md).

 ![Hybride actuel](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **Nouveau client hybride** :      La synchronisation d’Azure AD Connect n’est pas utilisée. La synchronisation cloud est utilisée pour l’approvisionnement à partir d’une forêt AD.  Pour plus d’informations, consultez le tutoriel [ici](tutorial-single-forest.md).
 
 ![Nouveaux clients](media/tutorial-single-forest/diagram-2.png)

- **Client hybride actuel** : La synchronisation d’Azure AD Connect est utilisée pour les forêts principales. La synchronisation cloud est pilotée par un petit ensemble d’utilisateurs dans les forêts principales [ici](tutorial-existing-forest.md).

 ![Pilote actuel](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

Pour en savoir plus, consultez [Topologies prises en charge](plan-cloud-sync-topologies.md).



## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)
