---
title: Restrictions d’adresses IP clients - Azure App Service | Microsoft Docs
description: Comment utiliser les Restrictions d’accès avec Azure App Service
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
ms.date: 07/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 805de614246028bc75268e83991fa7831b990325
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882325"
---
# <a name="azure-app-service-static-access-restrictions"></a>Restrictions d’accès statique Azure App Service #

Restrictions d’accès permettent de définir une priorité autoriser ou refuser liste ordonnée des adresses IP qui sont autorisés à accéder à votre application. Les adresses autorisées peuvent inclure des adresses IPv4 et IPv6. Lorsqu’il y a une ou plusieurs entrées, il existe une règle implicite « Tout refuser » qui se trouve à la fin de la liste.

La fonctionnalité de Restrictions d’accès fonctionne avec toutes les Qu'app Service hébergés les charges de travail, notamment ; applications Web, applications API, applications Linux, les applications de conteneur Linux et fonctions.

Lorsqu’une demande est faite à votre application, l’adresse IP FROM est évaluée par rapport à la liste de Restrictions d’accès. Si l’adresse n’est pas autorisée à y accéder selon les règles définies dans la liste, le service répond avec le code d’état [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

La fonctionnalité de Restrictions d’accès est implémentée dans les rôles frontaux App Service, qui sont en amont des hôtes de travail dans lequel votre code s’exécute. Par conséquent, les Restrictions d’accès sont effectivement ACL réseau.  

![flux de restrictions d’accès](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Pendant une durée, la fonctionnalité de Restrictions d’accès dans le portail a été une couche sur la fonctionnalité ipSecurity dans IIS. La fonctionnalité de Restrictions d’accès actuelle est différente. Vous pouvez toujours configurer ipSecurity dans le fichier web.config de votre application, mais les règles de Restrictions d’accès basées sur frontal seront appliquées avant tout le trafic atteint IIS.

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Ajout et modification des règles de Restriction d’accès dans le portail ##

Pour ajouter une règle de restriction d’accès à votre application, utilisez le menu pour ouvrir **réseau**>**Restrictions d’accès** , puis cliquez sur **configurer des Restrictions d’accès**

![Options réseau d’App Service](media/app-service-ip-restrictions/ip-restrictions.png)  

À partir de l’interface utilisateur de Restrictions d’accès, vous pouvez consulter la liste des règles de restriction d’accès définis pour votre application.

![restrictions d’accès de liste](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Si vos règles ont été configurées comme dans cette image, votre application accepte uniquement le trafic provenant de l’adresse 131.107.159.0/24 et refuse toute autre adresse IP.

Vous pouvez cliquer sur **[+] ajouter** pour ajouter une nouvelle règle de restriction d’accès. Quand vous ajoutez une règle, celle-ci est appliquée immédiatement. Les règles sont appliquées par ordre de priorité, du chiffre le moins élevé au chiffre le plus élevé. Il existe une règle implicite « Tout refuser » qui s’applique dès que vous ajoutez une règle.

![Ajouter une règle de restriction d’accès](media/app-service-ip-restrictions/ip-restrictions-add.png)

Pour les adresses IPv4 et IPv6, la notation CIDR doit être utilisée. Pour spécifier une adresse exacte, vous pouvez utiliser le format 1.2.3.4/32, où les quatre premiers octets représentent votre adresse IP, et /32 correspond au masque. La notation CIDR IPv4 est 0.0.0.0/0 pour toutes les adresses. Pour plus d’informations sur la notation CIDR, consultez [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Vous pouvez cliquer sur n’importe quelle ligne pour modifier une règle de restriction d’accès existante. Les modifications sont appliquées immédiatement, y compris les changements de priorité.

![modifier une règle de restriction d’accès](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Pour supprimer une règle, cliquez sur les trois points (**...**) dans votre règle, puis cliquez sur **Supprimer**.

![supprimer la règle de restriction d’accès](media/app-service-ip-restrictions/ip-restrictions-delete.png)

Vous pouvez également restreindre l’accès de déploiement dans l’onglet suivant. Pour ajouter/modifier/supprimer à chaque règle, suivez la même étape que ci-dessus.

![restrictions d’accès de liste](media/app-service-ip-restrictions/ip-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Manipulation par programmation des règles de restriction d’accès ##

Il n’existe actuellement aucune interface CLI ou PowerShell pour la nouvelle fonctionnalité de Restrictions d’accès, mais les valeurs peuvent être définies manuellement avec une opération PUT sur la configuration d’application dans le Gestionnaire de ressources. Par exemple, vous pouvez utiliser resources.azure.com et modifier le bloc ipSecurityRestrictions pour ajouter le code JSON nécessaire.

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
