---
title: Vue d’ensemble du flux de modification DICOM-API Azure Healthcare
description: Dans cet article, vous allez découvrir les concepts du flux de modification DICOM.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: fc23c4cedbb6ee94c4f253cf53e0cdde27531fb4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122434043"
---
# <a name="change-feed-overview"></a>Vue d’ensemble du flux de modification

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Le flux de modification fournit des journaux de toutes les modifications qui se produisent dans le service DICOM. Le flux de modification fournit des journaux triés, garantis, immuables et en lecture seule de ces modifications. Le flux de modification offre la possibilité de parcourir l’historique du service DICOM et agit sur les créations et les suppressions dans le service.

Les applications clientes peuvent lire ces journaux à tout moment, soit en continu, soit en mode batch. Le flux de modification vous permet de créer des solutions efficaces et évolutives qui traitent les événements de modification qui se produisent dans votre service DICOM.

Vous pouvez traiter ces événements de modification de façon asynchrone, de manière incrémentielle ou complète. Un nombre quelconque d’applications clientes peuvent lire indépendamment le flux de modification, en parallèle, et à leur propre rythme.

Veillez à spécifier la version dans le cadre de l’URL lors de l’exécution des requêtes. Pour plus d’informations, consultez la documentation sur le contrôle [de version des API pour le service DICOM](api-versioning-dicom-service.md).

## <a name="api-design"></a>Conception d’API

L’API expose deux `GET` points de terminaison pour interagir avec le flux de modification. Vous trouverez [ci-dessous](#example-usage-flow)un flux classique pour la consommation du flux de modification.

Verbe | Routage              | Retours     | Description
:--- | :----------------- | :---------- | :---
GET  | /changefeed        | Tableau JSON  | [Lire le flux de modification](#read-change-feed)
GET  | /changefeed/latest | Objet JSON | [Lire l’entrée la plus récente dans le flux de modification](#get-latest-change-feed-item)

### <a name="object-model"></a>Modèle objet

Champ               | Type      | Description
:------------------ | :-------- | :---
Séquence            | int       | ID de séquence qui peut être utilisé pour la pagination (par décalage) ou l’ancrage
StudyInstanceUid    | string    | UID de l’instance d’étude
SeriesInstanceUid   | string    | UID de l’instance de série
SopInstanceUid      | string    | UID de l’instance SOP
Action              | string    | Action qui a été effectuée-soit `create` ou `delete`
Timestamp           | DATETIME  | Date et heure auxquelles l’action a été effectuée en heure UTC
State               | string    | [État actuel des métadonnées](#states)
Métadonnées            | object    | Éventuellement, les métadonnées DICOM actuelles si l’instance existe

#### <a name="states"></a>États

State    | Description
:------- | :---
actuels  | Cette instance est la version actuelle.
replaced | Cette instance a été remplacée par une nouvelle version.
deleted  | Cette instance a été supprimée et n’est plus disponible dans le service.

### <a name="read-change-feed"></a>Lire le flux de modification

**Itinéraire**:/changefeed ? offset = {int} &Limit = {int} &IncludeMetadata = {**true**| false}
```
[
    {
        "Sequence": 1,
        "StudyInstanceUid": "{uid}",
        "SeriesInstanceUid": "{uid}",
        "SopInstanceUid": "{uid}",
        "Action": "create|delete",
        "Timestamp": "2020-03-04T01:03:08.4834Z",
        "State": "current|replaced|deleted",
        "Metadata": {
            "actual": "metadata"
        }
    },
    {
        "Sequence": 2,
        "StudyInstanceUid": "{uid}",
        "SeriesInstanceUid": "{uid}",
        "SopInstanceUid": "{uid}",
        "Action": "create|delete",
        "Timestamp": "2020-03-05T07:13:16.4834Z",
        "State": "current|replaced|deleted",
        "Metadata": {
            "actual": "metadata"
        }
    }
    ...
]
```
#### <a name="parameters"></a>Paramètres

Nom            | Type | Description
:-------------- | :--- | :---
offset          | int  | Nombre d’enregistrements à ignorer avant les valeurs à retourner
limit           | int  | Nombre d’enregistrements à retourner (valeur par défaut : 10, min : 1, Max : 100)
IncludeMetadata | bool | Indique s’il faut inclure ou non les métadonnées (valeur par défaut : true)

### <a name="get-latest-change-feed-item"></a>Récupérer l’élément de flux de modification le plus récent

**Itinéraire**:/changefeed/latest ? IncludeMetadata = {**true**| false}

```
{
    "Sequence": 2,
    "StudyInstanceUid": "{uid}",
    "SeriesInstanceUid": "{uid}",
    "SopInstanceUid": "{uid}",
    "Action": "create|delete",
    "Timestamp": "2020-03-05T07:13:16.4834Z",
    "State": "current|replaced|deleted",
    "Metadata": {
        "actual": "metadata"
    }
}
```

#### <a name="parameters"></a>Paramètres

Nom            | Type | Description
:-------------- | :--- | :---
IncludeMetadata | bool | Indique s’il faut inclure ou non les métadonnées (valeur par défaut : true)

## <a name="usage"></a>Utilisation

### <a name="example-usage-flow"></a>Exemple de workflow d’utilisation

Voici le déroulement de l’utilisation d’un exemple d’application qui effectue d’autres traitements sur les instances au sein du service DICOM.

1. L’application qui veut surveiller le flux de modification démarre.
2. Il détermine s’il existe un état actuel avec lequel il doit commencer :
   * S’il a un État, il utilise le décalage (séquence) stocké.
   * S’il n’a jamais démarré et souhaite commencer depuis le début, il utilise `offset=0` .  
   * S’il souhaite uniquement effectuer un traitement à partir de maintenant, il interroge `/changefeed/latest` pour obtenir la dernière séquence.
3. Il interroge le flux de modification avec l’offset donné. `/changefeed?offset={offset}`
4. S’il existe des entrées :
   * Il effectue un traitement supplémentaire.  
   * Il met à jour son état actuel.  
   * Elle redémarre à l’étape 2.
5. S’il n’y a pas d’entrée, il est mis en veille pendant un laps de temps configuré et recommence à l’étape 2.

### <a name="other-potential-usage-patterns"></a>Autres modèles d’utilisation potentiels

La prise en charge des flux de modification convient parfaitement aux scénarios qui traitent les données en fonction d’objets qui ont été modifiés. Par exemple, il peut être utilisé pour :

* créez des pipelines d’application connectés comme les ML qui réagissent aux événements de modification ou planifiez les exécutions en fonction de l’instance créée ou supprimée.
* Extrayez les Insights et les métriques Business Analytics, en fonction des modifications apportées à vos objets.
* Interrogez le flux de modification pour créer une source d’événements pour les notifications push.

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons examiné la conception de l’API REST de flux de modification et les scénarios d’utilisation potentiels. Pour plus d’informations sur le flux de modification, consultez [extraire des modifications du flux de modification](pull-dicom-changes-from-change-feed.md).

## <a name="next-steps"></a>Étapes suivantes

>[!div class="nextstepaction"]
>[Vue d’ensemble du service DICOM](dicom-services-overview.md)

