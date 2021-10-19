---
title: Utilisation d’API standard DICOMweb avec le service DICOM des API de santé Azure
description: Ce didacticiel explique comment utiliser les API standard DICOMweb avec le service DICOM.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: aersoy
ms.openlocfilehash: b219c4d624c5823804371bba156911aaa94eeccb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631984"
---
# <a name="using-dicomwebtradestandard-apis-with-dicom-services"></a>Utilisation &trade; des API standard DICOMweb avec les services DICOM

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Ce didacticiel fournit une vue d’ensemble de l’utilisation des &trade; API standard DICOMweb avec le service DICOM.

Le service DICOM prend en charge un sous-ensemble de la &trade; norme DICOMweb qui comprend les éléments suivants :

* Magasin (ARRIM-RS)
* Récupérer (WADO-RS)
* Recherche (QIDO-RS)

En outre, les API non standard suivantes sont prises en charge :

* Supprimer
* Modifier le flux

Pour en savoir plus sur la prise en charge des API standard Web DICOM, consultez le document de référence sur les [instructions de conformité DICOM](dicom-services-conformance-statement.md) .

## <a name="prerequisites"></a>Prérequis

Pour utiliser les &trade; API standard DICOMweb, vous devez disposer d’une instance des services DICOM déployée. Si vous n’avez pas déjà déployé une instance du service DICOM, consultez [déployer le service DICOM à l’aide de l’portail Azure](deploy-dicom-services-in-azure.md).

Une fois le déploiement terminé, vous pouvez utiliser la Portail Azure pour accéder au service DICOM nouvellement créé afin d’afficher les détails, notamment votre URL de service. L’URL du service pour accéder à votre service DICOM sera : ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` . Veillez à spécifier la version dans le cadre de l’URL lors de l’exécution des requêtes. Pour plus d’informations, consultez la documentation sur le contrôle [de version des API pour le service DICOM](api-versioning-dicom-service.md).

## <a name="overview-of-various-methods-to-use-with-dicom-service"></a>Vue d’ensemble des différentes méthodes à utiliser avec le service DICOM

Étant donné que le service DICOM est exposé en tant qu’API REST, vous pouvez y accéder à l’aide de n’importe quel langage de développement moderne. Pour obtenir des informations indépendantes du langage sur l’utilisation du service, consultez [instruction de conformité DICOM](dicom-services-conformance-statement.md).

Pour voir des exemples spécifiques au langage, reportez-vous aux exemples ci-dessous. Vous pouvez consulter des exemples de collection de publications dans plusieurs langages, notamment :

* Go 
* Java 
* JavaScript 
* C# 
* PHP 
* C 
* NodeJS
* Objective-C
* OCaml
* PowerShell
* Python
* Ruby 
* Prompt.

### <a name="c"></a>C#

Reportez-vous au didacticiel [utilisation des API DICOMweb™ standard avec c#](dicomweb-standard-apis-c-sharp.md) pour apprendre à utiliser c# avec le service DICOM.

### <a name="curl"></a>cURL

l’outil bouclé est un outil de ligne de commande courant permettant d’appeler des points de terminaison Web disponibles pour presque n’importe quel système d’exploitation. [Téléchargez la boucle](https://curl.haxx.se/download.html) pour commencer.

Pour savoir comment utiliser la boucle avec le service DICOM, consultez [utilisation de DICOMWeb™ API standard avec le didacticiel de boucle](dicomweb-standard-apis-curl.md) .

### <a name="python"></a>Python

Reportez-vous au didacticiel [utilisation des API DICOMWeb™ standard avec Python](dicomweb-standard-apis-python.md) pour apprendre à utiliser Python avec le service DICOM.

### <a name="postman"></a>postman

Poster est un excellent outil pour la conception, la création et le test des API REST. [Téléchargez le billet](https://www.postman.com/downloads/) pour commencer. Vous pouvez apprendre à utiliser efficacement le billet sur le [site de formation](https://learning.postman.com/).

L’un des inconvénients majeurs des publications et de la &trade; norme DICOMweb est que la publication ne peut prendre en charge que le téléchargement de fichiers DICOM à l’aide de la charge utile unique définie dans la norme DICOM. Cela est dû au fait que la publication ne peut pas prendre en charge les séparateurs personnalisés dans une requête de publication à parties multiples/associée. Pour plus d’informations, consultez la [publication en plusieurs parties qui ne fonctionne pas pour moi # 576](https://github.com/postmanlabs/postman-app-support/issues/576). Ainsi, tous les exemples de la collection de publications pour le téléchargement de documents DICOM à l’aide d’une demande en plusieurs parties sont préfixés par [ne fonctionne pas-voir la description]. Les exemples de chargement à l’aide d’une demande de composant unique sont inclus dans la collection et portent le préfixe « Store-Single-instance ».

Pour utiliser la collection de publications, vous devez télécharger le regroupement localement et importer le regroupement par le biais du billet. Pour accéder à cette collection, consultez [exemples de collections de publications](https://github.com/microsoft/dicom-server/blob/main/docs/resources/Conformance-as-Postman.postman_collection.json).

## <a name="summary"></a>Récapitulatif

Ce didacticiel vous a fourni une vue d’ensemble des API prises en charge par le service DICOM. Commencez à utiliser ces API avec les outils suivants :

- [Utilisation de DICOMweb™ API standard avec C #](dicomweb-standard-apis-c-sharp.md)
- [Utilisation de DICOMWeb™ les API standard avec l’accolade](dicomweb-standard-apis-curl.md)
- [Utilisation de DICOMWeb™ API standard avec Python](dicomweb-standard-apis-python.md)
- [Utiliser les API standard DICOM Web avec l’exemple de collection de publications](https://github.com/microsoft/dicom-server/blob/main/docs/resources/Conformance-as-Postman.postman_collection.json)

### <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez la rubrique

>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)
