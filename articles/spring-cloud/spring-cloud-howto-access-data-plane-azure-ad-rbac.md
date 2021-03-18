---
title: Accéder au plan de données avec Azure AD, RBAC
description: Explique comment accéder au plan de données avec le contrôle d’accès en fonction du rôle Azure Active Directory.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2909d40fbc7cb5b310ea17f17a6e683ce47638ce
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220092"
---
# <a name="access-the-data-plane-with-azure-active-directory-and-role-based-access-control"></a>Accéder au plan de données avec Azure Active Directory et le contrôle d’accès en fonction du rôle

Cet article explique comment accéder au serveur de configuration Azure Spring Cloud et aux points de terminaison du registre de service à l’aide du contrôle d’accès en fonction du rôle (RBAC) Azure Active Directory (AAD).

## <a name="assign-role-to-aad-usergroup-msi-or-service-principal"></a>Attribuer un rôle à un utilisateur/groupe AAD, une identité MSI ou un principal de service

Pour utiliser AAD et RBAC, vous devez affecter le rôle *Lecteur de données Azure Spring Cloud* à un utilisateur, un groupe ou un principal de service en appliquant la procédure suivante :

1. Accédez à la page de vue d’ensemble du service de votre instance de service.

2. Cliquez sur **Contrôle d’accès (IAM)** pour ouvrir le panneau de contrôle d’accès.

3. Cliquez sur le bouton **Ajouter** et sur **Ajouter des attributions de rôles** (une autorisation peut être nécessaire pour l’ajout).

4. Recherchez et sélectionnez *Lecteur de données Azure Spring Cloud* sous **Rôle**.
5. Attribuez l’accès à `User, group, or service principal` ou `User assigned managed identity` en fonction du type d’utilisateur. Recherchez et sélectionnez l’utilisateur.  
6. Cliquez sur `Save`

   ![attribuer un rôle](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-data-plane"></a>Accéder au plan de données

Une fois qu’un utilisateur AAD s’est vu attribuer le rôle *Lecteur de données Azure Spring Cloud*, les clients peuvent se connecter à Azure CLI avec l’utilisateur, le principal de service ou l’identité managée.  Pour obtenir un jeton d’accès, consultez [Authentifier Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).  Utilisez ensuite les commandes suivantes.

```azurecli
az login
az account get-access-token
```

Actuellement, l’interface CLI prend en charge les points de terminaison par défaut du serveur de configuration et du registre de service. Pour plus d’informations, consultez [Production ready endpoints](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints) (Points de terminaison prêts pour la production). 

Les clients peuvent également obtenir une liste complète des points de terminaison du serveur de configuration et du registre de service pris en charge en accédant aux points de terminaison :
* *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/*
* *https://SERVICE_NAME.Root_Endpoint/config/actuator/* 

Le jeton d’accès dans l’en-tête fournit l’autorisation. Seule la méthode « GET » est prise en charge.

Par exemple, accédez à un point de terminaison comme *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/health* pour afficher l’état d’intégrité d’eureka.

Différents points de terminaison racines sont affichés ci-dessous en fonction de différents types de cloud.

| Cloud          | Point de terminaison racine              |
| -------------- | -------------------------- |
| Public         | svc.azuremicroservices.io  |
| Mooncake/China | svc.microservices.azure.cn |

Si la réponse est *401 Non autorisé*, vérifiez si le rôle a bien été attribué.  Plusieurs minutes sont nécessaires pour que le rôle prenne effet. Vérifiez également que le jeton d’accès n’a pas expiré.

## <a name="next-steps"></a>Étapes suivantes
* [Authentifier Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Production ready endpoints](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints) (Points de terminaison prêts pour la production)

## <a name="see-also"></a>Voir aussi
* [Créer des rôles et des autorisations](spring-cloud-howto-permissions.md)