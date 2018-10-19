---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/06/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3a3ee0cda3643ac73581f868e47905841d9f1563
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095589"
---
> [!IMPORTANT]
> - La préversion de l’authentification Azure AD pour les objets blob et les files d’attente est destinée uniquement à une utilisation hors production. Les contrats SLA (contrats de niveau de service) de production ne sont actuellement pas disponibles. Si l’authentification d’Azure AD n’est pas encore prise en charge pour votre scénario, continuez à utiliser l’autorisation basée sur une clé partagée ou les jetons SAP dans vos applications.
>
> - Durant la préversion, les attributions de rôles RBAC peuvent prendre jusqu’à cinq minutes pour se propager.
>
> - Vous devez utiliser HTTPS pour l’authentification auprès d’Azure AD lors de l’appel d’opérations d’objet blob et de file d’attente.
>
> - Dans la préversion, le portail Azure n’utilise pas les informations d’identification Azure AD pour lire et écrire les données d’objet blob et de file d’attente. Au lieu de cela, le portail continue à s’appuyer sur la clé d’accès au compte. Pour afficher ou mettre à jour les données d’objet blob ou de file d’attente dans le portail, l’utilisateur doit avoir un rôle RBAC qui englobe [Microsoft.Storage/storageAccounts/listkeys/action](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role), qui accorde des autorisations pour appeler [Comptes de stockage – ListKeys](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/listkeys). Les rôles de Contributeur et de Lecteur pour les objets blob et de file d’attente ne contiennent pas l’action **listkeys** dans le cadre de la préversion et ne donnent donc pas accès aux données via le portail Azure. Pour découvrir l’accès basé sur l’identité aux données des objets blob et de file d’attente pendant la préversion, utilisez PowerShell ou Azure CLI.
