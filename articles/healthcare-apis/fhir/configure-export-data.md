---
title: Configurer les paramètres d’exportation dans API Azure pour FHIR
description: Cet article décrit comment configurer les paramètres d’exportation dans API Azure pour FHIR
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/11/2021
ms.author: cavoeg
ms.openlocfilehash: b245fcaecde13c4a751926e9b7bd639a43b36701
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111809043"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Configurer le paramètre d’exportation et le compte de stockage

API Azure pour FHIR prend en charge la commande $export qui vous permet d’exporter les données d’un compte API Azure pour FHIR vers un compte de stockage.

Trois étapes sont nécessaires à la configuration de l’exportation dans API Azure pour FHIR :

1. Activez l’identité managée sur l’API Azure pour le service FHIR.
2. Création d’un compte de stockage Azure (si vous ne l’avez pas déjà fait) et attribution d’une autorisation d’accès à l’API Azure pour FHIR au compte de stockage.
3. Sélection du compte de stockage dans l’API Azure pour FHIR en tant que compte de stockage d’exportation.

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Activation de l’identité managée sur API Azure pour FHIR

La première étape de la configuration d’API Azure pour FHIR en vue de l’exportation consiste à activer l’identité managée à l’échelle du système sur le service. Pour plus d’informations sur les identités gérées dans Azure, consultez [à propos des identités gérées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Pour ce faire, accédez au service Azure API pour FHIR et sélectionnez **identité**. La modification de l’État en on active l’identité gérée dans l’API Azure pour **le** service FHIR.

![Activer une identité managée](media/export-data/fhir-mi-enabled.png)

À présent, vous pouvez passer à l’étape suivante en créant un compte de stockage et en attribuant une autorisation à notre service.

## <a name="adding-permission-to-storage-account"></a>Ajout d’autorisations au compte de stockage

L’étape suivante de l’exportation de données consiste à attribuer l’autorisation pour l’API Azure pour le service FHIR à écrire dans le compte de stockage.

Une fois que vous avez créé un compte de stockage, accédez au **Access Control (IAM)** dans le compte de stockage, puis sélectionnez **Ajouter une attribution de rôle**. 

Pour plus d’informations sur l’affectation de rôles dans le Portail Azure, consultez [rôles intégrés Azure](../../role-based-access-control/role-assignments-portal.md).

C’est ici que vous allez ajouter le [contributeur de stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) de rôle à notre nom de service, puis sélectionnez **Enregistrer**.

![Page Ajouter une attribution de rôle](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

Vous êtes maintenant prêt à sélectionner le compte de stockage dans l’API Azure pour FHIR comme compte de stockage par défaut pour $export.

## <a name="selecting-the-storage-account-for-export"></a>Sélection du compte de stockage pour $export

La dernière étape consiste à affecter le compte de stockage Azure utilisé par l’API Azure pour FHIR pour exporter les données vers. Pour ce faire, accédez à **intégration** dans l’API Azure pour le service FHIR et sélectionnez le compte de stockage.

![Stockage d’exportation FHIR](media/export-data/fhir-export-storage.png)

Une fois que vous avez terminé cette étape finale, vous êtes maintenant prêt à exporter les données à l’aide de $export commande.

> [!Note]
> Seuls les comptes de stockage dans le même abonnement que pour l’API Azure pour FHIR peuvent être enregistrés comme destination pour les opérations de $export.

Pour plus d’informations sur la configuration des paramètres de base de données, le contrôle d’accès, l’activation de la journalisation des diagnostics et l’utilisation d’en-têtes personnalisés pour ajouter des données aux journaux d’audit, consultez :

>[!div class="nextstepaction"]
>[Paramètres supplémentaires](azure-api-for-fhir-additional-settings.md)
