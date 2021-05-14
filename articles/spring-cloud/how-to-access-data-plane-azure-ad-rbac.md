---
title: Accéder à Config Server et Service Registry
titleSuffix: Azure Spring Cloud
description: Comment accéder aux points de terminaison de Config Server et Service Registry avec le contrôle d’accès en fonction du rôle d’Azure Active Directory.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java, subject-rbac-steps
ms.openlocfilehash: b8474e7f322025609222b4867db988d22aa2bcb9
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108291205"
---
# <a name="access-config-server-and-service-registry"></a>Accéder à Config Server et Service Registry

Cet article explique comment accéder aux services Spring Cloud Config Server et Spring Cloud Service Registry managés par Azure Spring Cloud à l’aide de la fonctionnalité RBAC (contrôle d’accès en fonction du rôle) d’Azure AD (Azure Active Directory).

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Attribuer un rôle à un utilisateur, un groupe ou un principal de service Azure AD, ou à une identité managée pour les ressources Azure

Affectez le rôle [azure-spring-cloud-data-reader](../role-based-access-control/built-in-roles.md#azure-spring-cloud-data-reader) à [utilisateur | groupe | principal-de-service | identité-managée] pour l’étendue [groupe-de-gestion | abonnement | groupe-de-ressources | ressources].

Pour connaître la procédure détaillée, consultez [Attribution de rôles Azure à l’aide du Portail Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="access-config-server-and-service-registry-endpoints"></a>Accéder aux points de terminaison de Config Server et Service Registry

Une fois le rôle Lecteur de données Azure Spring Cloud attribué, les clients peuvent accéder aux points de terminaison de Spring Cloud Config Server et de Spring Cloud Service Registry. Utilisez les procédures suivantes :

1. Obtenez un jeton d’accès. Une fois qu’un utilisateur Azure AD s’est vu attribuer le rôle Lecteur de données Azure Spring Cloud, les clients peuvent utiliser les commandes suivantes pour se connecter à Azure CLI avec les informations de l’utilisateur, du principal de service ou de l’identité managée, et obtenir un jeton d’accès. Pour plus d’informations, consultez [Authentifier Azure CLI](/cli/azure/authenticate-azure-cli). 

    ```azurecli
    az login
    az account get-access-token
    ```
2. Composez le point de terminaison. Nous prenons en charge les points de terminaison par défaut des services Spring Cloud Config Server et Spring Cloud Service Registry managés par Azure Spring Cloud. Pour plus d’informations, consultez [Production ready endpoints](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints) (Points de terminaison prêts pour la production). Les clients peuvent également obtenir la liste complète des points de terminaison pris en charge pour les services Spring Cloud Config Server et Spring Cloud Service Registry managés par Azure Spring Cloud en accédant aux points de terminaison suivants :

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/'* 

>[!NOTE]
> Si vous utilisez Azure Chine, remplacez `*.azuremicroservices.io` par `*.microservices.azure.cn`. [En savoir plus](/azure/china/resources-developer-guide#check-endpoints-in-azure).

3. Accédez au point de terminaison composé avec le jeton d’accès. Placez le jeton d’accès dans un en-tête pour permettre l’autorisation.  Seule la méthode « GET » est prise en charge.

    Par exemple, accédez à un point de terminaison comme *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health '* pour afficher l’état d’intégrité d’eureka.

    Si la réponse est *401 Non autorisé*, vérifiez si le rôle a bien été attribué.  Plusieurs minutes sont nécessaires pour que le rôle prenne effet. Vérifiez également que le jeton d’accès n’a pas expiré.

## <a name="next-steps"></a>Étapes suivantes
* [Authentifier Azure CLI](/cli/azure/authenticate-azure-cli)
* [Production ready endpoints](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints) (Points de terminaison prêts pour la production)

## <a name="see-also"></a>Voir aussi
* [Créer des rôles et des autorisations](how-to-permissions.md)