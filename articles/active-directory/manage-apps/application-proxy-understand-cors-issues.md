---
title: Comprendre et résoudre les problèmes d’Azure AD Application Proxy CORS
description: Fournit une présentation de CORS dans le Proxy d’Application et comment identifier et résoudre les problèmes CORS.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 2b6adcf4231aa44a4f28d277e963efa16de8af81
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399348"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Comprendre et résoudre les problèmes d’Azure Active Directory Application Proxy CORS

[Ressources cross-origin CORS (partage)](http://www.w3.org/TR/cors/) peut présenter parfois des difficultés pour les applications et les API que vous publiez via le Proxy d’Application Azure Active Directory. Cet article traite des problèmes d’Azure AD Application Proxy CORS et des solutions.

Sécurité du navigateur empêche généralement une page web à partir d’adresser des demandes AJAX vers un autre domaine. Cette restriction est appelée la *stratégie de même origine* et empêche un site malveillant de lire des données sensibles à partir d’un autre site. Toutefois, vous pouvez parfois laisser les autres sites à appeler votre API web. CORS est une norme W3C qui permet à un serveur assouplir la stratégie de même origine et autoriser certaines requêtes cross-origin tout en refusant d’autres.

## <a name="understand-and-identify-cors-issues"></a>Comprendre et identifier les problèmes CORS

Deux URL ont la même origine que s’ils ont des schémas identiques, les hôtes et les ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)), telles que :

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Les URL suivantes ont différentes origines que le précédent deux :

-   http :\//contoso.net - domaine différent
-   http :\//contoso.com:9000/foo.html - port différent
-   https :\//contoso.com/foo.html - schéma différent
-   http :\//www.contoso.com/foo.html - autre sous-domaine

Stratégie de même origine empêche des applications d’accéder aux ressources à partir d’autres origines, sauf si elles utilisent les en-têtes de contrôle d’accès correctes. Si les en-têtes CORS sont absents ou incorrects, les demandes cross-origin échouent. 

Vous pouvez identifier les problèmes CORS à l’aide des outils de débogage de navigateur :

1. Lancer le navigateur et accédez à l’application web.
1. Appuyez sur **F12** pour afficher la console de débogage.
1. Essayez de reproduire la transaction et examinez le message de console. Une violation de CORS génère une erreur de la console relatifs à l’origine.

Dans la capture d’écran suivante, en sélectionnant le **essayer** bouton a provoqué un message d’erreur CORS que le protocole https :\//corswebclient-contoso.msappproxy.net n’a pas été trouvé dans l’en-tête Access-Control-Allow-Origin.

![Problème CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Défis CORS avec le Proxy d’Application

L’exemple suivant montre un scénario d’Azure AD Application Proxy CORS. Les hôtes de serveur interne un **CORSWebService** contrôleur API web et un **CORSWebClient** qui appelle **CORSWebService**. Il existe une requête AJAX à partir de **CORSWebClient** à **CORSWebService**.

![Demande de même origine en local](./media/application-proxy-understand-cors-issues/image1.png)

L’application de CORSWebClient fonctionne lorsque vous hébergez il en local, mais soit ne parvient pas à la charge ou génère une erreur lors de la publication via le Proxy d’Application Azure AD. Si vous avez publié les applications CORSWebClient et CORSWebService séparément en tant que différentes applications via le Proxy d’Application, les deux applications sont hébergées sur des domaines différents. Une requête AJAX à partir de CORSWebClient à CORSWebService est une demande de cross-origin, et il échoue.

![La requête CORS de Proxy d’application](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Solutions aux problèmes de CORS de Proxy d’Application

Vous pouvez résoudre le problème précédent de CORS dans l’une de plusieurs façons.

### <a name="option-1-set-up-a-custom-domain"></a>Option 1 : Configurer un domaine personnalisé

Utiliser un Proxy d’Application Azure AD [domaine personnalisé](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) pour publier à partir de la même origine, sans avoir à apporter des modifications à des origines de l’application, le code ou les en-têtes. 

### <a name="option-2-publish-the-parent-directory"></a>Option 2 : Publier le répertoire parent

Publier le répertoire parent du deux applications. Cette solution fonctionne particulièrement bien si vous avez uniquement deux applications sur le serveur web. Au lieu de la publication de chaque application séparément, vous pouvez publier le répertoire parent commun, ce qui se traduit dans la même origine.

Les exemples suivants montrent la page Proxy d’Application Azure AD pour l’application CORSWebClient du portail.  Lorsque le **URL interne** a la valeur *contoso.com/CORSWebClient*, l’application ne peut pas effectuer des demandes réussies le *contoso.com/CORSWebService* directory, car ils sont cross-origin. 

![Publier les application individuellement](./media/application-proxy-understand-cors-issues/image4.png)

Au lieu de cela, définissez la **URL interne** pour publier le répertoire parent, qui comprend à la fois le *CORSWebClient* et *CORSWebService* répertoires :

![Publier le répertoire parent](./media/application-proxy-understand-cors-issues/image5.png)

Les URL d’application qui en résulte résolvent efficacement le problème CORS :

- https :\//corswebclient-contoso.msappproxy.net/CORSWebService
- https :\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Option 3 : Mettre à jour des en-têtes HTTP

Ajouter un en-tête de réponse HTTP personnalisé sur le service web pour faire correspondre la demande d’origine. Pour les sites Web dans Internet Information Services (IIS) en cours d’exécution, utilisez le Gestionnaire IIS pour modifier l’en-tête :

![Ajouter un en-tête de réponse personnalisée dans le Gestionnaire des services Internet](./media/application-proxy-understand-cors-issues/image6.png)

Cette modification ne nécessite aucune modification de code. Vous pouvez le vérifier dans les traces Fiddler :

**Valider l’ajout de l’en-tête**\
HTTP/1.1 200 OK\
Cache-Control : no-Server\
Pragma : no-Server\
Content-Type : text/plain ; charset = utf-8\
Date d’expiration :-1\
Varient : Encoding\ accepter
Serveur :  Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin : https://corswebclient-contoso.msappproxy.net** \
X-AspNet-Version: 4.0.30319\
X-alimenté par : ASP.NET\
Content-Length : 17

### <a name="option-4-modify-the-app"></a>Option 4 : Modifier l’application

Vous pouvez modifier votre application pour prendre en charge de CORS en ajoutant l’en-tête Access-Control-Allow-Origin, avec les valeurs appropriées. La façon d’ajouter l’en-tête dépend du langage de code de l’application. Modification du code d’est l’option moins recommandée, car il nécessite le plus d’efforts.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Option 5 : Étendre la durée de vie du jeton d’accès

Certains problèmes CORS ne peut pas être résolues, par exemple lorsque votre application redirige vers *login.microsoftonline.com* pour l’authentification, et le jeton d’accès expire. CORS appeler, puis échoue. Une solution de contournement pour ce scénario consiste à étendre la durée de vie du jeton d’accès, pour éviter qu’il arrive à expiration lors d’une session utilisateur. Pour plus d’informations sur la procédure à suivre, consultez [des durées de vie de jeton configurables dans Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Voir aussi
- [Tutoriel : Ajouter une application en local pour l’accès à distance via le Proxy d’Application dans Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Planifier un déploiement de Proxy d’Application Azure AD](application-proxy-deployment-plan.md) 
- [Accès à distance aux applications locales via le Proxy d’Application Azure Active Directory](application-proxy.md) 
