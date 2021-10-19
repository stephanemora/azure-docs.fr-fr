---
title: Configurer les paramètres d’exportation dans le service FHIR
description: Cet article explique comment configurer des paramètres d’exportation dans le service FHIR
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/11/2021
ms.author: cavoeg
ms.openlocfilehash: 327a36714b822669cbc41fc8b8fb195bed4872ed
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122434086"
---
# <a name="configure-export-settings-and-set-up-a-storage-account"></a>Configurer les paramètres d’exportation et configurer un compte de stockage

Le service FHIR prend en charge $export commande qui vous permet d’exporter les données du compte de service FHIR vers un compte de stockage.

Trois étapes sont nécessaires à la configuration de l’exportation dans le service FHIR :

1. Activez l’identité managée sur le service de service FHIR.
2. Création d’un compte de stockage Azure (si vous ne l’avez pas déjà fait) et attribution de l’autorisation d’accès au service FHIR au compte de stockage.
3. Sélection du compte de stockage dans le service FHIR en tant que compte de stockage d’exportation.

## <a name="enabling-managed-identity-on-fhir-service"></a>Activation de Managed Identity sur le service FHIR

La première étape de la configuration du service FHIR pour l’exportation consiste à activer l’identité gérée sur le système sur le service. Pour plus d’informations sur les identités gérées dans Azure, consultez [à propos des identités gérées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Pour ce faire, accédez au service de service FHIR et sélectionnez **identité**. La modification de l’état **sur activé** active l’identité gérée dans le service de service FHIR.

![Activer une identité managée](media/export-data/fhir-mi-enabled.png)

À présent, vous pouvez passer à l’étape suivante en créant un compte de stockage et en attribuant une autorisation à notre service.

## <a name="adding-permission-to-storage-account"></a>Ajout d’autorisations au compte de stockage

L’étape suivante de l’exportation de données consiste à affecter l’autorisation d’écrire sur le compte de stockage par le service de service FHIR.

Une fois que vous avez créé un compte de stockage, accédez au **Access Control (IAM)** dans le compte de stockage, puis sélectionnez **Ajouter une attribution de rôle**. 

Pour plus d’informations sur l’affectation de rôles dans le Portail Azure, consultez [rôles intégrés Azure](../../role-based-access-control/role-assignments-portal.md).

ici, vous allez ajouter le rôle [Stockage contributeur de données Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) à notre nom de service, puis sélectionnez **enregistrer**.

![Page Ajouter une attribution de rôle](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

Vous êtes maintenant prêt à sélectionner le compte de stockage dans le service FHIR en tant que compte de stockage par défaut pour $export.

## <a name="selecting-the-storage-account-for-export"></a>Sélection du compte de stockage pour $export

La dernière étape consiste à affecter le compte de stockage Azure utilisé par le service FHIR pour exporter les données vers. Pour ce faire, accédez à **intégration** dans le service de service FHIR et sélectionnez le compte de stockage.

![Stockage d’exportation FHIR](media/export-data/fhir-export-storage.png)

Une fois que vous avez terminé cette étape finale, vous êtes maintenant prêt à exporter les données à l’aide de $export commande.

> [!Note]
> Seuls les comptes de stockage dans le même abonnement que pour le service FHIR peuvent être enregistrés comme destination pour les opérations de $export.