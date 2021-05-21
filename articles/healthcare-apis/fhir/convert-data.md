---
title: Conversion de données pour l’API Azure pour FHIR
description: Utilisez le point de terminaison $convert-data et les modèles customize-converter pour convertir des données dans l’API Azure pour FHIR.
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 05/11/2021
ms.author: ranku
ms.openlocfilehash: 8d60cde14d52dceb58ea5c68383fad192a1e1ff3
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110078685"
---
# <a name="how-to-convert-data-to-fhir-preview"></a>Comment convertir des données en FHIR (préversion)

> [!IMPORTANT]
> Cette fonctionnalité est disponible en version préliminaire publique et elle est fournie sans contrat de niveau de service. Il n’est pas recommandé pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le point de terminaison personnalisé $convert-data dans l’API Azure pour FHIR est destiné à la conversion de données de différents formats vers FHIR. Il utilise le moteur de modèle Liquid et les modèles du projet [FHIR Converter](https://github.com/microsoft/FHIR-Converter) comme modèles par défaut. Vous pouvez personnaliser ces modèles de conversion en fonction de vos besoins. Actuellement, il prend en charge la conversion de HL7v2 vers FHIR.

## <a name="use-the-convert-data-endpoint"></a>Utiliser le point de terminaison $convert-data

`https://<<FHIR service base URL>>/$convert-data`

$convert-data prend une ressource [Parameter](http://hl7.org/fhir/parameters.html) dans le corps de la requête, comme décrit ci-dessous :

**Ressource de paramètre :**

| Nom du paramètre      | Description | Valeurs acceptées |
| ----------- | ----------- | ----------- |
| inputData      | Données à convertir. | Valeur valide du type de données de chaîne JSON|
| inputDataType   | Type de données de l’entrée. | ```HL7v2``` |
| templateCollectionReference | Référence à une collection de modèles. Il peut s’agir d’une référence à des **modèles par défaut** ou d’une image de modèle personnalisée inscrite auprès de l’API Azure pour FHIR. Pour en savoir plus sur la personnalisation des modèles, leur hébergement sur ACR et l’inscription à l’API Azure pour FHIR, consultez les sections ci-dessous.  | ```microsofthealth/fhirconverter:default```, \<RegistryServer\>/\<imageName\>@\<imageDigest\> |
| rootTemplate | Modèle racine à utiliser lors de la transformation des données. | ```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04``` |  

> [!WARNING]
> Les modèles par défaut vous aident à démarrer rapidement. Toutefois, ils sont susceptibles d’être mis à jour lors de la mise à niveau de l’API Azure pour FHIR. Pour que le comportement de conversion des données soit cohérent entre les différentes versions de l’API Azure pour FHIR, vous devez héberger votre propre copie des modèles sur un Azure Container Registry, les inscrire auprès de l’API Azure pour FHIR et les utiliser dans vos appels d’API, comme décrit plus loin.

**Exemple de requête :**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**Exemple de réponse :**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>Personnaliser des modèles

Vous pouvez utiliser l’[extension FHIR Converter](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) pour Visual Studio Code afin de personnaliser les modèles en fonction de vos besoins. L’extension fournit une expérience d’édition interactive et facilite le téléchargement de modèles et d’exemples de données publiés par Microsoft. Pour plus d’informations, reportez-vous à la documentation de l’extension.

## <a name="host-and-use-templates"></a>Héberger et utiliser des modèles

Il est fortement recommandé d’héberger votre propre copie des modèles sur ACR. Quatre étapes sont nécessaires pour héberger votre propre copie des modèles et les utiliser dans l’opération de $convert de données :

1. Envoyer (push) les modèles à votre instance Azure Container Registry
1. Activer Managed Identity sur votre instance d’API Azure pour FHIR
1. Fournir l’accès à l’ACR à l’identité managée API Azure pour FHIR
1. Inscrire les serveurs ACR dans l’API Azure pour FHIR
1. Éventuellement, configurez le pare-feu ACR pour un accès sécurisé.

### <a name="push-templates-to-azure-container-registry"></a>Envoyer (push) les modèles vers Azure Container Registry

Après avoir créé une instance d’ACR, vous pouvez utiliser la commande _FHIR Converter: Push Templates_ dans l’[extension FHIR Converter](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) pour envoyer les modèles personnalisés vers ACR. Vous pouvez également utiliser l’[outil Template Management CLI](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md) à cet effet.

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>Activer Managed Identity sur l’API Azure pour FHIR

Accédez à votre instance d’Azure API pour FHIR service dans le Portail Azure, puis sélectionnez le panneau **identité** .
Basculez l’état sur **Activé** afin d’activer Managed Identity dans API Azure pour FHIR.

![Activer une identité managée](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>Fournir l’accès à l’ACR à l’API Azure pour FHIR

1. Accédez au **panneau de contrôle d’accès (IAM)** .

1. Sélectionnez **Ajouter**, puis sélectionnez **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.

1. Affectez le rôle [AcrPull](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#acrpull) . 

   ![Page Ajouter une attribution de rôle](../../../includes/role-based-access-control/media/add-role-assignment-page.png) 

Pour plus d’informations sur l’affectation de rôles dans le Portail Azure, consultez [rôles intégrés Azure](../../role-based-access-control/role-assignments-portal.md).

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>Inscrire les serveurs ACR dans API Azure pour FHIR

Vous pouvez inscrire le serveur ACR à l’aide de l’Portail Azure ou à l’aide de l’interface CLI.

#### <a name="registering-the-acr-server-using-azure-portal"></a>Inscription du serveur ACR à l’aide de Portail Azure
Accédez au panneau **artefacts** sous **transformation des données** dans votre API Azure pour l’instance FHIR. La liste des serveurs ACR actuellement inscrits s’affiche. Sélectionnez **Ajouter**, puis sélectionnez votre serveur de Registre dans le menu déroulant. Vous devez sélectionner **Enregistrer** pour que l’inscription prenne effet. L’application de la modification et le redémarrage de votre instance peuvent prendre quelques minutes.

#### <a name="registering-the-acr-server-using-cli"></a>Inscription du serveur ACR à l’aide de l’interface CLI
Vous pouvez inscrire jusqu’à 20 serveurs ACR dans l’API Azure pour FHIR.

Installez l’interface CLI des API de santé à partir de Azure PowerShell si nécessaire :

```powershell
az extension add -n healthcareapis
```

Inscrivez les serveurs ACR auprès d’API Azure pour FHIR en suivant les exemples ci-dessous :

##### <a name="register-a-single-acr-server"></a>Inscrire un serveur ACR unique

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

##### <a name="register-multiple-acr-servers"></a>Inscrire plusieurs serveurs ACR

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```
### <a name="configure-acr-firewall"></a>Configurer le pare-feu ACR

Sélectionnez **mise en réseau** du compte de stockage Azure à partir du portail.

   :::image type="content" source="media/convert-data/networking-container-registry.png" alt-text="Registre de conteneurs.":::


Sélectionnez **Réseaux sélectionnés**. 

Dans la section **pare-feu** , spécifiez l’adresse IP dans la zone **plage d’adresses** . Ajoutez des plages d’adresses IP pour autoriser l’accès à partir d’Internet ou de vos réseaux locaux. 

Dans le tableau ci-dessous, vous trouverez l’adresse IP de la région Azure dans laquelle le service API Azure pour FHIR est approvisionné.

|**Région Azure**         |**Adresse IP publique** |
|:----------------------|:-------------------|
| Australie Est       | 20.53.44.80       |
| Centre du Canada       | 20.48.192.84      |
| USA Centre           | 52.182.208.31     |
| USA Est              | 20.62.128.148     |
| USA Est 2            | 20.49.102.228     |
| USA Est 2 (EUAP)       | 20.39.26.254      |
| Allemagne Nord        | 51.116.51.33      |
| Allemagne Centre-Ouest | 51.116.146.216    |
| Japon Est           | 20.191.160.26     |
| Centre de la Corée        | 20.41.69.51       |
| Centre-Nord des États-Unis     | 20.49.114.188     |
| Europe Nord         | 52.146.131.52     |
| Afrique du Sud Nord   | 102.133.220.197   |
| États-Unis - partie centrale méridionale     | 13.73.254.220     |
| Asie Sud-Est       | 23.98.108.42      |
| Suisse Nord    | 51.107.60.95      |
| Sud du Royaume-Uni             | 51.104.30.170     |
| Ouest du Royaume-Uni              | 51.137.164.94     |
| Centre-USA Ouest      | 52.150.156.44     |
| Europe Ouest          | 20.61.98.66       |
| USA Ouest 2            | 40.64.135.77      |


> [!NOTE]
> Les étapes ci-dessus sont similaires aux étapes de configuration décrites dans le document Comment exporter des données FHIR. Pour plus d’informations, consultez [exportation sécurisée vers le stockage Azure](https://docs.microsoft.com/azure/healthcare-apis/fhir/export-data#secure-export-to-azure-storage) .

### <a name="verify"></a>Vérification

Appelez l’API $convert-data en spécifiant votre référence de modèle dans le paramètre templateCollectionReference.

`<RegistryServer>/<imageName>@<imageDigest>`
