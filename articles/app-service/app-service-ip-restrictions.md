---
title: Restreindre l’accès - Azure App Service | Microsoft Docs
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
ms.date: 04/22/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 558b67b5b0e1ce4f452ce2ca2e97dd7e785c80b6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728701"
---
# <a name="azure-app-service-access-restrictions"></a>Restrictions d’accès Azure App Service #

Restrictions d’accès permettent de définir une liste ordonnée/de refus priorité qui contrôle l’accès réseau à votre application. La liste peut inclure des adresses IP ou sous-réseaux de réseau virtuel Azure. Lorsqu’il existe une ou plusieurs entrées, il est ensuite implicite « refuser tout » qui existe à la fin de la liste.

La fonctionnalité de Restrictions d’accès fonctionne avec tous les App Service, charge de travail hébergé, y compris ; applications Web, applications API, applications Linux, les applications de conteneur Linux et fonctions.

Lorsqu’une demande est faite à votre application, l’adresse est recherchée dans les règles d’adresses IP dans votre liste de restrictions d’accès. Si l’adresse est dans un sous-réseau qui est configuré avec les points de terminaison de service à Microsoft.Web, puis les sous-réseaux source sont comparé les règles de réseau virtuel dans votre liste de restrictions d’accès. Si l’adresse n’est pas autorisée à y accéder selon les règles définies dans la liste, le service répond avec le code d’état [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

La fonctionnalité de restrictions d’accès est implémentée dans les rôles frontaux App Service, qui sont en amont des hôtes de travail dans lequel votre code s’exécute. Par conséquent, les restrictions d’accès sont effectivement ACL réseau.

La possibilité de restreindre l’accès à votre application web à partir d’un réseau virtuel de Azure (VNet) est appelée [points de terminaison de service][serviceendpoints]. Points de terminaison de service permettent de restreindre l’accès à un service mutualisé à partir de sous-réseaux sélectionnés. Il doit être activé sur le côté de mise en réseau ainsi que le service activé avec. 

![flux de restrictions d’accès](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Ajout et modification des règles de Restriction d’accès dans le portail ##

Pour ajouter une règle de restriction d’accès à votre application, utilisez le menu pour ouvrir **réseau**>**Restrictions d’accès** , puis cliquez sur **configurer des Restrictions d’accès**

![Options réseau d’App Service](media/app-service-ip-restrictions/access-restrictions.png)  

À partir de l’interface utilisateur de Restrictions d’accès, vous pouvez consulter la liste des règles de restriction d’accès définis pour votre application.

![restrictions d’accès de liste](media/app-service-ip-restrictions/access-restrictions-browse.png)

La liste affiche toutes les restrictions actuelles qui se trouvent sur votre application. Si vous avez une restriction de réseau virtuel sur votre application, la table affiche si les points de terminaison de service sont activés pour Microsoft.Web. Lorsqu’il n’y a aucune restriction définie sur votre application, votre application sera accessible depuis n’importe où.  

Vous pouvez cliquer sur **[+] ajouter** pour ajouter une nouvelle règle de restriction d’accès. Quand vous ajoutez une règle, celle-ci est appliquée immédiatement. Les règles sont appliquées par ordre de priorité, du chiffre le moins élevé au chiffre le plus élevé. Il existe une règle implicite « Tout refuser » qui s’applique dès que vous ajoutez une règle.

![Ajouter une règle de restriction d’accès IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Lorsque vous créez une règle, vous devez sélectionner autoriser ou refuser, ainsi que le type de règle. Vous devez également fournir la valeur de priorité et ce que vous pouvez réserver l’accès.  Vous pouvez éventuellement ajouter un nom et une description à la règle.  

Règle basée sur pour définir une adresse IP, sélectionnez un type de IPv4 ou IPv6. Pour les adresses IPv4 et IPv6, la notation CIDR doit être utilisée. Pour spécifier une adresse exacte, vous pouvez utiliser le format 1.2.3.4/32, où les quatre premiers octets représentent votre adresse IP, et /32 correspond au masque. La notation CIDR IPv4 est 0.0.0.0/0 pour toutes les adresses. Pour plus d’informations sur la notation CIDR, consultez [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

![Ajouter une règle de restriction d’accès réseau virtuel](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Pour restreindre l’accès à des sous-réseaux sélectionnées, sélectionnez un type de réseau virtuel. Ci-dessous, vous serez en mesure de sélectionner l’abonnement, le réseau virtuel et le sous-réseau que vous souhaitez autoriser ou refuser l’accès avec. Si les points de terminaison de service ne sont pas encore activés avec Microsoft.Web pour le sous-réseau que vous avez sélectionné, il est automatiquement activé pour vous, sauf si vous cochez la case demandant ne pas à le faire. La situation où vous souhaiteriez activer sur l’application, mais pas le sous-réseau en grande partie concerne si vous disposez des autorisations pour activer les points de terminaison de service sur le sous-réseau ou non. Si vous avez besoin obtenir de quelqu'un d’autre pour activer les points de terminaison de service sur le sous-réseau, vous pouvez la case à cocher et que votre application configurée pour les points de terminaison de service en prévision de celle-ci en cours d’activation ultérieurement sur le sous-réseau. 

Vous pouvez cliquer sur n’importe quelle ligne pour modifier une règle de restriction d’accès existante. Les modifications sont appliquées immédiatement, y compris les changements de priorité.

![modifier une règle de restriction d’accès](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Lorsque vous modifiez une règle, vous ne pouvez pas modifier le type entre une règle d’adresse IP et une règle de réseau virtuel. 

![modifier une règle de restriction d’accès](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Pour supprimer une règle, cliquez sur les trois points (**...**) dans votre règle, puis cliquez sur **Supprimer**.

![supprimer la règle de restriction d’accès](media/app-service-ip-restrictions/access-restrictions-delete.png)

### <a name="scm-site"></a>Site SCM 

Outre la possibilité de contrôler l’accès à votre application, vous pouvez également restreindre l’accès au site scm utilisé par votre application. Le site scm est le web déployer le point de terminaison, ainsi que la console Kudu. Vous pouvez séparément adjoindre des restrictions d’accès au site scm à partir de l’application ou utiliser les mêmes pour l’application et le site scm. Lorsque vous activez la case pour ont les mêmes restrictions que votre application, tout ce dont s’affiche en grisé. Si vous décochez la case, les paramètres que vous aviez précédemment sur le site scm sont appliquées. 

![restrictions d’accès de liste](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

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

## <a name="function-app-ip-restrictions"></a>Restrictions d’adresse IP d’application de fonction

Restrictions d’adresse IP sont disponibles pour les deux applications de fonction avec les mêmes fonctionnalités que les plans App Service. L’activation des restrictions d’adresse IP désactive l’éditeur de code de portail pour les adresses IP non autorisé.

[En savoir plus ici](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
