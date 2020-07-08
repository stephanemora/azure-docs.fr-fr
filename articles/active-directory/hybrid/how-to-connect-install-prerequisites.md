---
title: 'Azure AD Connect : Conditions préalables et matériel | Microsoft Docs'
description: Cette rubrique décrit les conditions préalables et la configuration matérielle requise pour Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bcf7b5b8791b813a28133d8a662d1736aacf35a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85358716"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Conditions préalables pour Azure AD Connect
Cette rubrique décrit les conditions préalables et la configuration matérielle requise pour Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Avant d’installer Azure AD Connect
Avant d’installer Azure AD Connect, voici ce dont vous avez besoin.

### <a name="azure-ad"></a>Azure AD
* Un locataire Azure AD. Vous pouvez en obtenir un avec un [essai gratuit Azure](https://azure.microsoft.com/pricing/free-trial/). Vous pouvez utiliser un des portails suivants pour gérer Azure AD Connect :
  * Le [portail Azure](https://portal.azure.com).
  * Le [portail Office](https://portal.office.com).  
* [Ajoutez et vérifiez le domaine](../active-directory-domains-add-azure-portal.md) que vous prévoyez d’utiliser dans Azure AD. Par exemple, si vous envisagez d’utiliser contoso.com pour vos utilisateurs, vérifiez que ce domaine a été vérifié et que vous n’utilisez pas uniquement le domaine par défaut contoso.onmicrosoft.com.
* Un client Azure AD prend en charge 50 000 objets par défaut. Si vous vérifiez votre domaine, la limite passe à 300 000 objets. Si vous avez besoin de davantage d’objets dans Azure AD, vous devez ouvrir une demande de support pour que la limite soit relevée en conséquence. Si vous avez besoin de plus de 500 000 objets, si vous avez besoin d’une licence comme Office 365, Azure AD Standard, Azure AD Premium ou Enterprise Mobility Suite.

### <a name="prepare-your-on-premises-data"></a>Préparez vos données locales
* Utilisez [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) pour identifier les erreurs comme les problèmes de mise en forme dans votre annuaire et repérer les doublons avant d’effectuer la synchronisation avec Azure AD et Office 365.
* Vérifiez [les fonctionnalités de synchronisation facultatives que vous pouvez activer dans Azure AD](how-to-connect-syncservice-features.md) et évaluez quelles fonctionnalités vous devez activer.

### <a name="on-premises-active-directory"></a>Active Directory local
* La version de schéma Active Directory et le niveau fonctionnel de forêt doivent être Windows Server 2003 ou version ultérieure. Les contrôleurs de domaine peuvent exécuter n’importe quelle version aussi longtemps que les exigences relatives au schéma et le niveau de forêt sont remplies.
* Si vous prévoyez d’utiliser la fonctionnalité **Réécriture du mot de passe**, les contrôleurs de domaine doivent être exécutés sous Windows Server 2008 ou une version ultérieure.
* Le contrôleur de domaine utilisé par Azure AD doit être accessible en écriture. L’utilisation d’un contrôleur de domaine en lecture seule (RODC) **n’est pas prise en charge** et Azure AD Connect ne suivra pas les redirections d’écriture.
* L’utilisation de forêts/domaines locaux utilisant des noms NetBios avec point (le nom contient un point ’.’) n’est **pas prise en charge**.
* Il est recommandé d’[activer la Corbeille Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Serveur Azure AD Connect
>[!IMPORTANT]
>Le serveur Azure AD Connect contient des données d’identité critique et doit être traité comme un composant de niveau 0 comme expliqué dans [le modèle de niveau administratif Active Directory](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).

* Vous ne pouvez pas installer Azure AD Connect sur Small Business Server ou Windows Server Essentials version antérieure à 2019 (Windows Server Essentials 2019 est pris en charge). Le serveur doit utiliser Windows Server Standard ou une version supérieure.
* L’installation d’Azure AD Connect sur un contrôleur de domaine n’est pas recommandée en raison des pratiques de sécurité et de paramètres plus restrictifs pouvant empêchant Azure AD Connect de s’installer correctement.
* Le serveur Azure AD Connect doit disposer d’une interface utilisateur graphique complète. Il ne **peut pas être installé** sur Server Core.
>[!IMPORTANT]
>L’installation d’Azure AD Connect sur un petit serveur Business, Essentials ou Core n’est pas prise en charge.

* Azure Active Directory Connect doit être installé sur Windows Server 2012 ou version ultérieure. Ce serveur doit être joint à un domaine et peut être un contrôleur de domaine ou un serveur membre.
* Le serveur Azure AD Connect ne doit pas avoir de stratégie de groupe de transcription PowerShell activée si vous utilisez l’Assistant Azure AD Connect pour gérer la configuration ADFS. Vous pouvez activer la transcription PowerShell si vous utilisez l’Assistant Azure AD Connect pour gérer la configuration de la synchronisation.
* Si les services de fédération Active Directory sont déployés, les serveurs sur lesquels ces services ou le proxy d’application web sont installés doivent être des serveurs Windows Server 2012 R2 ou version ultérieure. [gestion à distance de Windows](#windows-remote-management) doit être activée sur ces serveurs pour l’installation à distance.
* Si Active Directory Federation Services est déployé, vous avez besoin de [certificats TLS/SSL](#tlsssl-certificate-requirements).
* Si des services ADFS sont déployés, vous devrez configurer une [résolution de noms](#name-resolution-for-federation-servers).
* Si l’authentification MFA est activée pour vos administrateurs généraux, **https://secure.aadcdn.microsoftonline-p.com** doit figurer dans la liste des sites de confiance. Vous êtes invité à ajouter ce site à la liste des sites de confiance lorsque vous êtes invité à passer un test d’authentification MFA et qu’il n’a pas ajouté. Vous pouvez utiliser Internet Explorer pour l’ajouter à vos sites de confiance.
* Microsoft vous recommande de renforcer votre serveur Azure AD Connect afin de réduire la surface d’attaque de sécurité de ce composant essentiel de votre environnement informatique.  L’application des recommandations ci-après vous permettra de limiter les risques pour la sécurité dans votre organisation.

* Déployez Azure AD Connect sur un serveur joint au domaine et restreignez l’accès administratif aux administrateurs de domaine ou à d’autres groupes de sécurité étroitement contrôlés.

Pour plus d'informations, consultez les rubriques suivantes : 

* [Securing administrators groups](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory) (Sécurisation des groupes d’administrateurs)

* [Securing built-in administrator accounts](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory) (Sécurisation des comptes Administrateur intégrés)

* [Security improvement and sustainment by reducing attack surfaces](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces ) (Amélioration et maintien en état de la sécurité par la réduction des surfaces d’attaque)

* [Reducing the Active Directory attack surface](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) (Réduction de la surface d’attaque Active Directory)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server utilisé par Azure AD Connect
* Azure AD Connect nécessite une base de données SQL Server pour stocker les données d’identité. Par défaut, une base de données SQL Server 2012 Express LocalDB (version légère de SQL Server Express) est installée. SQL Server Express a une limite de 10 Go qui vous permet de gérer environ 100 000 objets. Si vous avez besoin de gérer un volume plus important d’objets d’annuaire, vous devez pointer l’assistant d’installation vers une autre installation de SQL Server. Le type d’installation de SQL Server peut impacter les [performances d’Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Si vous utilisez une installation différente de SQL Server, ces conditions s’appliquent :
  * Azure AD Connect prend en charge toutes les versions de Microsoft SQL Server à partir de 2012 (avec le dernier Service Pack) et jusqu’à SQL Server 2019. Microsoft Azure SQL Database n’est **pas pris en charge** comme base de données.
  * Vous devez utiliser un classement SQL qui ne respecte pas la casse. Ces classements sont identifiés par un \_CI_ dans leur nom. L’utilisation d’un classement qui respecte la casse, identifié par \_CS_ dans le nom, **n’est pas prise en charge**.
  * Vous ne pouvez avoir qu’un seul moteur de synchronisation par instance SQL. Le partage de l’instance SQL avec FIM/MIM Sync, DirSync ou Azure AD Sync n’est **pas pris en charge**.

### <a name="accounts"></a>Comptes
* Le compte d’administrateur global Azure AD du locataire Azure AD que vous souhaitez intégrer. Ce compte doit être un **compte scolaire ou d’organisation** et non d’un **compte Microsoft**.
* Si vous utilisez la [configuration rapide](reference-connect-accounts-permissions.md#express-settings-installation) ou effectuez une mise à niveau depuis DirSync, vous devez disposer d’un compte d’administrateur d’entreprise pour votre annuaire Active Directory local.
* Si vous utilisez le chemin d’installation des paramètres personnalisés, vous avez plus d’options. Pour plus d’informations, consultez [Paramètres d’installation personnalisée](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Connectivité
* Le serveur Azure AD Connect nécessite une résolution DNS Intranet et Internet. Le serveur DNS doit pouvoir résoudre des noms sur votre domaine Active Directory local et sur les points de terminaison Azure AD.
* Si vous disposez de pare-feu sur votre Intranet et que vous devez ouvrir des ports entre les serveurs Azure AD Connect et vos contrôleurs de domaine, consultez l’article [Ports Azure AD Connect](reference-connect-ports.md) pour en savoir plus.
* Si votre proxy ou pare-feu limite les URL accessibles, les URL répertoriées dans [URL et plages d’adresses IP Office 365 ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) doivent être ouvertes.
  * Si vous utilisez Microsoft Cloud en Allemagne ou le cloud Microsoft Azure Government, consultez [Considérations sur les instances de service Sync Azure AD Connect](reference-connect-instances.md) relatives aux URL.
* Azure AD Connect (version 1.1.614.0 et ultérieures) utilise TLS 1.2 par défaut pour le chiffrement de la communication entre le moteur de synchronisation et Azure AD. Si TLS 1.2 n’est pas disponible sur le système d’exploitation sous-jacent, Azure AD Connect revient de façon incrémentielle aux protocoles plus anciens (TLS 1.1 et TLS 1.0).
* Avant la version 1.1.614.0, Azure AD Connect utilise TLS 1.0 par défaut pour le chiffrement de la communication entre le moteur de synchronisation et Azure AD. Pour utiliser TLS 1.2, suivez les étapes de l’article [Activer TLS 1.2 pour Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Si vous utilisez un proxy sortant pour vous connecter à Internet, le paramètre suivant dans le fichier **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** doit être ajouté pour que l’Assistant Installation et Azure AD Connect Sync puissent se connecter à Internet et à Azure AD. Ce texte doit être entré en bas du fichier. Dans ce code, &lt;PROXYADDRESS&gt; représente l’adresse IP réelle ou le nom d’hôte du proxy.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Si votre serveur proxy nécessite une authentification, le [compte de service](reference-connect-accounts-permissions.md#adsync-service-account) doit se trouver dans le domaine, et vous devez utiliser le chemin d’installation des paramètres personnalisés pour spécifier un [compte de service personnalisé](how-to-connect-install-custom.md#install-required-components). Vous devez également modifier le fichier machine.config. Cette modification dans le fichier machine.config permet à l’Assistant d’installation et au moteur de synchronisation de répondre aux demandes d’authentification du serveur proxy. Dans toutes les pages de l'Assistant d’installation, à l'exclusion de la page **Configurer**, les informations d'identification de l’utilisateur sont utilisées. Dans la page **Configurer** à la fin de l’Assistant d’installation, le contexte bascule vers le [compte de service](reference-connect-accounts-permissions.md#adsync-service-account) que vous avez créé. La section machine.config doit ressembler à ceci.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Lorsque Azure Connect AD envoie une requête web à Azure AD dans le cadre de la synchronisation d’annuaires, Azure AD peut mettre jusqu'à 5 minutes pour répondre. Il est courant qu’un délai d’inactivité de la connexion soit configuré sur les serveurs proxy. Vérifiez que la configuration est définie sur au moins 6 minutes, voire plus.

Consultez MSDN pour plus d’informations sur [l’élément proxy par défaut](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Pour plus d’informations lorsque vous avez des problèmes de connectivité, consultez [Résoudre les problèmes de connectivité liés à Azure AD Connect](tshoot-connect-connectivity.md).

### <a name="other"></a>Autres
* Facultatif : Un compte d’utilisateur test pour vérifier la synchronisation.

## <a name="component-prerequisites"></a>Configuration requise pour les composants
### <a name="powershell-and-net-framework"></a>PowerShell et .NET Framework
Azure AD Connect repose sur Microsoft PowerShell et le .NET Framework 4.5.1. Cette version ou une version ultérieure doit être installée sur votre serveur. Selon votre version de Windows Server, procédez comme suit :

* Windows Server 2012 R2
  * Microsoft PowerShell est installé par défaut. Aucune action n'est requise.
  * Les versions .NET Framework 4.5.1 et ultérieures sont offertes par le biais de Windows Update. Assurez-vous que vous avez installé les dernières mises à jour de Windows Server dans le Panneau de configuration.
* Windows Server 2012
  * La dernière version de Microsoft PowerShell est disponible dans **Windows Management Framework 4.0**, dans le [Centre de téléchargement Microsoft](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 et les versions ultérieures sont disponibles dans le [Centre de téléchargement Microsoft](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Activer TLS 1.2 pour Azure AD Connect
Avant la version 1.1.614.0, Azure AD Connect utilise TLS 1.0 par défaut pour le chiffrement de la communication entre le serveur de moteur de synchronisation et Azure AD. Vous pouvez modifier ce comportement en configurant les applications .NET pour qu’elles utilisent TLS 1.2 par défaut sur le serveur. Vous trouverez plus d’informations sur TLS 1.2 dans [l’Avis de sécurité Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358).

1.  Vérifiez que le correctif .NET 4.5.1 est installé pour votre système d’exploitation. Consultez [l’Avis de sécurité Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358). Il est possible que cette version du correctif ou une version ultérieure soit déjà installée sur votre serveur.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319 "SchUseStrongCrypto"=dword:00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PowerShell command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PowerShell connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PowerShell session should open to ensure remote PowerShell sessions can be established.

### TLS/SSL Certificate Requirements
* It's strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).
