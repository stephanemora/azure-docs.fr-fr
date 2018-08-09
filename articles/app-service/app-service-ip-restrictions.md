---
title: Restrictions d’adresse IP avec Azure App Service | Microsoft Docs
description: Comment utiliser des restrictions d’adresse IP avec Azure App Service
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 7/30/2018
ms.author: ccompy
ms.openlocfilehash: fb26d91ae772c4da1055da80366d6e8c6b80a6ac
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364306"
---
# <a name="azure-app-service-static-ip-restrictions"></a>Restrictions d’adresse IP statique avec Azure App Service #

Les restrictions d’adresse IP permettent de définir la liste des adresses IP qui sont autorisées à accéder à votre application. Dans cette liste, les adresses IP sont classées par ordre de priorité (Autoriser/Refuser). Les adresses autorisées peuvent inclure des adresses IPv4 et IPv6. Lorsqu’il y a une ou plusieurs entrées, il existe une règle implicite « Tout refuser » qui se trouve à la fin de la liste. 

La fonctionnalité Restrictions d’adresse IP peut être utilisée avec toutes les charges de travail hébergées par App Service, notamment les applications web, les applications API, les applications Linux, les applications de conteneur Linux et Functions. 

Lorsqu’une demande est envoyée à votre application, l’adresse IP dont provient la demande est vérifiée par rapport à la liste de restriction des adresses IP. Si l’adresse n’est pas autorisée à y accéder selon les règles définies dans la liste, le service répond avec le code d’état [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

La fonctionnalité Restrictions d’adresse IP est implémentée dans les rôles frontend App Service, qui sont en amont des hôtes de travail où votre code s’exécute. Les restrictions d’adresse IP sont donc comparables à des listes de contrôle d’accès (ACL) réseau.  

![Flux des restrictions d’adresse IP](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Pendant un temps, la fonctionnalité Restrictions d’adresse IP disponible dans le portail était un complément à la fonctionnalité ipSecurity d’IIS. La nouvelle fonctionnalité Restrictions d’adresse IP est différente. Vous pouvez toujours configurer ipSecurity dans le fichier web.config de votre application. Toutefois, les règles de restriction d’adresse IP basées sur le frontend seront appliquées avant que le trafic n’atteigne IIS.

## <a name="adding-and-editing-ip-restriction-rules-in-the-portal"></a>Ajout et modification des règles de restriction d’adresse IP dans le portail ##

Pour ajouter une règle de restriction d’adresse IP à votre application, dans le menu, ouvrez **Réseau**>**Restrictions d’adresse IP**, puis cliquez sur **Configurer des restrictions d’adresse IP**.

![Options réseau d’App Service](media/app-service-ip-restrictions/ip-restrictions.png)  

Dans l’interface utilisateur Restrictions d’adresse IP, vous pouvez consulter la liste des règles de restriction d’adresse IP définies pour votre application.

![Liste des restrictions d’adresse IP](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Si vos règles ont été configurées comme dans cette image, votre application accepte uniquement le trafic provenant de l’adresse 131.107.159.0/24 et refuse toute autre adresse IP.

Vous pouvez cliquer sur **[+] Ajouter** pour ajouter une nouvelle règle de restriction d’adresse IP. Quand vous ajoutez une règle, celle-ci est appliquée immédiatement. Les règles sont appliquées par ordre de priorité, du chiffre le moins élevé au chiffre le plus élevé. Il existe une règle implicite « Tout refuser » qui s’applique dès que vous ajoutez une règle. 

![Ajouter une règle de restriction d’adresse IP](media/app-service-ip-restrictions/ip-restrictions-add.png)

Pour les adresses IPv4 et IPv6, la notation CIDR doit être utilisée. Pour spécifier une adresse exacte, vous pouvez utiliser le format 1.2.3.4/32, où les quatre premiers octets représentent votre adresse IP, et /32 correspond au masque. La notation CIDR IPv4 est 0.0.0.0/0 pour toutes les adresses. Pour plus d’informations sur la notation CIDR, consultez [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Vous pouvez cliquer sur n’importe quelle ligne pour modifier une règle existante de restriction d’adresse IP. Les modifications sont appliquées immédiatement, y compris les changements de priorité.

![Modifier une règle de restriction d’adresse IP](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Pour supprimer une règle, cliquez sur les trois points (**...**) dans votre règle, puis cliquez sur **Supprimer**. 

![Supprimer une règle de restriction d’adresse IP](media/app-service-ip-restrictions/ip-restrictions-delete.png)

## <a name="programmatic-manipulation-of-ip-restriction-rules"></a>Manipulation par programmation des règles de restriction d’adresse IP ##

Il n’existe aucune interface CLI ou PowerShell pour la nouvelle fonctionnalité Restrictions d’adresse IP. Toutefois, vous pouvez définir les valeurs manuellement à l’aide d’une opération PUT dans la configuration d’application de Resource Manager. Par exemple, vous pouvez utiliser resources.azure.com et modifier le bloc ipSecurityRestrictions pour ajouter le code JSON nécessaire. 

Dans Resource Manager, ces informations se trouvent à l’emplacement suivant :

management.azure.com/subscriptions/**subscription ID**/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/**web app name**/config/web?api-version=2018-02-01

La syntaxe JSON de l’exemple précédent est la suivante :

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],
