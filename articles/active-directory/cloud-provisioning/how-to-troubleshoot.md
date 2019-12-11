---
title: Résolution des problèmes de provisionnement cloud Azure AD Connect
description: Ce document explique comment résoudre les problèmes pouvant survenir avec l’agent de provisionnement cloud.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 4100886e0a24fa961b9085bd507ae3f4ebfdd6eb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795448"
---
# <a name="cloud-provisioning-troubleshooting"></a>Résolution des problèmes de provisionnement cloud

Le provisionnement cloud a une incidence sur bien des éléments et compte de nombreuses dépendances différentes.  À l’évidence, cet état de fait peut donner lieu à des problèmes divers.  Ce document est conçu pour vous aider à résoudre ces problèmes.  Dans ce document vous sont présentées les zones classiques auxquelles vous devez apporter une attention particulière, la façon dont rassembler des informations supplémentaires et diverses techniques pouvant être utilisées pour détecter des problèmes.  


## <a name="common-troubleshooting-areas"></a>Zones courantes de résolution de problème

|Nom|Description|
|-----|-----|
|[Problèmes liés à l’agent](#agent-issues)|Vérifier que l’agent a été correctement installé et qu’il communique avec Azure AD.|
|[Problèmes de synchronisation d’objet](#object-synchronization-issues)|Utiliser les journaux de provisionnement pour résoudre les problèmes de synchronisation d’objet.|
|[Problèmes de mise en quarantaine du provisionnement](#provisioning-quarantined-issues)|Comprendre les problèmes de mise en quarantaine du provisionnement et la façon de les corriger.|


## <a name="agent-issues"></a>Problèmes liés à l’agent
Voici quelques-unes des premières choses à vérifier au niveau de l’agent :


-  est-il installé ?
-  l’agent s’exécute-t-il localement ?
-  l’agent est-il dans le portail ?
-  l’agent est-il marqué comme sain ?  

Ces éléments peuvent se contrôler dans le portail Azure et sur le serveur local qui exécute l’agent.

### <a name="azure-portal-agent-verification"></a>Vérification de l’agent dans le portail Azure

Pour vérifier que l’agent est visible par Azure et qu’il est sain, procédez comme suit :

1. Connectez-vous au portail Azure.
2. Sur la gauche, sélectionnez **Azure Active Directory**, cliquez sur **Azure AD Connect** et, au centre, sélectionnez **Gérer le provisionnement (préversion)** .
3.  Dans l’écran **Provisionnement Azure AD (préversion)** , cliquez sur **Passer en revue tous les agents**.
 ![Provisionnement Azure AD](media/how-to-install/install7.png)</br>
 
4. Dans l’écran **Agents de provisionnement locaux**, vous voyez les agents que vous avez installés.  Vérifiez que l’agent en question est présent et qu’il est marqué comme étant **Sain**.
 ![Agents de provisionnement](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Vérifier le port

Pour vérifier que le service Azure est à l’écoute sur le port 443 et que votre agent peut communiquer avec lui, vous pouvez utiliser l’outil suivant :

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ce test permet de vérifier que vos agents sont en mesure de communiquer avec Azure via le port 443.  Ouvrez un navigateur et accédez à l’URL ci-dessus à partir du serveur sur lequel l’agent est installé.
 ![Services](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Sur le serveur local

Pour vérifier que l’agent est en cours d’exécution, effectuez les étapes suivantes :

1.  Sur le serveur où l’agent est installé, ouvrez **Services** en y accédant ou en accédant à Démarrer/Exécuter/Services.msc.
2.  Sous **Services**, assurez-vous que le **Programme de mise à jour de l’agent Microsoft Azure AD Connect** et l’**Agent de provisionnement Microsoft Azure AD Connect** sont présents, et que leur état est **En cours d’exécution**.
 ![Services](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-issues"></a>Problèmes courants de l’installation de l’agent

Voici quelques problèmes courants liés à l’installation de l’agent, et leur résolution standard.

#### <a name="agent-failed-to-start"></a>Échec du démarrage de l’agent

Si vous recevez un message d’erreur indiquant que :

**Le service « Agent de provisionnement Microsoft Azure AD Connect » n’a pas pu démarrer.  Veillez à avoir les droits nécessaires pour démarrer les services système.** 

Cela est généralement dû à une stratégie de groupe qui empêchait l’application d’autorisations sur le « compte d’ouverture de session » du service NT local créé par le programme d’installation (NT SERVICE\AADConnectProvisioningAgent). Ces autorisations sont nécessaires pour démarrer le service.

Pour résoudre ce problème, effectuez les étapes suivantes :

1.  Connectez-vous au serveur avec un compte administrateur.
2.  Ouvrez **Services** en y accédant ou en accédant à Démarrer/Exécuter/Services.msc.
3.  Sous **Services**, double-cliquez sur **Agent de provisionnement Microsoft Azure AD Connect**.
4. Sous l’onglet, remplacez le « Compte d’ouverture de session » par celui d’un administrateur de domaine et redémarrez le service. 

 ![Services](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Le délai de l’agent a expiré ou le certificat n’est pas valide

Si vous tentez d’inscrire l’agent, les erreurs suivantes peuvent s’afficher.

 ![Services](media/how-to-troubleshoot/troubleshoot4.png)

Cela est généralement dû au fait que l’agent ne peut pas se connecter au service d’identité hybride, et que la configuration d’un proxy HTTP est nécessaire.  Pour résoudre ce problème, configurez un proxy sortant. 

L'agent d'approvisionnement prend en charge l'utilisation du proxy sortant. Vous pouvez le configurer en modifiant le fichier de configuration **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config** de l'agent. Ajoutez les lignes suivantes à la fin du fichier, juste avant la balise `</configuration>` de fermeture.
Remplacez les variables [proxy-server] et [proxy-port] par le nom de votre serveur proxy et les valeurs de port.

```xml
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

#### <a name="agent-registration-fails-with-security-error"></a>L’inscription de l’agent échoue avec une erreur de sécurité

L’erreur suivante peut s’afficher lors de l’installation de l’agent de provisionnement cloud.

Cela est généralement dû à l’incapacité de l’agent à exécuter les scripts d’inscription PowerShell en raison des stratégies d’exécution PowerShell locales.

Pour résoudre cela, changez les stratégies d’exécution PowerShell sur le serveur. Les stratégies de la machine et de l’utilisateur doivent être définies sur « Undefined » ou « RemoteSigned ». Si elles sont « Unrestricted », cette erreur s’affiche.  Pour plus d’informations, consultez [Stratégies d’exécution PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Fichiers journaux

Par défaut, l’agent émet très peu de messages d’erreur et d’informations sur la trace de la pile. Vous pouvez trouver ces journaux de suivi dans le dossier : **C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace**

Utilisez les étapes suivantes pour obtenir des détails supplémentaires sur la résolution des problèmes liés à l’agent.

1. Arrêtez le service **Agent de provisionnement Microsoft Azure AD Connect**.
2. Créez une copie du fichier de configuration d’origine : **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**.
3. Remplacez la section <system.diagnostics> existante par le code suivant, et tous les messages de suivi seront dès lors consignés dans le fichier **ProvAgentTrace.log**.

      ```xml
        <system.diagnostics>
            <sources>
            <source name="AAD Connect Provisioning Agent">
                <listeners>
                <add name="console"/>
                <add name="etw"/>
                <add name="textWriterListener"/>
                </listeners>
            </source>
            </sources>
            <sharedListeners>
            <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
            <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
                <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
            </add>
            <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
            </sharedListeners>
        </system.diagnostics>

      ```
4. Démarrez le service **Microsoft Azure AD Connect Provisioning Agent**.
5. Vous pouvez utiliser la commande suivante pour mettre fin au fichier et déboguer les problèmes : 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-issues"></a>Problèmes de synchronisation d’objet

La section suivante contient des informations sur la résolution des problèmes de synchronisation d’objet.

### <a name="provisioning-logs"></a>Journaux d’approvisionnement

Dans le portail Azure, les journaux de provisionnement peuvent être utilisés pour faciliter le suivi et résoudre les problèmes de synchronisation d’objet.  Pour voir les journaux, sélectionnez **Journaux**.
 ![Journaux d’approvisionnement](media/how-to-troubleshoot/log1.png)

Les journaux de provisionnement fournissent une multitude d’informations sur l’état des objets synchronisés entre votre environnement Active Directory local et Azure.

 ![Journaux d’approvisionnement](media/how-to-troubleshoot/log2.png)

Vous pouvez utiliser les listes déroulantes en haut de la page pour filtrer la vue et ainsi mettre en évidence des dates particulières, des problèmes spécifiques, etc.  En double-cliquant sur un événement donné, vous obtenez des informations détaillées supplémentaires.

 ![Journaux d’approvisionnement](media/how-to-troubleshoot/log3.png)

Ces informations détaillent les étapes, et le moment auquel le problème de synchronisation se produit.  Vous pouvez ainsi vous concentrer sur le problème et localiser l’endroit exact où se situe le problème.


## <a name="provisioning-quarantined-issues"></a>Problèmes de provisionnement en quarantaine

Le provisionnement cloud supervise l’intégrité de votre configuration et place les applications qui ne sont pas saines en état de « quarantaine ». Si la plupart ou la totalité des appels effectués sur le système cible échouent systématiquement à cause d’une erreur, par exemple quand les informations d’identification ne sont pas valides, le travail de provisionnement est mis en quarantaine.

 ![Mise en quarantaine](media/how-to-troubleshoot/quarantine1.png)

En cliquant sur l’état, vous pouvez voir des informations supplémentaires sur la mise en quarantaine.  Vous pouvez également obtenir le code et le message d’erreur.

 ![Mise en quarantaine](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolving-a-quarantine"></a>Résolution d’une mise en quarantaine

- Utilisez le portail Azure pour redémarrer le travail de provisionnement. Dans la page de configuration de l’agent, sélectionnez **Redémarrer le provisionnement**.

  ![Mise en quarantaine](media/how-to-troubleshoot/quarantine3.png)

- Utilisez Microsoft Graph pour [redémarrer le travail de provisionnement](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Vous bénéficiez d’un contrôle total sur ce que vous redémarrez. Vous pouvez choisir d’effacer les entiercements (pour redémarrer le compteur d’entiercements qui augmente jusqu’à l’état de quarantaine), de supprimer la quarantaine (pour sortir l’application de quarantaine) ou d’effacer les filigranes. Utilisez la requête suivante :
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Présentation du provisionnement cloud Azure AD Connect](what-is-cloud-provisioning.md)



