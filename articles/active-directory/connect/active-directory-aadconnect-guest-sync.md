---
title: Synchronisation des comptes d’utilisateur des utilisateurs invités qui utilisent une adresse e-mail pour se connecter à Azure | Microsoft Docs
description: Cet article explique comment synchroniser les comptes d’utilisateur invité qui utilisent un autre ID pour se connecter aux applications.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2018
ms.author: billmath
ms.openlocfilehash: f450747cac06a416950efc6c37c1058c1cb40775
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="synchronizing-guest-user-accounts-that-use-email-for-sign-in-preview"></a>Synchronisation des comptes d’utilisateur des utilisateurs invités qui utilisent une adresse e-mail pour se connecter (préversion)

>[!NOTE]
> Cette fonctionnalité est actuellement disponible en préversion publique.

Le scénario suivant présente une situation dans laquelle des utilisateurs externes de votre environnement AD local, par exemple des partenaires, utilisent une autre méthode de connexion.

Dans l’exemple précédent, Nina Morin travaille pour Fabrikam et son adresse e-mail est la suivante : nmorin@fabrikam.com. Nina est partenaire de Contoso et elle a besoin d’accéder à certaines applications de Contoso. Contoso a créé un compte pour Nina et a demandé à Nina d’utiliser son adresse e-mail pour se connecter aux applications.

Pour leurs applications locales, ce scénario a très bien fonctionné. Mais maintenant, Contoso est en train de migrer ces applications vers le cloud et souhaite que ses partenaires aient la même expérience.

Ce scénario répond à cette situation.


## <a name="pre-requisites-and-assumptions"></a>Conditions préalables et hypothèses
Cette section contient une liste des conditions préalables et des hypothèses que vous devez connaître avant d’essayer de mettre en place ce scénario.

