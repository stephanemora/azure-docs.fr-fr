---
title: Data Flow dans IoT Connector-API Azure Healthcare
description: Comprendre le workflow des données du connecteur IoT. Le connecteur IoT ingère, normalise, regroupe, transforme et conserve les données IoMT dans le service FHIR.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: jasteppe
ms.openlocfilehash: 41ad8d284636e431b74c43006bb544f737ce1f47
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005500"
---
# <a name="iot-connector-data-flow"></a>Workflow de données du connecteur IoT

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Cet article fournit une vue d’ensemble du connecteur IoT. Vous en apprendrez davantage sur les différentes étapes de traitement des données dans IoT Connector qui transforment les données des appareils en ressources d' [observation](https://www.hl7.org/fhir/observation.html) FHIR&#174;) rapides.

Voici les différentes étapes que les données passent une fois reçues par le connecteur IoT.

## <a name="ingest"></a>Ingérer
La réception est la première étape où les données de l’appareil sont reçues dans le connecteur IoT. Le point de terminaison d’ingestion pour les données d’appareils est hébergé sur un [Azure Event Hub](../../event-hubs/index.yml). La plateforme Azure Event Hub prend en charge une échelle et un débit élevés, avec la possibilité de recevoir et de traiter des millions de messages par seconde. Il permet également au connecteur IoT de consommer les messages de manière asynchrone, ce qui évite d’avoir à attendre que les données des appareils soient traitées.

> [!NOTE]
> JSON est le seul format actuellement pris en charge pour les données d’appareils.

## <a name="normalize"></a>Normaliser
Normalize est l’étape suivante dans laquelle les données de l’appareil sont récupérées à partir du hub d’événements Azure ci-dessus et traitées à l’aide du mappage de l’appareil. Ce processus de mappage entraîne une transformation des données d’appareils en un schéma normalisé. 

Le processus de normalisation non seulement simplifie le traitement des données à des étapes ultérieures, mais aussi permet de projeter un message d’entrée vers plusieurs messages normalisés. Par exemple, un appareil pourrait envoyer plusieurs signes vitaux pour la température du corps, la fréquence cardiaque, la pression artérielle et la fréquence respiratoire dans un message unique. Ce message d’entrée crée quatre ressources FHIR distinctes. Chaque ressource représente un signe vital différent, avec le message d’entrée projeté dans quatre messages normalisés différents.

## <a name="group"></a>Grouper
Le groupement est l’étape suivante dans laquelle les messages normalisés disponibles de l’étape précédente sont groupés sur la base de trois paramètres différents : identité de l’appareil, type de mesure et période.

Lee groupements par identité de l’appareil et types de mesure permettent d’utiliser le type de mesure [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData). Ce type fournit un moyen concis de représenter une série de mesures basées sur le temps d’un appareil dans FHIR. Et la période de temps contrôle la latence à laquelle les ressources d’observation générées par le connecteur IoT sont écrites dans le service FHIR.

> [!NOTE]
> La valeur de la période est définie par défaut sur 15 minutes et ne peut pas être configurée pour la préversion.

## <a name="transform"></a>Transformer
Dans l’étape de transformation, les messages normalisés groupés sont traités par le biais de modèles de mappage de destination FHIR. Les messages correspondant à un type de modèle sont transformés en ressources d’observation basées sur FHIR, comme spécifié via le mappage.

À ce stade, la ressource de l' [appareil](https://www.hl7.org/fhir/device.html) , ainsi que la ressource du [patient](https://www.hl7.org/fhir/patient.html) qui lui est associée, sont également récupérées à partir du service FHIR à l’aide de l’identificateur d’appareil présent dans le message. Ces ressources sont ajoutées en tant que référence à la ressource Observation en cours de création.

> [!NOTE]
> Toutes les recherches d’identité sont mises en cache une fois résolues pour réduire la charge sur le service FHIR. Si vous prévoyez de réutiliser des appareils avec plusieurs patients, il est recommandé de créer une ressource appareil virtuel spécifique du patient et d’envoyer l’identificateur d’appareil virtuel dans la charge utile du message. L’appareil virtuel peut être lié à la ressource appareil réelle en tant que parent.

S’il n’existe aucune ressource d’appareil pour un identificateur d’appareil donné dans le service FHIR, le résultat dépend de la valeur de `Resolution Type` définie au moment de la création. Lorsque la valeur est définie sur `Lookup`, le message spécifique est ignoré et le pipeline continue de traiter d’autres messages entrants. Si la valeur `Create` est, le connecteur IOT crée un appareil nu et des ressources pour les patients sur le service FHIR.  

## <a name="persist"></a>Conserver
Une fois la ressource FHIR d’observation générée à l’étape de transformation, la ressource est enregistrée dans le service FHIR. Si la ressource FHIR est nouvelle, elle sera créée sur le service FHIR. Si la ressource FHIR existe déjà, elle est mise à jour.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer des mappages de destination appareil et FHIR.

> [!div class="nextstepaction"]
> [Mappage d’appareil](how-to-use-device-mapping-iot.md)

> [!div class="nextstepaction"]
> [Mappage de destination FHIR](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.