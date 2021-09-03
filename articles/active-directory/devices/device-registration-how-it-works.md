---
title: Fonctionnement de l’inscription d’appareil Azure AD
description: Flux d’inscription d’appareil Azure AD pour les domaines gérés et fédérés
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bc24ecd387c011b5a8a36f380916ab815c1f0ab
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122534791"
---
# <a name="how-it-works-device-registration"></a>Fonctionnement : inscription de l’appareil

L’inscription de l’appareil est une condition préalable à l’authentification basée sur le cloud. En règle générale, les appareils sont joints à Azure AD ou Azure AD Hybride pour terminer l’inscription de l’appareil. Cet article fournit des détails sur la façon dont la jointure Azure AD et la jointure Azure AD Hybride fonctionnent dans des environnements gérés et fédérés. Pour plus d’informations sur le fonctionnement de l’authentification Azure AD sur ces appareils, consultez l’article [Jetons d’actualisation principaux](concept-primary-refresh-token.md#detailed-flows)

## <a name="azure-ad-joined-in-managed-environments"></a>Jointure Azure AD dans des environnements gérés

:::image type="content" source="media/device-registration-how-it-works/device-registration-azure-ad-managed.png" alt-text="Flux d’appareil joints Azure AD dans un environnement géré" lightbox="media/device-registration-how-it-works/device-registration-azure-ad-managed.png":::

| Phase | Description |
| :----: | :----------- |
| Un | La méthode la plus courante d’inscription des appareils joints Azure AD se trouve au cours de l’expérience OOBE (Out-Of-Box-Experience) où elle charge l’application web de jointure Azure AD dans l’application CXH (Cloud Experience Host). L’application envoie une requête GET au point de terminaison de configuration Azure AD OpenID pour découvrir les points de terminaison d’autorisation. Azure AD retourne la configuration OpenID, qui comprend les points de terminaison d’autorisation, à l’application en tant que document JSON. |
| B | L’application génère une requête de connexion pour le point de terminaison d’autorisation et collecte les informations d’identification de l’utilisateur. |
| C | Une fois que l’utilisateur a fourni son nom d’utilisateur (au format UPN), l’application envoie une requête GET à Azure AD pour découvrir les informations de domaine correspondantes pour l’utilisateur. Ces informations déterminent si l’environnement est géré ou fédéré. Azure AD retourne les informations d’un objet JSON. L’application détermine que l’environnement est géré (non fédéré).<br><br>La dernière étape de cette phase a pour effet que l’application crée une mémoire tampon d’authentification et, si dans OOBE, la met temporairement en cache pour la connexion automatique à la fin d’OOBE. L’application publie les informations d’identification dans Azure AD où elles sont validées. Azure AD retourne un jeton d’ID avec des revendications. |
| D | L’application recherche des conditions d’utilisation MDM (la revendication mdm_tou_url). S’il est présent, l’application récupère les conditions d’utilisation à partir de la valeur de la revendication, présente le contenu à l’utilisateur et attend que l’utilisateur accepte les conditions d’utilisation. Cette étape est facultative et ignorée si la revendication n’est pas présente ou si la valeur de la revendication est vide. |
| E | L’application envoie une requête de détection d’inscription d’appareil à ADRS (Azure Device Registration Service). Azure DRS retourne un document de données de découverte, qui retourne des URI spécifiques au locataire pour terminer l’inscription de l’appareil. |
| F | L’application crée une paire de clés de 2048 bits RSA (par défaut), connue sous le nom de clé d’appareil (dkpub/dkpriv). L’application crée une requête de certificat à l’aide de dkpub et de la clé publique et signe la requête de certificat en utilisant dkpriv. Ensuite, l’application dérive la deuxième paire de clés de la clé racine de stockage de TPM. Cette clé est la clé de transport (tkpub/tkpriv). |
| G | L’application envoie une requête d’inscription d’appareil à Azure DRS qui comprend le jeton d’ID, la requête de certificat, tkpub et les données d’attestation. Azure DRS valide le jeton d’ID, crée un ID d’appareil et crée un certificat basé sur la requête de certificat incluse. Azure DRS écrit ensuite un objet d’appareil dans Azure AD et envoie l’ID d’appareil et le certificat d’appareil au client. |
| H | L’inscription de l’appareil s’effectue en recevant l’ID de l’appareil et le certificat de l’appareil à partir d’Azure DRS. L’ID de l’appareil est enregistré pour référence ultérieure (visible à partir de `dsregcmd.exe /status`) et le certificat de l’appareil est installé dans le magasin personnel de l’ordinateur. Une fois l’inscription de l’appareil terminée, le processus se poursuit avec l’inscription MDM. |

## <a name="azure-ad-joined-in-federated-environments"></a>Jointure Azure AD dans des environnements fédérés

:::image type="content" source="media/device-registration-how-it-works/device-registration-azure-ad-federated.png" alt-text="Flux d’appareil joints Azure AD dans un environnement fédéré" lightbox="media/device-registration-how-it-works/device-registration-azure-ad-federated.png":::

| Phase | Description |
| :----: | :----------- |
| Un | La méthode la plus courante d’inscription des appareils joints Azure AD se trouve au cours de l’expérience OOBE (Out-Of-Box-Experience) où elle charge l’application web de jointure Azure AD dans l’application CXH (Cloud Experience Host). L’application envoie une requête GET au point de terminaison de configuration Azure AD OpenID pour découvrir les points de terminaison d’autorisation. Azure AD retourne la configuration OpenID, qui comprend les points de terminaison d’autorisation, à l’application en tant que document JSON. |
| B | L’application génère une requête de connexion pour le point de terminaison d’autorisation et collecte les informations d’identification de l’utilisateur. |
| C | Une fois que l’utilisateur a fourni son nom d’utilisateur (au format UPN), l’application envoie une requête GET à Azure AD pour découvrir les informations de domaine correspondantes pour l’utilisateur. Ces informations déterminent si l’environnement est géré ou fédéré. Azure AD retourne les informations d’un objet JSON. L’application détermine que l’environnement est fédéré.<br><br>L’application redirige vers la valeur AuthURL (page de connexion STS locale) dans l’objet de domaine JSON renvoyé. L’application collecte les informations d’identification via la page web STS. |
| D | L’application publie les informations d’identification sur le STS local, ce qui peut nécessiter des facteurs d’authentification supplémentaires. Le STS local authentifie l’utilisateur et retourne un jeton. L’application publie le jeton sur Azure AD pour l’authentification. Azure AD valide le jeton et retourne un jeton d’ID avec des revendications. |
| E | L’application recherche des conditions d’utilisation MDM (la revendication mdm_tou_url). S’il est présent, l’application récupère les conditions d’utilisation à partir de la valeur de la revendication, présente le contenu à l’utilisateur et attend que l’utilisateur accepte les conditions d’utilisation. Cette étape est facultative et ignorée si la revendication n’est pas présente ou si la valeur de la revendication est vide. |
| F | L’application envoie une requête de détection d’inscription d’appareil à ADRS (Azure Device Registration Service). Azure DRS retourne un document de données de découverte, qui retourne des URI spécifiques au locataire pour terminer l’inscription de l’appareil. |
| G | L’application crée une paire de clés de 2048 bits RSA (par défaut), connue sous le nom de clé d’appareil (dkpub/dkpriv). L’application crée une requête de certificat à l’aide de dkpub et de la clé publique et signe la requête de certificat en utilisant dkpriv. Ensuite, l’application dérive la deuxième paire de clés de la clé racine de stockage de TPM. Cette clé est la clé de transport (tkpub/tkpriv). |
| H | L’application envoie une requête d’inscription d’appareil à Azure DRS qui comprend le jeton d’ID, la requête de certificat, tkpub et les données d’attestation. Azure DRS valide le jeton d’ID, crée un ID d’appareil et crée un certificat basé sur la requête de certificat incluse. Azure DRS écrit ensuite un objet d’appareil dans Azure AD et envoie l’ID d’appareil et le certificat d’appareil au client. |
| I | L’inscription de l’appareil s’effectue en recevant l’ID de l’appareil et le certificat de l’appareil à partir d’Azure DRS. L’ID de l’appareil est enregistré pour référence ultérieure (visible à partir de `dsregcmd.exe /status`) et le certificat de l’appareil est installé dans le magasin personnel de l’ordinateur. Une fois l’inscription de l’appareil terminée, le processus se poursuit avec l’inscription MDM. |

## <a name="hybrid-azure-ad-joined-in-managed-environments"></a>Jointure Azure AD Hybride dans des environnements gérés

:::image type="content" source="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-managed.png" alt-text="Flux d’appareil joints Azure AD Hybride dans un environnement géré" lightbox="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-managed.png":::

| Phase | Description |
| :----: | ----------- |
| Un | L’utilisateur se connecte à un ordinateur Windows 10 joint au domaine à l’aide des informations d’identification de domaine. Ces informations d’identification peuvent être le nom d’utilisateur et le mot de passe ou l’authentification par carte à puce. L’utilisateur se connecte pour déclencher la tâche de jointure de l’appareil automatique. Les tâches de jointure de l’appareil automatique sont déclenchées lors de la jointure de domaine et retentées toutes les heures. Cela ne dépend pas uniquement de la connexion de l’utilisateur. |
| B | La tâche interroge Active Directory à l’aide du protocole LDAP pour l’attribut Keywords sur le point de connexion de service stocké dans la partition de configuration d’Active Directory (`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=corp,DC=contoso,DC=com`). La valeur retournée dans l’attribut Keywords détermine si l’inscription de l’appareil est dirigée vers ADRS (Azure Device Registration Service) ou le service d’inscription d’appareil d’entreprise hébergé localement. |
| C | Pour l’environnement géré, la tâche crée des informations d’identification d’authentification initiales sous la forme d’un certificat auto-signé. La tâche écrit le certificat dans l’attribut userCertificate de l’objet ordinateur dans Active Directory à l’aide de LDAP. |
| D | L’ordinateur ne peut pas s’authentifier auprès d’Azure DRS tant qu’un objet d’appareil représentant l’ordinateur qui comprend le certificat sur l’attribut userCertificate n’a pas été créé dans Azure AD. Azure AD Connect détecte une modification d’attribut. Lors du prochain cycle de synchronisation, Azure AD Connect envoie le userCertificate, le GUID de l’objet et le SID de l’ordinateur à Azure DRS. Azure DRS utilise les informations d’attribut pour créer un objet d’appareil dans Azure AD. |
| E | La tâche de jointure de l’appareil automatique est déclenchée chaque fois que l’utilisateur se connecte ou toutes les heures et tente d’authentifier l’ordinateur pour Azure AD à l’aide de la clé privée correspondante de la clé publique dans l’attribut userCertificate. Azure AD authentifie l’ordinateur et émet un jeton d’ID sur l’ordinateur. |
| F | La tâche crée une paire de clés de 2048 bits RSA (préférée) liée à TPM, connue sous le nom de clé d’appareil (dkpub/dkpriv). L’application crée une requête de certificat à l’aide de dkpub et de la clé publique et signe la requête de certificat en utilisant dkpriv. Ensuite, l’application dérive la deuxième paire de clés de la clé racine de stockage de TPM. Cette clé est la clé de transport (tkpub/tkpriv). |
| G | L’application envoie une requête d’inscription d’appareil à Azure DRS qui comprend le jeton d’ID, la requête de certificat, tkpub et les données d’attestation. Azure DRS valide le jeton d’ID, crée un ID d’appareil et crée un certificat basé sur la requête de certificat incluse. Azure DRS met ensuite à jour un objet d’appareil dans Azure AD et envoie l’ID d’appareil et le certificat d’appareil au client. |
| H | L’inscription de l’appareil s’effectue en recevant l’ID de l’appareil et le certificat de l’appareil à partir d’Azure DRS. L’ID de l’appareil est enregistré pour référence ultérieure (visible à partir de `dsregcmd.exe /status`) et le certificat de l’appareil est installé dans le magasin personnel de l’ordinateur. Une fois l’inscription de l’appareil terminée, la tâche se termine. |

## <a name="hybrid-azure-ad-joined-in-federated-environments"></a>Jointure Azure AD Hybride dans des environnements fédérés

:::image type="content" source="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-federated.png" alt-text="Flux d’appareil joints Azure AD Hybride dans un environnement géré" lightbox="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-federated.png":::

| Phase | Description |
| :----: | :----------- |
| Un | L’utilisateur se connecte à un ordinateur Windows 10 joint au domaine à l’aide des informations d’identification de domaine. Ces informations d’identification peuvent être le nom d’utilisateur et le mot de passe ou l’authentification par carte à puce. L’utilisateur se connecte pour déclencher la tâche de jointure de l’appareil automatique. Les tâches de jointure de l’appareil automatique sont déclenchées lors de la jointure de domaine et retentées toutes les heures. Cela ne dépend pas uniquement de la connexion de l’utilisateur. |
| B | La tâche interroge Active Directory à l’aide du protocole LDAP pour l’attribut Keywords sur le point de connexion de service stocké dans la partition de configuration d’Active Directory (`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=corp,DC=contoso,DC=com`). La valeur retournée dans l’attribut Keywords détermine si l’inscription de l’appareil est dirigée vers ADRS (Azure Device Registration Service) ou le service d’inscription d’appareil d’entreprise hébergé localement. |
| C | Pour les environnements fédérés, l’ordinateur authentifie le point de terminaison d’inscription des appareils d’entreprise à l’aide de l’authentification intégrée Windows. Le service d’inscription d’appareils d’entreprise crée et retourne un jeton qui comprend les revendications pour le GUID d’objet, le SID de l’ordinateur et l’état joint au domaine. La tâche envoie le jeton et les revendications à Azure AD où elles sont validées. Azure AD renvoie un jeton d’ID à la tâche en cours. |
| D | L’application crée une paire de clés de 2048 bits RSA (par défaut), connue sous le nom de clé d’appareil (dkpub/dkpriv). L’application crée une requête de certificat à l’aide de dkpub et de la clé publique et signe la requête de certificat en utilisant dkpriv. Ensuite, l’application dérive la deuxième paire de clés de la clé racine de stockage de TPM. Cette clé est la clé de transport (tkpub/tkpriv). |
| E | Pour fournir l’authentification unique pour une application fédérée locale, la tâche demande un PRT d’entreprise à partir du STS local. Windows Server 2016 exécutant le rôle de services de fédération Active Directory (AD FS), validez la requête et renvoyez-la à la tâche en cours d’exécution. |
| F | L’application envoie une requête d’inscription d’appareil à Azure DRS qui comprend le jeton d’ID, la requête de certificat, tkpub et les données d’attestation. Azure DRS valide le jeton d’ID, crée un ID d’appareil et crée un certificat basé sur la requête de certificat incluse. Azure DRS écrit ensuite un objet d’appareil dans Azure AD et envoie l’ID d’appareil et le certificat d’appareil au client. L’inscription de l’appareil s’effectue en recevant l’ID de l’appareil et le certificat de l’appareil à partir d’Azure DRS. L’ID de l’appareil est enregistré pour référence ultérieure (visible à partir de `dsregcmd.exe /status`) et le certificat de l’appareil est installé dans le magasin personnel de l’ordinateur. Une fois l’inscription de l’appareil terminée, la tâche se termine. |
| G | Si la réécriture d'appareil Azure AD Connect est activée, Azure AD Connect demande des mises à jour à partir d’Azure AD lors du prochain cycle de synchronisation (la réécriture d'appareil est requise pour le déploiement hybride à l’aide de l’approbation de certificat). Azure AD met en corrélation l’objet appareil avec un objet ordinateur synchronisé correspondant. Azure AD Connect reçoit l’objet d’appareil qui comprend le GUID d’objet et le SID de l’ordinateur, puis écrit l’objet d’appareil sur Active Directory. |

## <a name="next-steps"></a>Étapes suivantes

- [Appareils joints Azure AD](concept-azure-ad-join.md)
- [Appareils inscrits sur Azure AD](concept-azure-ad-register.md)
- [Appareils joints Azure AD hybrides](concept-azure-ad-join-hybrid.md)
- [Qu’est-ce qu’un jeton d’actualisation principal ?](concept-primary-refresh-token.md)
- [Azure AD Connect : Options de l’appareil](../hybrid/how-to-connect-device-options.md)
