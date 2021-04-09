---
title: Comprendre l’authentification et l’autorisation de Device Update pour IoT Hub | Microsoft Docs
description: Découvrez comment Device Update pour IoT Hub utilise RBAC Azure pour fournir l’authentification et l’autorisation des utilisateurs et API de service.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ca55b1df347b47a6eb82557658d59a3de666b703
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558395"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Contrôle d’accès en fonction du rôle (RBAC) Azure et Device Update

Device Update utilise RBAC Azure pour fournir l’authentification et l’autorisation des utilisateurs et API de service.

## <a name="configure-access-control-roles"></a>Configurer les rôles de contrôle d’accès

Pour permettre à d’autres utilisateurs et applications d’accéder à Device Update, ceux-ci doivent avoir accès à cette ressource. Voici les rôles pris en charge par Device Update :

|   Nom du rôle   | Description  |
| :--------- | :---- |
|  Administrateur Device Update | Peut accéder à toutes les ressources Device Update  |
|  Lecteur Device Update| Peut afficher toutes les mises à jour et tous les déploiements |
|  Administrateur de contenu Device Update | Peut afficher, importer et supprimer des mises à jour  |
|  Lecteur du contenu Device Update | Peut afficher les mises à jour  |
|  Administrateur des déploiements Device Update | Peut gérer le déploiement des mises à jour sur les appareils|
|  Lecteur des déploiements Device Update| Peut afficher les déploiements des mises à jour sur les appareils |

Une combinaison de rôles peut être utilisée pour fournir le niveau d’accès approprié. Par exemple, un développeur peut importer et gérer des mises à jour à l’aide du rôle Administrateur de contenu Device Update, mais il a besoin du rôle Lecteur des déploiements Device Update pour voir la progression d’une mise à jour. À l’inverse, un opérateur de solutions doté du rôle Lecteur Device Update peut afficher toutes les mises à jour, mais il doit utiliser le rôle Administrateur des déploiements Device Update pour déployer une mise à jour spécifique sur des appareils.


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>S’authentifier auprès des API REST Device Update pour la publication et la gestion

Device Update utilise également Azure AD à des fins d’authentification pour publier et gérer le contenu par le biais des API de service. Pour bien démarrer, vous avez besoin de créer et de configurer une application cliente.

### <a name="create-client-azure-ad-app"></a>Créer une application Azure AD App cliente

Pour intégrer une application ou un service à Azure AD, [commencez par inscrire](../active-directory/develop/quickstart-register-app.md) une application auprès d’Azure AD. La configuration d’une application cliente varie selon le flux d’autorisation utilisé.  La configuration ci-dessous a pour but de vous guider si vous utilisez des API REST Device Update.

* Définissez l’authentification du client : « URI de redirection pour le client natif ou web ».
* Définissez les autorisations d’API. Device Update pour IoT Hub expose :
  * Autorisations déléguées : 'user_impersonation'
  * **Facultatif**, Accorder le consentement administrateur

[Étapes suivantes : Créer des ressources Device Update et configurer des rôles de contrôle d’accès](./create-device-update-account.md)