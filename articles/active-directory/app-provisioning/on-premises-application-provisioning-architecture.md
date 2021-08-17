---
title: Architecture du provisionnement d’applications local Azure AD | Microsoft Docs
description: Donne une vue d’ensemble de l’architecture du provisionnement d’applications local.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d996ebf8641b4b2829ecbe7cb70ed50280f89391
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451567"
---
# <a name="azure-ad-on-premises-application-provisioning-architecture"></a>Architecture de provisionnement d’applications local Azure AD

>[!IMPORTANT]
> Actuellement, la préversion du provisionnement local est sur invitation uniquement. Pour demander l’accès à la fonctionnalité, utilisez le [formulaire de demande d’accès](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale (GA).

## <a name="overview"></a>Vue d’ensemble

Le diagramme suivant présente une vue d’ensemble du fonctionnement du provisionnement d’applications local.

![Diagramme qui montre l’architecture de l’approvisionnement d’applications locales.](.\media\on-premises-application-provisioning-architecture\arch-3.png)

Il existe trois composants principaux pour provisionner les utilisateurs dans une application locale :

- L’agent de provisionnement assure la connectivité entre Azure Active Directory (Azure AD) et votre environnement local.
- L’hôte ECMA convertit les demandes de provisionnement d’Azure AD en demandes adressées à votre application cible. Il sert de passerelle entre Azure AD et votre application. Vous pouvez l’utiliser pour importer les connecteurs ECMA2 existants utilisés avec Microsoft Identity Manager. L’hôte ECMA n’est pas obligatoire si vous avez créé une application SCIM ou une passerelle SCIM.
- Le service de provisionnement Azure AD sert de moteur de synchronisation.

>[!NOTE]
> La synchronisation de Microsoft Identity Manager n’est pas obligatoire. Toutefois, vous pouvez l’utiliser pour créer et tester votre connecteur ECMA avant de l’importer dans l’hôte ECMA.


### <a name="firewall-requirements"></a>Configuration requise du pare-feu

Vous n’avez pas besoin d’ouvrir de connexion sortante sur le réseau d’entreprise. Les agents de provisionnement utilisent uniquement des connexions sortantes vers le service de provisionnement, ce qui signifie qu’il n’est pas nécessaire d’ouvrir des ports de pare-feu pour les connexions entrantes. Vous n’avez pas non plus besoin de réseau de périmètre (DMZ) parce que toutes les connexions sont sortantes et sur un canal sécurisé.

## <a name="agent-best-practices"></a>Bonnes pratiques concernant l’agent
- Vérifiez que le service de mise à jour automatique de l’agent de provisionnement Azure AD Connect est en cours d’exécution. Elle est activée par défaut lorsque vous installez l’agent. La mise à jour automatique est nécessaire pour que Microsoft prenne en charge votre déploiement.
- Évitez toute forme d’inspection inline sur les communications TLS sortantes établies entre les agents et Azure. Ce type d’inspection inline entraîne une dégradation du flux de communication.
- L’agent doit communiquer avec Azure et votre application, donc le placement de l’agent affecte la latence de ces deux connexions. Vous pouvez réduire la latence du trafic de bout en bout en optimisant chaque connexion réseau. Chaque connexion peut être optimisée en :
  - Réduisant la distance entre les deux extrémités du tronçon.
  - Choisissant le réseau approprié à parcourir. Par exemple, parcourir un réseau privé au lieu de l’Internet public peut être plus rapide en raison des liaisons dédiées.

## <a name="provisioning-agent-questions"></a>Questions relatives à l'agent d'approvisionnement
Certaines questions courantes sont traitées ici.

### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Quelle est la version mise à la disposition générale de l'agent d'approvisionnement ?

Pour obtenir la dernière version GA de l’agent d’approvisionnement, consultez [Azure ad Connect Configuration agent : historique de publication des versions](provisioning-agent-release-version-history.md).

### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Comment connaître la version de mon agent d'approvisionnement ?

 1. Connectez-vous au serveur Windows sur lequel l’agent d’approvisionnement est installé.
 1. Accédez à **Panneau de configuration** > **Désinstaller ou modifier un programme**.
 1. Recherchez la version correspondant à l'entrée **Agent d'approvisionnement Microsoft Azure AD Connect**.

### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft fournit-il automatiquement les mises à jour de l'agent d'approvisionnement ?

Oui. Microsoft met à jour automatiquement l’agent de provisionnement si le service Windows Microsoft Azure AD Connect Agent Updater est en cours d’exécution. Vous devez vous assurer que votre agent est à jour pour pouvoir résoudre les problèmes.

### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect-or-microsoft-identity-manager"></a>Est-ce que je peux installer l’agent de provisionnement sur le même serveur qui exécute Azure AD Connect ou Microsoft Identity Manager ?

Oui. Vous pouvez installer l’agent de provisionnement sur le même serveur qui exécute Azure AD Connect ou Microsoft Identity Manager, mais ils ne sont pas requis.

### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Comment configurer l'agent d'approvisionnement afin qu'il utilise un serveur proxy pour la communication HTTP sortante ?

L'agent d'approvisionnement prend en charge l'utilisation du proxy sortant. Vous pouvez le configurer en modifiant le fichier de configuration **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config** de l'agent. Ajoutez les lignes suivantes à la fin du fichier, juste avant la balise `</configuration>` de fermeture. Remplacez les variables `[proxy-server]` et `[proxy-port]` par le nom de votre serveur proxy et les valeurs de port.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```
### <a name="how-do-i-ensure-the-provisioning-agent-can-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Comment savoir que l'agent d'approvisionnement peut communiquer avec le locataire Azure AD et qu'aucun pare-feu ne bloque les ports requis par l'agent ?

Vous pouvez également vérifier si tous les ports requis sont ouverts.

### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Comment désinstaller l'agent d'approvisionnement ?
1. Connectez-vous au serveur Windows sur lequel l’agent d’approvisionnement est installé.
1. Accédez à **Panneau de configuration** > **Désinstaller ou modifier un programme**.
1. Désinstallez les programmes suivants :
     - Agent d'approvisionnement Microsoft Azure AD Connect
     - Programme de mise à jour de l'agent Microsoft Azure AD Connect
     - Package Agent d'approvisionnement Microsoft Azure AD Connect


## <a name="next-steps"></a>Étapes suivantes

- [Provisionnement d’applications](user-provisioning.md)
- [Prérequis de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-prerequisites.md)
- [Installation de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-install.md)
- [Configuration de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-configure.md)
