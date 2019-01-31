---
title: Configurer le protocole LDAPS (LDAP sécurisé) dans les services de domaine Azure AD | Microsoft Docs
description: Configurer le protocole LDAPS (LDAP sécurisé) pour un domaine géré par les services de domaine Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: ergreenl
ms.openlocfilehash: 66c50d7ec9948b1f2447aadcef4a4a8f321204ef
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177388"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurer le protocole LDAPS (LDAP sécurisé) pour un domaine managé Azure AD Domain Services
Cet article explique comment activer le protocole LDAPS pour votre domaine géré par les services de domaine Azure Active Directory. Le protocole LDAP sécurisé est également appelé « protocole LDAP sur SSL (Secure Sockets Layer) / TLS (Transport Layer Security) ».

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Avant de commencer
Pour exécuter les tâches indiquées dans cet article, vous avez besoin des éléments suivants :

1. Un **abonnement Azure**valide.
2. Un **répertoire Azure AD** , synchronisé avec un répertoire local ou un répertoire cloud uniquement.
3. **services de domaine Azure AD** , qui doivent être activés pour le répertoire Azure AD. Si ce n’est déjà fait, suivez l’ensemble des tâches décrites dans le [Guide de mise en route](active-directory-ds-getting-started.md).
4. Un **certificat à utiliser pour activer le protocole LDAP sécurisé**.

   * **Recommandé** - Procurez-vous un certificat auprès de votre autorité de certification publique de confiance. Cette option de configuration est plus sûre.
   * Le cas échéant, vous pouvez également [créer un certificat auto-signé](#task-1---obtain-a-certificate-for-secure-ldap) comme indiqué plus loin dans cet article.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Configuration requise pour le certificat LDAP sécurisé
Obtenez un certificat valide, en suivant les instructions ci-dessous, avant d’activer le protocole LDAP sécurisé. Toute tentative d’activation du protocole LDAP sécurisé pour votre domaine géré avec un certificat non valide ou incorrect se soldera par un échec.

1. **Émetteur approuvé** : le certificat doit être émis par une autorité approuvée par les ordinateurs qui se connectent au domaine managé à l’aide du protocole LDAP sécurisé. Cette autorité peut être une autorité de certification publique ou une autorité de certification d’entreprise approuvée par ces ordinateurs.
2. **Durée de vie** : le certificat doit être valide pour les 3 à 6 mois à venir. L’accès du protocole LDAP sécurisé à votre domaine géré est interrompu lorsque le certificat expire.
3. **Nom du sujet** : le nom du sujet du certificat doit correspondre à votre domaine managé. Par exemple, si le nom du domaine est « contoso100.com », le nom d’objet du certificat doit correspondre à « contoso100.com ». Définissez le nom DNS (nom alternatif du sujet) sur un nom générique pour votre domaine managé.
4. **Utilisation de la clé** : le certificat doit être configuré pour le chiffrage de clés et les signatures numériques.
5. **Rôle du certificat** : le certificat doit être valide pour l’authentification de serveur SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Tâche 1 : Obtenir un certificat pour le protocole LDAP sécurisé
La première tâche consiste à obtenir un certificat à utiliser pour l’accès du protocole LDAP sécurisé au domaine géré. Deux options s'offrent à vous :

* Obtenir un certificat auprès d’une autorité de certification publique ou d’une autorité de certification d’entreprise.
* Vous pouvez créer un certificat auto-signé.

> [!NOTE]
> Les ordinateurs clients qui doivent se connecter au domaine géré via le protocole LDAP sécurisé doivent approuver l’émetteur du certificat LDAP sécurisé.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Option A (recommandée) : obtention d’un certificat LDAP sécurisé auprès d’une autorité de certification
Si votre organisation obtient ses certificats auprès d’une autorité de certification publique, obtenez le certificat LDAP sécurisé auprès de cette dernière. Si vous déployez une autorité de certification d’entreprise, obtenez le certificat LDAP sécurisé auprès de cette dernière.

> [!TIP]
> **Utilisez des certificats auto-signés pour les domaines managés présentant le suffixe de domaine « .onmicrosoft.com ».**
> Si le nom de domaine DNS de votre domaine managé se termine par « .onmicrosoft.com », vous ne pouvez pas obtenir de certificat LDAP auprès d’une autorité de certification publique. Étant donné que le domaine « onmicrosoft.com » est la propriété de Microsoft, les autorités de certification publique refusent d’émettre des certificats LDAP sécurisés pour les domaines présentant ce suffixe. Dans ce cas de figure, créez un certificat auto-signé et utilisez-le pour configurer le protocole LDAP sécurisé.
>

Vérifiez que le certificat que vous obtenez auprès de l’autorité de certification publique remplit tous les critères décrits dans [Configuration requise pour le certificat LDAP sécurisé](#requirements-for-the-secure-ldap-certificate).


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Option B : création d’un certificat auto-signé pour le protocole LDAP sécurisé
Si vous ne prévoyez pas d’utiliser un certificat d’une autorité de certification publique, vous pouvez choisir de créer un certificat auto-signé pour le protocole LDAP sécurisé. Choisissez cette option si le nom de domaine DNS de votre domaine managé se termine par « .onmicrosoft.com ».

**Créer un certificat auto-signé à l’aide de PowerShell**

Sur votre ordinateur Windows, ouvrez une nouvelle fenêtre PowerShell en tant **qu’administrateur** et saisissez les commandes suivantes, afin de créer un certificat auto-signé.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com, contoso100.com
```

Dans l’exemple ci-dessus, remplacez « contoso100.com » par le nom de domaine DNS de votre domaine managé. Par exemple, si vous avez créé un domaine managé nommé « contoso100.onmicrosoft.com », remplacez « .contoso100.com » dans l’attribut Subject par « .contoso100.onmicrosoft.com » et «  *.contoso100.com » dans l’attribut DnsName par «*  .contoso100.onmicrosoft.com ».

![Sélectionner un annuaire Azure AD](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Le nouveau certificat auto-signé est placé dans le magasin de certificats de l’ordinateur local.


## <a name="next-step"></a>Étape suivante
[Tâche 2 : Exporter le certificat du protocole LDAP sécurisé vers un fichier .PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
