---
title: Appareil photo
description: Décrit les paramètres de caméra et les cas d’usage
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594127"
---
# <a name="camera"></a>Appareil photo

Pour permettre une composition sans interruption du contenu rendu localement et à distance, plusieurs propriétés de caméra doivent rester synchronisées entre le serveur et le client. C’est tout particulièrement le cas des propriétés de transformation et de projection de caméra. Par exemple, le contenu rendu localement doit utiliser les propriétés de transformation et de projection de caméra avec lesquelles les dernières images distantes ont été rendues.

![Caméra locale et distante](./media/camera.png)

Dans l’image ci-dessus, la caméra locale a été déplacée par rapport à l’image distante envoyée par le serveur. De ce fait, le contenu local doit être rendu avec la même perspective que l’image distante ; l’image obtenue est enfin reprojetée dans l’espace de la caméra locale, ce qui est expliqué en détail dans [Reprojection en phase tardive](late-stage-reprojection.md).

Le délai observé entre le rendu distant et le rendu local signifie que toute modification de ces paramètres est appliquée avec un décalage. Par conséquent, une nouvelle valeur ne peut pas être utilisée immédiatement dans la même image.

Les distances des plans rapproché et éloigné peuvent être définies dans les paramètres de caméra. Sur HoloLens 2, les données de transformation et de projection restantes sont définies par le matériel. Lors de l’utilisation de la [simulation de bureau](../../concepts/graphics-bindings.md), elles sont définies via l’entrée de sa fonction `Update`.

Les valeurs modifiées peuvent être utilisées localement dès l’arrivée de la première image distante qui a été rendue avec ces valeurs. Sur HoloLens 2, les données à utiliser pour le rendu sont fournies par *HolographicFrame* comme dans n’importe quelle autre application holographique. Dans la [simulation de bureau](../../concepts/graphics-bindings.md), elles sont récupérées via la sortie de la fonction `Update`.

## <a name="camera-settings"></a>Paramètres de caméra

Les propriétés suivantes peuvent être modifiées dans les paramètres de caméra :

**Plan rapproché et éloigné :**

Pour empêcher la définition de plages non valides, les propriétés **NearPlane** et **FarPlane** sont en lecture seule et une fonction distincte **SetNearAndFarPlane** permet de modifier la plage. Ces données sont envoyées au serveur à la fin de l’image. Lors de la définition de ces valeurs, **NearPlane** doit être plus petite que **FarPlane**. Sinon, une erreur se produit.

> [!IMPORTANT]
> Dans Unity, cela est gérée automatiquement quand les plans rapproché et éloigné de la caméra principale sont modifiés.

**EnableDepth** :

Il est parfois utile de désactiver l’écriture de la mémoire tampon de profondeur de l’image distante à des fins de débogage. La désactivation de la profondeur fait également arrêter l’envoi de données de profondeur par la serveur, ce qui réduit la bande passante.

> [!TIP]
> Dans Unity, le composant de débogage fourni appelé **EnableDepthComponent** permet d’activer ou de désactiver cette fonctionnalité dans l’interface utilisateur de l’éditeur.

**InverseDepth** :

> [!NOTE]
> Ce paramètre est important uniquement si `EnableDepth` est défini sur `true`. Dans le cas contraire, ce paramètre n’a aucun impact.

Les mémoires tampons de profondeurs enregistrent normalement les valeurs Z dans une plage à virgule flottante de [0;1], 0 indiquant la profondeur du plan proche et 1 la profondeur du plan lointain. Il est également possible d’inverser cette plage et d’enregistrer les valeurs de profondeur dans la plage [1;0] ; autrement dit, la profondeur du plan proche devient 1, tandis que la profondeur du plan lointain devient 0. En règle générale, cette dernière méthode améliore la distribution de la précision à virgule flottante sur la plage Z non linéaire.

> [!WARNING]
> Une approche courante consiste à inverser les valeurs de plan proche et de plan lointain sur les objets de caméra. Elle échoue pour Azure Remote Rendering avec une erreur si vous l’essayez sur `CameraSettings`.

L’API Azure Remote Rendering doit connaître la convention de mémoire tampon de profondeur de votre convertisseur local pour composer correctement la profondeur à distance dans la mémoire tampon de profondeur locale. Si la plage de mémoire tampon de profondeur est [0;1], laissez cet indicateur défini sur `false`. Si vous utilisez une mémoire tampon de profondeur inversée avec une plage [1;0], affectez à l’indicateur `InverseDepth` la valeur `true`.

> [!NOTE]
> Pour Unity, le paramètre correct étant déjà appliqué par `RenderingConnection`, aucune intervention manuelle n’est nécessaire.

La modification des paramètres de caméra peut être effectuée comme suit :

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>Documentation de l’API

* [CameraSettings, C#](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [CameraSettings, C++](/cpp/api/remote-rendering/camerasettings)
* [GraphicsBindingSimD3d11.Update, fonction C#](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [GraphicsBindingSimD3d11::Update, fonction C++](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Étapes suivantes

* [Liaison graphique](../../concepts/graphics-bindings.md)
* [Reprojection en phase tardive](late-stage-reprojection.md)