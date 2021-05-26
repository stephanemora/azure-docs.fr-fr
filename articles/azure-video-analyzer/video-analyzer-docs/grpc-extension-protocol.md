---
title: Protocole d’extension gRPC – Azure
description: Azure Video Analyzer vous permet d’étendre ses fonctionnalités de traitement via un nœud d’extension de pipeline. Le processeur d’extension gRPC permet des scénarios d’extensibilité en utilisant le protocole structuré hautement performant basé sur gRPC.
ms.topic: reference
ms.date: 05/15/2021
ms.openlocfilehash: aaa1030f2c751514568c96142268d41c2eba4df6
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386259"
---
# <a name="use-the-grpc-extension-protocol"></a>Utiliser le protocole d’extension gRPC 

Azure Video Analyzer vous permet d’étendre ses fonctionnalités de traitement de pipeline via un [nœud d’extension de pipeline](pipeline-extension.md). Le processeur d’extension gRPC permet des scénarios d’extensibilité en utilisant le [protocole structuré hautement performant basé sur gRPC](pipeline-extension.md#grpc-extension-processor).

Cet article explique comment utiliser le protocole d’extension gRPC pour échanger des messages entre le module Video Analyzer et votre serveur gRPC qui traite ces messages et retourne les résultats. gRPC est un framework RPC moderne, open source et extrêmement performant qui s’exécute dans n’importe quel environnement et prend en charge les communications multiplateforme et interlangage. Le service de transport gRPC utilise le streaming bidirectionnel HTTP/2 entre :

* le client gRPC (module Video Analyzer) et
* le serveur gRPC (votre extension personnalisée).

Une session gRPC est une connexion unique du client gRPC au serveur gRPC via le port TCP/TLS.
Dans une session unique : le client envoie un descripteur de flux multimédia, suivi d’images vidéo, au serveur sous la forme d’un message [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) via la session de flux gRPC. Le serveur valide le descripteur de flux, analyse l’image vidéo et retourne les résultats d’inférence sous la forme d’un message protobuf.

Il est fortement recommandé de retourner les réponses à l’aide de documents JSON valides, suivant le schéma préétabli défini conformément au [modèle d’objet de schéma de métadonnées d’inférence](inference-metadata-schema.md). Cela permet de mieux veiller à l’interopérabilité avec d’autres composants et scénarios, tels que l’enregistrement et la lecture de vidéos avec des métadonnées d’inférence.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/grpc-extension-protocol/ava-module.png" alt-text="Module Azure Video Analyzer" lightbox="./media/grpc-extension-protocol/ava-module.png":::

## <a name="implementing-grpc-protocol"></a>Implémentation du protocole gRPC

### <a name="creating-a-grpc-connection"></a>Création d’une connexion gRPC

L’extension personnalisée doit implémenter le service gRPC suivant :

```
service MediaGraphExtension
    {
        rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
    }
```

Quand celui-ci est appelé, il ouvre un flux bidirectionnel pour les messages échangés entre l’extension gRPC et le pipeline en direct de Video Analyzer. Le premier message envoyé dans ce flux par chaque tiers contient un MediaStreamDescriptor, qui définit les informations qui seront envoyées dans les messages MediaSample suivants.

Par exemple, l’extension peut envoyer le message (exprimé ici en JSON) pour indiquer qu’elle enverra des images 416x416 encodées en rgb24, incorporées aux messages gRPC, à l’extension personnalisée.

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": 
    {
        "graph_identifier": 
        {
            "media_services_arm_id": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{video-analyzer-account-name}",
            "graph_instance_name": "{live-pipeline-name}",
            "graph_node_name": "{grpc-extension-node-name}"
        },
        "media_descriptor": 
        {
            "timescale": 90000,
            "video_frame_sample_format": 
            {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": 
                {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

L’extension personnalisée, en réponse, envoie le message suivant pour indiquer qu’elle retourne uniquement des inférences.

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": 
    {
        "extension_identifier": "customExtensionName"    
    }
}
```

Maintenant que les deux côtés ont échangé des descripteurs multimédias, Video Analyzer commence à transmettre des images au serveur gRPC.

> [!NOTE]
> Vous pouvez implémenter le serveur gRPC à l’aide du langage de programmation de votre choix.

### <a name="sequence-numbers"></a>Numéros de séquence

Le nœud d’extension gRPC et l’extension personnalisée conservent un ensemble distinct de numéros de séquence, qui sont affectés à leurs messages. Les numéros de séquence doivent augmenter de façon monotone à partir de 1. ack_sequence_number peut être ignoré si aucun message ne fait l’objet d’un accusé de réception, ce qui peut se produire lors de l’envoi du premier message.

Une demande doit être reconnue une fois que le message correspondant a été entièrement traité. Dans le cas d’un transfert de mémoire partagée, cette reconnaissance indique que l’expéditeur peut libérer la mémoire partagée et que le destinataire ne pourra plus y accéder. L’expéditeur doit conserver toute mémoire partagée jusqu’à ce qu’elle soit reconnue.

### <a name="reading-embedded-content"></a>Lecture du contenu incorporé

Le contenu incorporé peut être lu directement à partir du message MediaSample via le champ content_byets. Les données sont encodées en fonction du MediaDescriptor.

### <a name="reading-content-from-shared-memory"></a>Lecture du contenu à partir de la mémoire partagée

Lors du transfert de contenu via la mémoire partagée, l’expéditeur inclut SharedMemoryBufferTransferProperties dans son MediaStreamDescriptor lors de l’établissement d’une première session. Cela peut se présenter comme suit (exprimé en JSON) :

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

Le récepteur ouvre alors le fichier /dev/shm/inference_client_share_memory_2146989006636459346. L’expéditeur envoie un message MediaSample qui contient une ContentReference faisant référence à un emplacement spécifique dans ce fichier.

```
{
    "timestamp": 143598615750000,
    "content_reference": 
    {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

Le récepteur lit ensuite les données à partir de cet emplacement dans le fichier.

Pour que le module Edge Video Analyzer communique au sujet de la mémoire partagée, le mode IPC du conteneur doit être configuré correctement. Cela peut être fait de nombreuses façons, mais voici quelques configurations recommandées :

* Lors de la communication avec un moteur d’inférence gRPC en cours d’exécution sur l’appareil hôte, le mode IPC doit être défini pour héberger.
* Lors de la communication avec un serveur gRPC en cours d’exécution dans un autre module IoT Edge, le mode IPC doit être défini sur `shareable` pour le module Video Analyzer, et `container:avaedge` pour l’extension personnalisée, si `avaedge` est le nom du module Video Analyzer.

Voici à quoi cela peut ressembler dans le jumeau d’appareil à l’aide de la première option ci-dessus.

```
"avaedge": 
{
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": 
  {
    "image": "mcr.microsoft.com/media/video-analyzer:1",
    "createOptions": 
      "HostConfig": 
      {
        "IpcMode": "shareable"
      }
  }
}
```

Pour plus d’informations sur les modes IPC, consultez [Paramètres IPC (--ipc)](https://docs.docker.com/engine/reference/run/#ipc-settings---ipc).

## <a name="video-analyzer-grpc-extension-contract-definitions"></a>Définitions de contrat d’extension gRPC pour Video Analyzer

Cette section définit le contrat gRPC qui définit le flux de données.

### <a name="protocol-messages"></a>Messages de protocole

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/grpc-extension-protocol/contract-definitions.png" alt-text="Messages du protocole Video Analyzer"  lightbox="./media/grpc-extension-protocol/contract-definitions.png":::

### <a name="client-authentication"></a>Authentification du client

Les implémenteurs d’extensions personnalisées peuvent valider l’authenticité des connexions gRPC entrantes pour s’assurer qu’elles proviennent du nœud d’extension gRPC. Le nœud fournira une entrée dans les en-têtes de demande à utiliser pour la validation.

Des informations d’identification nom d’utilisateur/mot de passe peuvent être utilisées pour ce faire. Lorsque vous créez un nœud d’extension gRPC, les informations d’identification sont fournies comme suit :

```
{
  "@type": " #Microsoft.VideoAnalyzer.GrpcExtension ",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": " #Microsoft.VideoAnalyzer.UnsecuredEndpoint ",
    "url": "tcp://customExtension:8081",
    "credentials": 
    {
      "@type": "#Microsoft. VideoAnalyzer.UsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

Lorsque la demande gRPC est envoyée, l’en-tête suivant est inclus dans les métadonnées de la demande, imitant l’authentification HTTP de base.

```
x-ms-authentication: Basic (Base64 Encoded username:password)
```

## <a name="configuring-inference-server-for-each-live-pipeline-over-grpc-extension"></a>Configuration du serveur d’inférence pour chaque pipeline en direct sur une extension gRPC

Quand vous configurez votre serveur d’inférence, vous n’êtes pas obligé d’exposer un nœud pour chaque modèle IA qui est empaqueté dans le serveur d’inférence. Au lieu de cela, pour un pipeline en direct, vous pouvez utiliser la propriété `extensionConfiguration` du nœud **GrpcExtension**, et définir la façon de sélectionner le ou les modèles IA. Pendant l’exécution, Video Analyzer transmet cette chaîne au serveur d’inférence qui peut l’utiliser pour appeler le modèle IA souhaité. Cette `extensionConfiguration property` est une propriété facultative spécifique de votre implémentation du serveur gRPC. Vous pouvez l’utiliser comme suit :

```
{
  "@type": "#Microsoft.VideoAnalyzer.GrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
    "url": "${grpcExtensionAddress}",
    "credentials": 
    {
      "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
      "username": "${grpcExtensionUserName}",
      "password": "${grpcExtensionPassword}"
    }
  },
    // Optional server configuration string. This is server specific 
  "extensionConfiguration": "{Optional extension specific string}",
  "dataTransfer": 
  {
    "mode": "sharedMemory",
    "SharedMemorySizeMiB": "75"
  }
    //Other fields omitted
}
```

## <a name="using-grpc-over-tls"></a>Utilisation de gRPC via TLS

Une connexion gRPC utilisée pour l’inférence peut être sécurisée via TLS. C’est utile quand la sécurité du réseau entre Video Analyzer et le moteur d’inférence ne peut pas être garantie. TLS chiffre tout le contenu incorporé dans les messages gRPC, ce qui entraîne un temps processeur supplémentaire lors de la transmission des images à un débit élevé.

Les options de vérification `IgnoreHostname` et `IgnoreSignature` ne sont pas prises en charge par gRPC. Par conséquent, le certificat de serveur que le moteur d’inférence présente doit contenir un nom commun (CN) correspondant exactement à l’adresse IP/nom d’hôte dans l’URL du point de terminaison du nœud d’extension gRPC.

## <a name="next-steps"></a>Étapes suivantes

Découvrez le [schéma des métadonnées d’inférence](inference-metadata-schema.md)