### <a name="pre-requisites"></a>Conditions préalables
- Informations d’identification d’administrateur général pour configurer Azure AD Connect, vérifier les domaines et configurer les paramètres de fédération de domaine
- Azure AD Connect 1.1.524.0 ou une version ultérieure
- Domaine vérifié pour définir l’UPN du cloud des utilisateurs externes (exemple : bmcontoso.com).
- Service FS (Federation Service) pour authentifier les utilisateurs externes. Si vous utilisez AD FS, il doit s’agir de la version 2012 R2 ou ultérieure
- MSOL PowerShell v1.1 est installé sur un ordinateur afin de contrôler les paramètres de fédération. Pour plus d’informations, consultez [Azure ActiveDirectory (MSOnline)](https://docs.microsoft.com/en-us/powershell/azure/active-directory/overview?view=azureadps-1.0).


### <a name="assumptions"></a>Hypothèses 
- Azure AD Connect a déjà été configuré et installé. Pour plus d’informations sur l’installation d’Azure AD Connect, consultez [Fédération avec Azure AD Connect](active-directory-aadconnectfed-whatis.md).
Ce document repose sur les hypothèses suivantes :
- vous avez configuré un service FS qui authentifie correctement les utilisateurs.
- les utilisateurs externes peuvent s’authentifier à l’aide de leur adresse e-mail externe.
- - l’utilisation d’un autre ID de connexion a été paramétrée et configurée. Les utilisateurs peuvent s’authentifier à l’aide de leur autre ID. Pour plus d’informations sur la configuration d’un autre ID avec AD FS, consultez [La configuration des ID de connexion alternatif](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

## <a name="task-1--prepare-the-environment"></a>Tâche 1 : Préparer l’environnement
La tâche suivante consiste à rassembler des informations pour que vous soyez prêt à commencer la synchronisation de vos comptes externes afin qu’ils puissent se connecter à l’aide d’un autre ID, comme l’attribut de messagerie.

Avant de passer à la seconde tâche, définissez les éléments du tableau ci-dessous.

![Architecture](media/active-directory-aadconnect-guest-sync/guest2.png)

|Aspect de l’environnement|À quoi sert-il ?|Mise en œuvre dans votre environnement|
|-----|-----|-----|
|Attribut UPN du cloud|L’attribut qui renseigne l’UPN des objets utilisateur externe dans le cloud. Le suffixe UPN des comptes externes doit être celui défini dans les conditions préalables. Il s’agit du domaine vérifié.|* Exemple : UserPrincipalName (nmorin@bmcontoso.com)|
|Adresse de connexion|L’attribut saisi par les utilisateurs externes lors de la connexion. Cet attribut doit avoir un format d’adresse e-mail et, dans la plupart des cas, il coïncide avec l’adresse e-mail réelle de l’utilisateur externe.|* Exemple : mail (nmorin@fabrikam.com)|
|Azure AD Connect avec filtre étendu|Le filtre qui permet de cibler les identités externes pour étendre les règles de synchronisation définies plus loin dans ce guide. Les méthodes classiques d’application d’une étendue sont : une unité d’organisation prédéfinie dans l’organisation, une certaine convention d’affectation de noms, un domaine spécifique, etc.|* Exemple : l’unité d’organisation contient Externes|
|Client Azure AD|Le nom du locataire Azure AD tel qu’il apparaît dans Azure AD Connect.|Exemple : contoso.onmicrosoft.com|

L’écran suivant comporte trois cadres rouges.
- L’unité d’organisation **Externals**, qui est utilisée dans Azure AD Connect avec filtre étendu et qui est l’emplacement des utilisateurs externes.
- L’attribut **mail**, qui est utilisé par les utilisateurs externes pour se connecter.
- L’attribut **userPrincipalName**, qui est le domaine vérifié avec lequel l’environnement local est fédéré.

![Utilisateur](media/active-directory-aadconnect-guest-sync/guest1.png)

## <a name="task-2--configure-azure-ad-connect"></a>Tâce 2 : Configurer Azure AD Connect
Une fois les informations ci-dessus définies, nous pouvons passer à la définition des règles de synchronisation Azure AD Connect. Pour configurer les règles, utilisez l’éditeur de règles de synchronisation Azure AD Connect. Pour plus d’informations sur l’éditeur, consultez [Approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

### <a name="how-to-configure-the-synchronization-rule"></a>Configurer la règle de synchronisation
Utilisez la procédure suivante pour configurer Azure AD Connect.

1. Ouvrez l’éditeur de règles de synchronisation Azure AD Connect en accédant à **Démarrer - Azure AD Connect - Éditeur des règles de synchronisation**.
2. Dans l’écran **Éditeur de règles de synchronisation**, vérifiez que la direction est **entrante** et cliquez sur **Ajouter une nouvelle règle** à droite.
3. Dans la page **Description**, configurez ce qui suit et cliquez sur **Suivant**.
    - **Nom** : entrez le nom de la règle 
    - **Système connecté :** l’environnement AD local
    - **Type d’objet système connecté :** - user
    - **Type d’objet métaverse :** - person
    - **Type de lien :** - Join
    - **Précédence :** - 90
    - 
![](media/active-directory-aadconnect-guest-sync/guest3.png)

4. Dans l’écran **Filtre d’étendue**, cliquez sur **Ajouter un groupe**.
5. Utilisez les listes déroulantes pour configurer le filtre. Entrez les informations suivantes et cliquez sur **Suivant**. Cette action crée un filtre qui s’applique uniquement aux objets situés dans l’unité d’organisation externe.
    - **Attribut** - dn
    - **Opérateur** -CONTIENT
    - **Valeur** - Externals
 
 ![Filtrer](media/active-directory-aadconnect-guest-sync/guest4.png)

6. Dans l’écran **Règles de jointure**, cliquez sur **Suivant**.
7. Dans l’écran **Transformations**, cliquez sur **Ajouter une transformation**. Entrez les informations suivantes :
    - **FlowType** - Constant
    - **Attribut cible** - userType
    - **Source** - Guest
8. Dans l’écran **Transformations**, cliquez sur **Ajouter une transformation**. Entrez les informations suivantes :
    - **FlowType** - Direct
    - **Attribut cible** - onPremisesUserPrincipalName
    - **Source** - mail
9. Dans l’écran **Transformations**, cliquez sur **Ajouter une transformation**. Entrez les informations suivantes :
    - **FlowType** - Direct
    - **Attribut cible** - userPrincipalName
    - **Source** -userPrincipalName ![Transformation](media/active-directory-aadconnect-guest-sync/guest5.png)
10. Cliquez sur **Add**. 
11. Dans l’écran **Éditeur de règles de synchronisation**, vérifiez que la direction est **sortante** et cliquez sur **Ajouter une nouvelle règle** à droite.
12. Dans la page **Description**, configurez ce qui suit et cliquez sur **Suivant**.
    - **Nom** : entrez le nom de la règle 
    - **Système connecté :** - le client Azure AD
    - **Type d’objet système connecté :** - user
    - **Type d’objet métaverse :** - person
    - **Type de lien :** - Join
    - **Précédence :** - 90
    - 
![Règle](media/active-directory-aadconnect-guest-sync/guest6.png)

13. Dans l’écran **Filtre d’étendue**, cliquez sur **Suivant**.
14. Dans l’écran **Règles de jointure**, cliquez sur **Suivant**.
15. Dans l’écran **Transformations**, cliquez sur **Ajouter une transformation**.  Entrez les informations suivantes :
    - **FlowType** - Direct
    - **Attribut cible** - userType
    - **Source** - userType
9. Dans l’écran **Transformations**, cliquez sur **Ajouter une transformation**. Entrez les informations suivantes :
    - **FlowType** - Direct
    - **Attribut cible** - onPremisesUserPrincipalName
    - **Source** - onPremisesUserPrincipalName ![Transformation](media/active-directory-aadconnect-guest-sync/guest7.png)
10. Cliquez sur **Add**. 
11. Une fois ces règles configurées, vous devez exécuter une synchronisation complète. Utilisez PowerShell pour démarrer une synchronisation complète. Une fois la synchronisation terminée, vous pouvez passer à l’étape suivante.

``` powershell
    Start-ADSyncSyncCycle -PolicyType Initial
```

## <a name="task-3--federation"></a>Tâche 3 : Fédération
La tâche suivante consiste à rassembler des informations sur quelques éléments que vous devez mettre en place pour que le scénario fonctionne.

Vous pouvez vérifier vos paramètres de fédération avec Azure à l’aide d’Azure AD PowerShell. Ce document utilise la version 1.1 de MSOL PowerShell. Vous pouvez installer cette version [ici](https://docs.microsoft.com/en-us/powershell/azure/active-directory/overview?view=azureadps-1.0).

### <a name="verify-the-federation-settings"></a>Vérification des paramètres de fédération
Utilisez la procédure suivante pour vérifier les paramètres de fédération.
1. Ouvrez Windows PowerShell et connectez-vous à Azure AD à l’aide de la commande suivante :
``` powershell
      Connect-MSOLservice
```
2.  Entrez les informations d’identification de l’administrateur général.
3.  Ensuite, entrez la commande suivante :
  ``` powershell
      Get-MSOLDomainFederationSettings
  ```
4.   Notez que les informations de fédération doivent être renvoyées. Remarque : **ActiveLogonUri** est l’URL du serveur de fédération.

  ![Fédération](media/active-directory-aadconnect-guest-sync/guest8.png)

### <a name="verify-alternate-login-id"></a>Vérification de l’ID de connexion alternatif
Ce document utilise AD FS comme fournisseur d’identité (Idp). Si vous utilisez un autre Idp, ces étapes peuvent varier.

1. Ouvrez Windows PowerShell et saisissez la commande suivante :
   ```powershell
    Get-ADFSClaimsProviderTrust
   ```
2. Vous devriez voir les informations AD FS.  Notez les valeurs **AlternateLoginID** et **LookupForests**.

![ADFS](media/active-directory-aadconnect-guest-sync/guest9.png)

## <a name="task-4--testing"></a>Tâche 4 : Test
Pour vérifier que tout fonctionne correctement, vous devez vous connecter à un point de terminaison configuré pour s’authentifier à l’aide du Idp. Pour ce test, nous avons déployé un site Web dans Azure et nous utilisons l’url suivante : contososampapp.azurewebsites.net

### <a name="verify-that-you-can-sign-in-with-the-alternate-id"></a>Vérification de la connexion avec l’ID alternatif
1. Connectez-vous au point de terminaison.</br>
![Point de terminaison](media/active-directory-aadconnect-guest-sync/guest10.png)
1. Entrez votre nom d’utilisateur pour être redirigé vers la page de connexion du service de fédération.
![Fédération](media/active-directory-aadconnect-guest-sync/guest11.png)
1. Entrez vos informations d’identification.
2. Vous devez maintenant être connecté.
![Success](media/active-directory-aadconnect-guest-sync/guest12.png)

## <a name="next-steps"></a>Étapes suivantes
- [Propriétés d’un utilisateur Azure Active Directory B2B Collaboration](../../active-directory/active-directory-b2b-user-properties.md#key-properties-of-the-azure-ad-b2b-collaboration-user)
- [Configuration d’un ID de connexion alternatif](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
- [Azure AD Connect : Historique de publication des versions](active-directory-aadconnect-version-history.md)
