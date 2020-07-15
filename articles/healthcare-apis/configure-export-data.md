---
title: Configurer les paramètres d’exportation dans API Azure pour FHIR
description: Cet article décrit comment configurer les paramètres d’exportation dans API Azure pour FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 46a55b83b38593a514d40a9f75d99739a1efb8a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871019"
---
# <a name="configure-export-setting-and-export-the-data-to-a-storage-account"></a>Configurer le paramètre d’exportation et exporter les données vers un compte de stockage

API Azure pour FHIR prend en charge la commande $export qui vous permet d’exporter les données d’un compte API Azure pour FHIR vers un compte de stockage.

Quatre étapes sont nécessaires pour effectuer l’exportation dans API Azure pour FHIR :

1. Activer l’identité managée sur le service API Azure pour FHIR
2. Créer un compte de stockage Azure (si vous ne l’avez pas déjà fait) et attribuer au compte de stockage une autorisation d’accès à API Azure pour FHIR
3. Sélectionner le compte de stockage dans API Azure pour FHIR en tant que compte de stockage d’exportation
4. Exécuter l’exportation en appelant la commande $export sur API Azure pour FHIR

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Activation de l’identité managée sur API Azure pour FHIR

La première étape de la configuration d’API Azure pour FHIR en vue de l’exportation consiste à activer l’identité managée à l’échelle du système sur le service. Vous pouvez lire toutes les informations relatives aux identités managées dans Azure [ici](../active-directory/managed-identities-azure-resources/overview.md).

Pour ce faire, accédez au service API Azure pour FHIR et sélectionnez le panneau Identité. La modification de l’état sur activé permettra d’activer l’identité managée dans le service API Azure pour FHIR.

![Activer une identité managée](media/export-data/fhir-mi-enabled.png)

Nous pouvons maintenant passer à l’étape suivante et créer un compte de stockage et attribuer une autorisation à notre service.

## <a name="adding-permission-to-storage-account"></a>Ajout d’autorisations au compte de stockage

L’étape suivante de l’exportation consiste à attribuer au service API Azure pour FHIR l’autorisation d’écrire dans le compte de stockage.

Après avoir créé un compte de stockage, accédez au panneau Access Control (IAM) dans Compte de stockage et sélectionnez Ajouter des attributions de rôles.

![Activer une identité managée](media/export-data/fhir-export-role-assignment.png)

Ici, nous ajoutons ensuite le rôle Contributeur aux données blob du stockage à notre nom de service.

![Activer une identité managée](media/export-data/fhir-export-role-add.png)

Nous sommes maintenant prêts pour l’étape suivante, dans laquelle nous pouvons sélectionner le compte de stockage dans API Azure pour FHIR comme compte de stockage par défaut pour $export.

## <a name="selecting-the-storage-account-for-export"></a>Sélection du compte de stockage pour $export

La dernière étape avant l’appel de la commande $export consiste à attribuer le compte de stockage Azure qu’API Azure pour FHIR utilisera pour l’exportation des données. Pour ce faire, accédez au panneau Intégration du service API Azure pour FHIR dans le portail Azure et sélectionnez le compte de stockage. 

![Activer une identité managée](media/export-data/fhir-export-storage.png)

Après cela, nous sommes prêts à exporter les données à l’aide de la commande $export.

## <a name="exporting-the-data-using-export-command"></a>Exportation des données à l’aide de la commande $export

Après avoir configuré API Azure pour FHIR pour l’exportation, nous pouvons maintenant utiliser la commande $export pour exporter les données hors du service et vers le compte de stockage que nous avons spécifié. Pour savoir comment appeler la commande $export dans le serveur FHIR, consultez la documentation sur la spécification $export à l’adresse [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)

> [!IMPORTANT]
> Notez qu’actuellement, API Azure pour FHIR prend uniquement en charge l’exportation au niveau du système, comme défini dans la spécification d’exportation à l’adresse [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html). Actuellement, nous ne prenons pas en charge les paramètres de requête avec $export.

>[!div class="nextstepaction"]
>[Paramètres supplémentaires](azure-api-for-fhir-additional-settings.md)