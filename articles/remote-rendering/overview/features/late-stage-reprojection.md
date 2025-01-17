---
title: Reprojection en phase tardive
description: Informations sur la reprojection en phase tardive et sur son utilisation.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 377e35b3107195841f49ecc8b1e5fb18fe38ed87
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658535"
---
# <a name="late-stage-reprojection"></a>Reprojection en phase tardive

La *reprojection en phase tardive* (LSR) est une fonctionnalité matérielle qui permet de stabiliser les hologrammes lorsque l’utilisateur est en mouvement.

Les modèles statiques sont censés conserver visuellement leur position lorsque vous les contournez. S’ils semblent instables, ce comportement peut indiquer des problèmes LSR. Gardez à l’esprit que des transformations dynamiques supplémentaires, comme des animations ou des vues d’explosion, peuvent masquer ce comportement.

Vous pouvez choisir entre deux modes LSR différents : **Planar LSR** ou **Depth LSR**. Celui qui est actif est déterminé par le fait que l’application cliente envoie ou non un tampon de profondeur.

Les deux modes LSR améliorent la stabilité des hologrammes, bien qu’ils aient chacun leurs propres limitations. Commencez par essayer Depth LSR, car il permet assurément d’obtenir de meilleurs résultats dans la plupart des cas.

## <a name="choose-lsr-mode-in-unity"></a>Choisir le mode LSR dans Unity

Dans l’éditeur Unity, accédez à *:::no-loc text="File > Build Settings":::* . Sélectionnez *:::no-loc text="Player Settings":::* en bas à gauche, puis vérifiez sous *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* si la case **:::no-loc text="Enable Depth Buffer Sharing":::** est cochée :

![Indicateur d’activation du partage du tampon de profondeur](./media/unity-depth-buffer-sharing-enabled.png)

Si c’est le cas, votre application utilisera Depth LSR, sinon, elle utilisera Planar LSR.

Quand vous utilisez OpenXR, le tampon de profondeur doit toujours être envoyé. Le paramètre se trouve dans *:::no-loc text="XR Plug-in Management > OpenXR":::* . Le mode de reprojection peut ensuite être modifié via une extension dans le plug-in OpenXR :

```cs
using Microsoft.MixedReality.OpenXR;

public class OverrideReprojection : MonoBehaviour
{
    void OnEnable()
    {
        RenderPipelineManager.endCameraRendering += RenderPipelineManager_endCameraRendering;
    }
    void OnDisable()
    {
        RenderPipelineManager.endCameraRendering -= RenderPipelineManager_endCameraRendering;
    }

    // When using the Universal Render Pipeline, OnPostRender has to be called manually.
    private void RenderPipelineManager_endCameraRendering(ScriptableRenderContext context, Camera camera)
    {
        OnPostRender();
    }

    // Called directly when using Unity's legacy renderer.
    private void OnPostRender()
    {
        ReprojectionSettings reprojectionSettings = default;
        reprojectionSettings.ReprojectionMode = ReprojectionMode.PlanarManual; // Or your favorite reprojection mode.
        
        // In case of PlanarManual you also need to provide a focus point here.
        reprojectionSettings.ReprojectionPlaneOverridePosition = ...;
        reprojectionSettings.ReprojectionPlaneOverrideNormal = ...;
        reprojectionSettings.ReprojectionPlaneOverrideVelocity = ...;

        foreach (ViewConfiguration viewConfiguration in ViewConfiguration.EnabledViewConfigurations)
        {
            if (viewConfiguration.IsActive && viewConfiguration.SupportedReprojectionModes.Contains(reprojectionSettings.ReprojectionMode))
            {
                viewConfiguration.SetReprojectionSettings(reprojectionSettings);
            }
        }
    }
}
```

## <a name="depth-lsr"></a>Depth LSR

Pour que Depth LSR fonctionne, l’application cliente doit fournir un tampon de profondeur valide qui contient toutes les géométries appropriées à prendre en compte pendant la reprojection en phase tardive.

Depth LSR tente de stabiliser la trame vidéo en fonction du contenu du tampon de profondeur fourni. Ainsi, le contenu qui n’y a pas été rendu, tel que les objets transparents, ne peut pas être ajusté par LSR mais présenter une instabilité et des artefacts de reprojection.

Pour atténuer l’instabilité de la reprojection pour les objets transparents, vous pouvez forcer l’écriture dans le tampon de profondeur. Consultez l’indicateur de matériau *TransparencyWritesDepth* pour les matériaux [Couleur](color-materials.md) et [PBR](pbr-materials.md). Notez toutefois que la qualité visuelle de l’interaction entre l’objet transparent et l’objet opaque peut souffrir de cet indicateur.

## <a name="planar-lsr"></a>Planar LSR

Planar LSR ne dispose pas d’informations de profondeur par pixel, à l’instar de Depth LSR. Au lieu de cela, il reprojette tout le contenu en fonction d’un plan que vous devez fournir à chaque trame.

Planar LSR reprojette de façon optimale les objets qui se trouvent le plus près du plan fourni. Plus un objet est éloigné, plus son apparence est instable. Si Depth LSR convient mieux pour la reprojection d’objets à différentes profondeurs, Planar LSR obtient de meilleurs résultats dans l’alignement de contenu par rapport à un plan.

### <a name="configure-planar-lsr-in-unity"></a>Configurer Planar LSR dans Unity

Les paramètres de plan sont dérivés de ce qu’on appelle un *point de focus*. Lors de l’utilisation de WMR, le point de focus doit être défini pour chaque image via `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`. Pour plus d’informations, consultez [API Unity Focus Point](/windows/mixed-reality/focus-point-in-unity). Pour OpenXR, le point de focus doit être défini via les `ReprojectionSettings` indiqués dans la section précédente.
Si vous ne définissez pas de zone de focus, une solution de secours sera choisie pour vous. Pour autant, ce point de secours automatique aboutit souvent à des résultats inférieurs.

Vous pouvez calculer vous-même la zone de focus, bien qu’il puisse paraître judicieux de la baser sur celle qui est calculée par l’hôte Remote Rendering. Appelez `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` pour l’obtenir.

En général, le client et l’hôte affichent chacun le contenu qui n’est pas pris en charge par l’autre, par exemple des éléments d’interface utilisateur sur le client. Ainsi, il peut s’avérer opportun de combiner la zone de focus à distance avec un point calculé localement.

Les zones de focus calculées dans deux images successives peuvent être assez différentes. Leur utilisation telle quelle peut donner aux hologrammes une apparence sautillante. Pour éviter ce comportement, il est conseillé d’interpoler entre la zone de focus précédente et la zone actuelle.

## <a name="next-steps"></a>Étapes suivantes

* [Requêtes de performances côté serveur](performance-queries.md)