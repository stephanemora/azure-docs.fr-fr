---
title: Protocole TLS dans Azure Site Recovery
description: Découvrez comment activer Azure Site Recovery pour utiliser le protocole de chiffrement TLS (Transport Layer Security) et sécuriser les données lors de leur transfert sur un réseau.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: 3937fd9f88a844c0257bc6cb66b4c3f97a112987
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602545"
---
# <a name="transport-layer-security-in-azure-site-recovery"></a>Protocole TLS dans Azure Site Recovery

Le protocole TLS (Transport Layer Security) est un protocole de chiffrement qui assure la sécurité des données lors de leur transfert sur un réseau. Azure Site Recovery utilise le protocole TLS pour protéger la confidentialité des données transférées. Azure Site Recovery utilise désormais le protocole TLS 1.2 pour améliorer la sécurité.

## <a name="enable-tls-on-older-versions-of-windows"></a>Activer le protocole TLS sur des versions antérieures de Windows

Si l’ordinateur exécute des versions antérieures de Windows, veillez à installer les mises à jour correspondantes comme indiqué ci-dessous et à effectuer les modifications de registre comme indiqué dans les articles respectifs de la base de connaissances.

|Système d’exploitation  |article de la Base de connaissances |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>La mise à jour installe les composants requis pour le protocole. Après l’installation, pour activer les protocoles requis, veillez à mettre à jour les clés de Registre comme indiqué dans les articles de la base de connaissances ci-dessus.

## <a name="verify-windows-registry"></a>Vérifier le Registre Windows

### <a name="configure-schannel-protocols"></a>Configurer des protocoles SChannel

Les clés de Registre suivantes garantissent que le protocole TLS 1.2 est activé au niveau du composant SChannel :

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>Par défaut, les clés de Registre ci-dessus sont définies dans les valeurs indiquées, qui sont définies dans Windows Server 2012 R2 et versions ultérieures. Pour ces versions de Windows, si les clés de Registre sont absentes, vous n’avez pas besoin de les créer.

### <a name="configure-net-framework"></a>Configurer .NET Framework

Utilisez les clés de Registre suivantes pour configurer .NET Framework qui prend en charge un chiffrement renforcé. Vous trouverez davantage d’informations sur la [configuration de .NET Framework ici](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry).

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="why-enable-tls-12"></a>Pourquoi activer TLS 1.2 ?

Le protocole TLS 1.2 est plus sécurisé que les protocoles de chiffrement précédents, tels que SSL 2.0, SSL 3.0, TLS 1.0 et TLS 1.1. Tous les services Azure Site Recovery prennent entièrement en charge TLS 1.2.

### <a name="what-determines-the-encryption-protocol-used"></a>Qu’est-ce qui détermine le protocole de chiffrement utilisé ?

La version de protocole la plus élevée qui est prise en charge par le client et le serveur est négociée pour établir la conversation chiffrée. Pour plus d’informations sur le protocole de négociation TLS, consultez [Établissement d’une session sécurisée à l’aide du protocole TLS](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls).

### <a name="what-is-the-impact-if-tls-12-is-not-enabled"></a>Quel est l’impact si TLS 1.2 n’est pas activé ?

Pour améliorer la sécurité contre les attaques par passage à une version antérieure de protocole, Azure Site Recovery commence par désactiver les versions de TLS antérieures à 1.2. Il s’agit d’un changement à long terme opéré entre les services pour interdire les connexions de suite de chiffrement et de protocole héritées. Les services et les composants d’Azure Site Recovery prennent en charge le protocole TLS 1.2 dans son intégralité. Toutefois, les versions de Windows non pourvues des mises à jour obligatoires ou de certaines configurations personnalisées peuvent toujours empêcher la mise à disposition des protocoles TLS 1.2. Des défaillances peuvent en résulter, parmi lesquelles une ou plusieurs des situations suivantes, mais pas seulement :

- La réplication peut échouer à la source.
- Échecs de connexion des composants d’Azure Site Recovery avec l’erreur 10054 (une connexion existante a été fermée de force par l’hôte distant).
- Les services liés à Azure Site Recovery ne s’arrêtent pas ou ne démarrent pas comme d’habitude.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Protocole TLS](/windows/win32/secauthn/transport-layer-security-protocol)
- [Assurer la prise en charge de TLS 1.2 sur les systèmes d’exploitation déployés](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [Meilleures pratiques Transport Layer Security (TLS) avec .NET Framework](/dotnet/framework/network-programming/tls)