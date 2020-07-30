---
title: Résoudre les problèmes de vérification de l'éditeur - Plateforme d'identités Microsoft | Azure
description: Explique comment résoudre les problèmes de vérification de l'éditeur (préversion) pour la Plateforme d'identités Microsoft en appelant les API Microsoft Graph.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 3069e3caf81d9bb2f809b21c88383c419e3b90b3
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282974"
---
# <a name="troubleshoot-publisher-verification-preview"></a>Résoudre les problèmes de vérification de l'éditeur (préversion)
Si vous ne parvenez pas à finaliser le processus ou si vous êtes confronté à un comportement inattendu lors de la [vérification de l'éditeur (préversion) ](publisher-verification-overview.md), effectuez ce qui suit dès la réception des messages d'erreur ou le constat du comportement inattendu : 

1. Examinez les [exigences](publisher-verification-overview.md#requirements) et assurez-vous qu'elles sont toutes remplies.

1. Consultez les instructions relatives au [marquage d'une application avec la mention « éditeur vérifié »](mark-app-as-publisher-verified.md) et assurez-vous que toutes les étapes ont été exécutées avec succès.

1. Consultez la liste des [problèmes courants](#common-issues).

1. Reproduisez la requête à l'aide de l'[Afficheur Graph](#making-microsoft-graph-api-calls) pour recueillir des informations supplémentaires et éliminer tout problème lié à l'interface utilisateur.

## <a name="common-issues"></a>Problèmes courants
Vous trouverez ci-dessous quelques problèmes courants qui peuvent survenir au cours du processus. 

- **Je ne connais pas mon identifiant Microsoft Partner Network (ID MPN) ou j'ignore qui est le contact principal du compte**. 
    1. Accédez à la [page d'inscription MPN](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new).
    1. Connectez-vous à l'aide d'un compte d'utilisateur du locataire Azure AD principal de l'organisation. 
    1. Si un compte MPN existe déjà, il sera reconnu et vous y serez ajouté. 
    1. Accédez à la [page Profil de partenaire](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) où l'ID MPN et le contact principal du compte sont répertoriés.

- **Je ne sais pas qui est mon administrateur général Azure AD (également appelé administrateur de l'entreprise ou administrateur du locataire). Comment puis-je les trouver ? Qu'en est-il de l'administrateur de l'application, ou d'un autre rôle d'administrateur ?**
    1. Connectez-vous au [portail Azure AD](https://aad.portal.azure.com) à l'aide d'un compte d'utilisateur du locataire principal de votre organisation.
    1. Accédez à [Gestion des rôles](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators).
    1. Cliquez sur « Administrateur général », ou sur le rôle d'administrateur souhaité.
    1. La liste des utilisateurs dotés de ce rôle s'affiche.

- **Je ne sais pas qui sont les administrateurs de mon compte MPN**. Accédez à la [page Gestion des utilisateurs MPN](https://partner.microsoft.com/pcv/users) et filtrez la liste des utilisateurs pour savoir qui est doté de tel ou tel rôle d'administrateur.

- **Je reçois un message d'erreur indiquant que mon ID MPN n'est pas valide ou que je n'y ai pas accès.**
    1. Accédez à votre [profil de partenaire](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) et vérifiez que : 
        - L'ID MPN est correct. 
        - Aucune erreur ou « action en attente » n'est affichée. Assurez-vous également que l'état de vérification présenté sous Profil juridique de l'entreprise et Infos partenaire indique « autorisé » ou « réussite ».
    1. Accédez à la [page de gestion des locataires MPN](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement), puis confirmez que le locataire auprès duquel l'application est inscrite et à partir duquel vous vous connectez avec un compte d'utilisateur figure sur la liste des locataires associés.
    1. Accédez à la [page Gestion des utilisateurs MPN](https://partner.microsoft.com/pcv/users) et confirmez que l'utilisateur avec lequel vous vous connectez est administrateur général, administrateur MPN ou administrateur de comptes.

- **Lorsque je me connecte au portail Azure AD, je ne vois aucune application inscrite. Pourquoi ?** 
    Les inscriptions d'applications ont peut-être été créées via un autre compte d'utilisateur, ou dans un autre locataire. Vérifiez que vous êtes connecté avec le compte approprié dans le locataire où les inscriptions d'applications ont été créées.

- **Comment savoir qui est le propriétaire d'une inscription d'application dans Azure AD ?** 
    Une fois connecté à un locataire auprès duquel l'application est inscrite, accédez au panneau Inscriptions d'applications, cliquez sur une application et sélectionnez Propriétaires.

## <a name="making-microsoft-graph-api-calls"></a>Passer des appels à l'API Microsoft Graph 

Si vous rencontrez un problème qui vous semble incompréhensible au vu de ce qui apparaît dans l'interface utilisateur, n'hésitez pas à utiliser les appels Microsoft Graph. Vous pourrez ainsi effectuer les mêmes opérations que sur le portail Inscription d'applications. Dans le cadre de la préversion, ces API ne seront disponibles que sur le point de terminaison /beta de Microsoft Graph.  

Pour exécuter ces requêtes, le plus simple consiste à utiliser l'[Afficheur Graph](https://developer.microsoft.com/graph/graph-explorer). Vous pouvez également envisager d'autres options, comme l'utilisation de [Postman](https://www.postman.com/), ou utiliser PowerShell pour [appeler une requête web](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7).  

Vous pouvez utiliser Microsoft Graph pour activer et désactiver l'éditeur vérifié de votre application, et vérifier le résultat après avoir effectué l'une de ces opérations. Le résultat est visible à la fois sur l'objet [application](/graph/api/resources/application?view=graph-rest-beta) correspondant à l'inscription d'application et sur les [principaux de service](/graph/api/resources/serviceprincipal?view=graph-rest-beta) qui ont été instanciés à partir de cette application. Pour plus d'informations sur la relation entre ces objets, consultez : [Objets application et principal de service dans Azure Active Directory](app-objects-and-service-principals.md)  

Voici quelques exemples de requêtes utiles :  

### <a name="set-verified-publisher"></a>Activer l'éditeur vérifié 

Requête

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
response 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* est votre ID MPN. 

### <a name="unset-verified-publisher"></a>Désactiver l'éditeur vérifié 

Demande :  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
response 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Obtenir des informations sur l'éditeur vérifié à partir de l'objet Application 
 
```
GET https://graph.microsoft.com/beta/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Obtenir des informations sur l'éditeur vérifié à partir de l'objet Principal de service 
```
GET https://graph.microsoft.com/beta/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Informations de référence sur les erreurs 

La liste suivante répertorie les codes d'erreur que vous pouvez rencontrer lors de la résolution des problèmes avec Microsoft Graph ou lors du processus sur le portail d'inscription des applications.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

L'ID MPN que vous avez fourni (<MPNID>) n'existe pas ou vous n'y avez pas accès. Fournissez un ID MPN valide, puis réessayez. 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

L'ID MPN que vous avez fourni (<MPNID>) n'est pas valide. Fournissez un ID MPN valide, puis réessayez. 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

L'ID MPN que vous avez fourni (<MPNID>) n'est pas valide. Fournissez un ID MPN valide, puis réessayez. 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

L'ID MPN (<MPNID>) que vous avez fourni n'est pas allé au terme du processus de vérification. Finalisez ce processus dans l'Espace partenaires, puis réessayez. 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

L'ID MPN que vous avez fourni (<MPNID>) n'est pas valide. Fournissez un ID MPN valide, puis réessayez. 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

L'ID MPN que vous avez fourni (<MPNID>) n'est pas valide. Fournissez un ID MPN valide, puis réessayez. 

### <a name="applicationnotfound"></a>ApplicationNotFound  

L'application cible (<AppId>) est introuvable. Fournissez un ID d'application valide, puis réessayez. 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Cette fonctionnalité n'est pas prise en charge dans le locataire Azure AD B2C. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

Cette fonctionnalité n'est pas prise en charge dans un locataire vérifié par e-mail. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

Un domaine d'éditeur doit être défini pour l'application cible (<AppId>). Définissez un domaine d'éditeur, puis réessayez. 

### <a name="publisherdomainisnotdnsverified"></a>PublisherDomainIsNotDNSVerified  

Le domaine d'éditeur (<publisherDomain>) de l'application cible n'est pas un domaine vérifié dans ce locataire. Vérifiez un domaine de locataire à l'aide de la vérification DNS, puis réessayez. 

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

Le domaine d'éditeur (<publisherDomain>) de l'application cible ne correspond pas au domaine utilisé pour effectuer la vérification par e-mail dans l'Espace partenaires (<pcDomain>). Vérifiez que ces domaines correspondent, puis réessayez. 

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

Vous n'êtes pas autorisé à définir la propriété de l'éditeur vérifié sur l'application (<AppId>). 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

L'ID MPN n'a pas été fourni dans le corps de la requête ou le type de contenu de la requête n'était pas "application/json". 

### <a name="msanotsupported"></a>MSANotSupported  

Cette fonctionnalité n'est pas prise en charge pour les comptes clients Microsoft. Seules les applications inscrites dans Azure AD par un utilisateur d'Azure AD sont prises en charge. 

## <a name="next-steps"></a>Étapes suivantes

Si vous avez passé en revue l'ensemble des informations précédentes et que l'erreur de Microsoft Graph persiste, rassemblez autant d'informations que possible sur la requête qui a échoué et [contactez le support Microsoft](developer-support-help-options.md#open-a-support-request).

- Timestamp 
- CorrelationId 
- ObjectID ou UserPrincipalName de l'utilisateur connecté 
- ObjectId de l'application cible
- AppId de l'application cible
- TenantId où l'application est inscrite
- ID MPN
- Requête REST en cours 
- Code d'erreur et message renvoyé 
