---
title: Mode d’appareil partagé pour les appareils Android | Azure
description: Découvrir le mode d’appareil partagé, qui permet aux employés de terrain de partager un appareil Android
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: marsma
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9928b64d286cc5072f28f7cc17e4af3e95662cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085617"
---
# <a name="shared-device-mode-for-android-devices"></a>Mode d’appareil partagé pour les appareils Android

> [!NOTE]
> Cette fonctionnalité est en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les employés de terrain, comme les représentants, les membres d’équipage et les techniciens d’intervention, utilisent souvent un appareil mobile partagé pour effectuer leur travail. Cela devient problématique quand ils commencent à partager des mots de passe ou des codes confidentiels pour accéder aux données des clients et de l’entreprise sur l’appareil partagé.

Le mode d’appareil partagé vous permet de configurer un appareil Android afin qu’il puisse être facilement partagé par plusieurs employés. Les employés peuvent se connecter et accéder rapidement aux informations client. Quand ils ont terminé leur journée ou leur tâche, ils peuvent se déconnecter de l’appareil et ce dernier est immédiatement prêt pour être utilisé par l’employé suivant.

Le mode d’appareil partagé permet également de gérer les appareils avec des identités Microsoft.

Pour créer une application en mode d’appareil partagé, les développeurs et les administrateurs d’appareils cloud travaillent ensemble :

- Les développeurs écrivent une application monocompte (les applications multicomptes ne sont pas prises en charge en mode d’appareil partagé), ajoutent `"shared_device_mode_supported": true` à la configuration de l’application et écrivent du code pour gérer des opérations comme la déconnexion des appareils partagés.
- Les administrateurs d’appareils préparent l’appareil à partager en installant l’application d’authentification et en définissant l’appareil en mode partagé à l’aide de l’application d’authentification. Seuls les utilisateurs qui ont le rôle [Administrateur d’appareil cloud](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator) peuvent mettre un appareil en mode partagé à l’aide de l’[application d’authentification](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview). Vous pouvez configurer l’appartenance de vos rôles organisationnels dans le portail Azure via : **Azure Active Directory** > **Rôles et administrateurs** > **Administrateur d’appareil cloud**.

 Cet article se concentre principalement sur les points sur lesquels les développeurs doivent réfléchir.

## <a name="single-vs-multiple-account-applications"></a>Applications monocomptes et multicomptes

Les applications écrites à l’aide du SDK de la bibliothèque d’authentification Microsoft (MSAL) peuvent gérer un seul compte ou plusieurs comptes. Pour plus d’informations, consultez [Mode monocompte ou mode multicompte](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account). Les fonctionnalités de plateforme d’identité Microsoft disponibles pour votre application varient selon que l’application s’exécute en mode monocompte ou en mode multicompte.

**Les applications en mode d’appareil partagé fonctionnent uniquement en mode monocompte**.

> [!IMPORTANT]
> Les applications qui ne prennent en charge que le mode multicompte ne peuvent pas s’exécuter sur un appareil partagé. Si un employé charge une application qui ne prend pas en charge le mode monocompte, elle ne s’exécutera pas sur l’appareil partagé.
>
> Les applications écrites avant la publication du SDK MSAL s’exécutent en mode multicompte et doivent être mises à jour pour prendre en charge le mode monocompte avant de pouvoir s’exécuter sur un appareil en mode partagé.

**Prise en charge à la fois d’un seul compte et de plusieurs comptes**

Votre application peut être conçue pour prendre en charge l’exécution à la fois sur des appareils personnels et sur des appareils partagés. Si votre application prend actuellement en charge plusieurs comptes et que vous souhaitez prendre en charge le mode d’appareil partagé, ajoutez la prise en charge du mode monocompte.

Vous pouvez également souhaiter que votre application change de comportement en fonction du type d’appareil sur lequel elle s’exécute. Utilisez `ISingleAccountPublicClientApplication.isSharedDevice()` pour déterminer quand elle doit exécuter en mode monocompte.

Il existe deux interfaces différentes qui représentent le type d’appareil sur lequel se trouve votre application. Quand vous demandez une instance d’application à partir de la fabrique d’applications de MSAL, l’objet d’application approprié est automatiquement fourni.

Le modèle d’objet suivant illustre le type d’objet que vous pouvez recevoir et ce qu’il signifie dans le contexte d’un appareil partagé :

![modèle d’héritage d’application cliente publique](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Vous devez effectuer une vérification de type et effectuer un cast vers l’interface appropriée quand vous obtenez votre objet `PublicClientApplication`. Le code suivant vérifie si le mode est multicompte ou monocompte, et effectue un cast de l’objet application en conséquence :

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

Les différences suivantes s’appliquent selon que votre application s’exécute sur un appareil partagé ou sur un appareil personnel :

|  | Appareil en mode partagé  | Appareil personnel |
|---------|---------|---------|
| **Comptes (Accounts)**     | Un seul compte | Plusieurs comptes |
| **Connexion** | Global | Global |
| **Déconnexion** | Global | Chaque application peut contrôler si la déconnexion est locale sur l’application ou pour la famille d’applications. |
| **Types de comptes pris en charge** | Comptes professionnels uniquement | Comptes personnels et professionnels pris en charge  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Raisons de vouloir prendre en charge uniquement le mode monocompte

Si vous écrivez une application destinée à être utilisée uniquement par des employés de terrain avec un appareil partagé, nous vous recommandons de prendre en charge uniquement le mode monocompte. Il inclut la plupart des applications concentrées sur les tâches, comme les applications de dossiers médicaux, les applications de facturation et la plupart des applications métier. Prendre en charge uniquement le mode monocompte simplifie le développement, car vous n’avez pas besoin d’implémenter les fonctionnalités supplémentaires qui font partie des applications multicomptes.

## <a name="what-happens-when-the-device-mode-changes"></a>Ce qui se passe quand le mode de l’appareil change

Si votre application s’exécute en mode multicomptes et qu’un administrateur met l’appareil en mode d’appareil partagé, tous les comptes de l’appareil sont effacés de l’application et cette dernière passe en mode monocompte.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Déconnexion des appareils partagés et cycle de vie global des applications

Quand un utilisateur se déconnecte, vous devez prendre des mesures pour protéger la confidentialité et les données de l’utilisateur. Par exemple, si vous générez une application de dossiers médicaux, vous voulez garantir que les dossiers des patients précédemment affichés sont effacés quand l’utilisateur se déconnecte. Votre application doit être préparée à cette situation et procéder à une vérification chaque fois qu’elle passe au premier plan.

Quand votre application utilise MSAL pour déconnecter l’utilisateur dans une application en cours d’exécution sur un appareil en mode partagé, le compte connecté et les jetons mis en cache sont supprimés de l’application et de l’appareil.

Le diagramme suivant présente le cycle de vie global des applications et les événements courants qui peuvent se produire pendant que votre application s’exécute. Le diagramme présente les étapes effectuées à compter du lancement d’une activité, la connexion et la déconnexion d’un compte, ainsi que la façon dont des événements tels que la suspension, la reprise et l’arrêt de l’activité s’intègrent.

![Cycle de vie des applications d’appareils partagés](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Étapes suivantes

Essayez le tutoriel [Utiliser le mode d’appareil partagé dans votre application Android](tutorial-v2-shared-device-mode.md) qui montre comment exécuter une application pour employés de terrain sur un appareil Android en mode partagé.
