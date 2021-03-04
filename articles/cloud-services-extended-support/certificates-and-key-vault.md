---
title: Stocker et utiliser des certificats dans Azure Cloud Services (support étendu)
description: Processus de stockage et d’utilisation des certificats dans Azure Cloud Services (support étendu)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: d9ff86eeb0e64e7edbad0eeca51b04cabbd191e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722651"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Utiliser des certificats avec Azure Cloud Services (support étendu)

Key Vault est utilisé pour stocker les certificats associés aux services cloud (support étendu). Vous pouvez créer des coffres de clés par le biais du [portail Azure](../key-vault/general/quick-create-portal.md) et de [PowerShell](../key-vault/general/quick-create-powershell.md). Ajoutez les certificats à Key Vault, puis référencez leurs empreintes dans le fichier de configuration de service. Vous devez également octroyer à Key Vault les autorisations appropriées afin que la ressource Cloud Services (support étendu) puisse récupérer les certificats stockés sous forme de secrets à partir de Key Vault.  

## <a name="upload-a-certificate-to-key-vault"></a>Charger un certificat sur Key Vault 

1.  Connectez-vous au portail Azure et accédez au coffre de clés. Si vous n’avez pas de coffre de clés configuré, vous pouvez choisir d’en créer un dans cette même fenêtre.

2. Sélectionnez **Stratégies d’accès**.

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="Image montrant la sélection de l’option Stratégies d’accès dans le panneau du coffre de clés.":::

3. Vérifiez que les stratégies d’accès incluent les propriétés suivantes :
    - **Activer l’accès aux machines virtuelles Azure pour le déploiement**
    - **Activer l’accès à Azure Resource Manager pour le déploiement de modèles** 

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="Image montrant la fenêtre Stratégies d’accès dans le portail Azure.":::
 
4.  Sélectionnez **Certificats**. 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="Image montrant la sélection de l’option Certificats dans la fenêtre des stratégies du panneau du coffre de clés dans le portail Azure.":::

5. Sélectionnez **Générer/Importer**.

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="Image montrant la sélection de l’option Générer/Importer.":::

4.  Complétez les informations nécessaires pour terminer le chargement du certificat. 

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="Image montrant la fenêtre d’importation dans le portail Azure.":::

5.  Ajoutez les détails du certificat à votre rôle dans le fichier de configuration de service (.cscfg). Vérifiez que l’empreinte du certificat dans le portail Azure correspond à celle du fichier de configuration de service (.cscfg). 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```

## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [prérequis du déploiement](deploy-prerequisite.md) de Cloud Services (support étendu).
- Consultez les [questions fréquentes (FAQ)](faq.md) sur Cloud Services (support étendu).
- Déployez une instance de Cloud Services (support étendu) avec le [portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).