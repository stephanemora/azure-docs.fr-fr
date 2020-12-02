---
title: Résoudre les problèmes de sécurité et de contrôle d’accès
description: Découvrez comment résoudre les problèmes de sécurité et de contrôle d’accès dans Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008721"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Résoudre les problèmes de sécurité et de contrôle d’accès dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article présente des méthodes couramment employées pour résoudre les problèmes de sécurité et de contrôle d’accès dans Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erreurs et messages courants

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>Problème de clé d’authentification non valide ou vide après la désactivation de l’accès au réseau public

#### <a name="symptoms"></a>Symptômes

Le runtime d’intégration auto-hébergé renvoie l’erreur « La clé d’authentification n’est pas valide ou est vide » après la désactivation de l’accès au réseau public pour Data Factory.

#### <a name="cause"></a>Cause

Le problème est probablement dû à une erreur de résolution DNS, car la désactivation de la connectivité publique et l’établissement d’un point de terminaison privé ne permettent pas la reconnexion.

#### <a name="resolution"></a>Résolution

1. Un PsPing a été effectué dans le FQDN d’ADF pour découvrir que la mémoire tampon redirigeait vers un point de terminaison public d’ADF, même après désactivation de celui-ci.

1. Modifiez le fichier d’hôte dans la machine virtuelle pour mapper l’adresse IP privée au FQDN et réexécutez un PsPing. La mémoire tampon sera en mesure d’accéder à l’adresse IP privée correcte d’ADF.

1. Réinscrivez le runtime d’intégration auto-hébergé qui sera de nouveau opérationnel.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Impossible d’inscrire la clé d’authentification de l’IR sur les machines virtuelles auto-hébergées en raison d’une liaison privée

#### <a name="symptoms"></a>Symptômes

Impossible d’inscrire la clé d’authentification de l’IR sur les machines virtuelles auto-hébergées en raison d’une liaison privée activée.

Les informations d’erreur s’affichent comme indiqué ci-dessous :

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Cause

Le problème peut être dû à la machine virtuelle sur laquelle le SHIR est en train d’être installé. L’accès au réseau public doit être activé pour la connexion au cloud.

#### <a name="resolution"></a>Résolution

 **Solution 1 :** Suivez les étapes ci-dessous pour résoudre le problème :

1. Accédez au lien ci-dessous : 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. Cliquez sur l’option **Essayer** et renseignez toutes les informations requises. Collez également la propriété ci-dessous dans le **Corps** :

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Cliquez sur **Exécuter** à la fin de la page pour exécuter la fonction. Assurez-vous que vous obtiendriez le code de réponse 200. La propriété que nous avons collée s’affichera également dans la définition JSON.

1. Vous pouvez ensuite essayer d’ajouter à nouveau la clé d’authentification IR dans le runtime d’intégration.


**Solution 2 :** Vous pouvez vous reporter à l’article ci-dessous pour obtenir la solution :

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Essayez d’activer l’accès au réseau public avec l’interface utilisateur.

![Activer l’accès public au réseau](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes, essayez les ressources suivantes :

*  [Private Link pour Data Factory](data-factory-private-link.md)
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Page de questions Microsoft Q&A](/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)