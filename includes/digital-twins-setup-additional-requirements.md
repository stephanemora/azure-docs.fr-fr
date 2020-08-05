---
author: baanders
description: fichier include pour les exigences supplémentaires possibles dans le programme d’installation d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 230304677b78f00b2d1288c846f8bf704cd8a497
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407467"
---
Il est possible que votre organisation exige des actions supplémentaires de la part des propriétaires d’abonnement pour configurer correctement une inscription d’application (et donc pour terminer la configuration d’une instance Azure Digital Twins utilisable). Les étapes requises peuvent varier en fonction des paramètres spécifiques de votre organisation.

Voici quelques activités courantes qu’un propriétaire peut devoir effectuer. Ces opérations et d’autres peuvent être effectuées à partir de la page [*Inscriptions d’applications Azure AD*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) dans le portail Azure.
* Accordez le consentement administrateur pour l’inscription d’application. Votre organisation peut avoir activé globalement l’option *Consentement administrateur requis* dans Azure AD pour toutes les inscriptions d’applications au sein de votre abonnement. Si c’est le cas, le propriétaire devra sélectionner ce bouton pour votre société dans la page *Autorisations de l’API* de l’inscription d’application pour que celle-ci soit valide :

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/grant-admin-consent.png" alt-text="Vue du portail du bouton « Consentement administrateur requis » sous Autorisations de l’API":::
  - Si le consentement a été accordé avec succès, l’entrée pour Azure Digital Twins doit alors indiquer une valeur d’*État* _Accordé pour **(votre société)**_
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/granted-admin-consent-done.png" alt-text="Vue du portail du consentement administrateur accordé à la société sous Autorisations de l’API":::
* Activer l’accès client public
* Définir des URL de réponse spécifiques pour l’accès web et au bureau
* Autoriser les flux d’authentification OAuth2 implicites

Pour plus d’informations sur l’inscription d’applications et ses différentes options d’installation, consultez [*Inscrire une application avec la plateforme d’identités Microsoft*](https://docs.microsoft.com/graph/auth-register-app-v2).

Vous disposez maintenant d’une instance Azure Digital Twins prête à l’emploi, vous avez accordé des autorisations pour la gérer, et vous avez configuré des autorisations pour qu’une application cliente y accède.