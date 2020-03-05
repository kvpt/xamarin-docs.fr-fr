---
title: Xamarin. Android TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2017
ms.openlocfilehash: 2ffa544789e0d605a241c8e038c790650a7fc6a3
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292132"
---
# <a name="xamarinandroid-textureview"></a>Xamarin. Android TextureView

La classe `TextureView` est une vue qui utilise le rendu 2D avec accélération matérielle pour permettre l’affichage d’un flux de contenu vidéo ou OpenGL. Par exemple, la capture d’écran suivante montre les `TextureView` affichant un flux en direct à partir de l’appareil photo de l’appareil :

[![exemple de capture d’écran d’une image en direct à partir de l’appareil photo de l’appareil](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

Contrairement à la classe `SurfaceView`, qui peut également être utilisée pour afficher du contenu OpenGL ou vidéo, le TextureView n’est pas rendu dans une fenêtre distincte.
Par conséquent, `TextureView` est en mesure de prendre en charge les transformations d’affichage comme n’importe quelle autre vue. Par exemple, il est possible de faire pivoter une `TextureView` en définissant simplement sa propriété `Rotation`, sa transparence en définissant sa propriété `Alpha`, et ainsi de suite.

Par conséquent, avec le `TextureView` nous pouvons maintenant effectuer des opérations telles que afficher un flux en direct à partir de l’appareil photo et le transformer, comme illustré dans le code suivant :

```csharp
public class TextureViewActivity : Activity,
    TextureView.ISurfaceTextureListener
{
    Camera _camera;
    TextureView _textureView;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        _textureView = new TextureView (this);
        _textureView.SurfaceTextureListener = this;

        SetContentView (_textureView);
    }

    public void OnSurfaceTextureAvailable (
        Android.Graphics.SurfaceTexture surface,
        int width, int height)
    {
        _camera = Camera.Open ();
        var previewSize = _camera.GetParameters ().PreviewSize;
        _textureView.LayoutParameters =
            new FrameLayout.LayoutParams (previewSize.Width,
                previewSize.Height, (int)GravityFlags.Center);

        try {
            _camera.SetPreviewTexture (surface);
            _camera.StartPreview ();
        } catch (Java.IO.IOException ex) {
            Console.WriteLine (ex.Message);
        }

        // this is the sort of thing TextureView enables
        _textureView.Rotation = 45.0f;
        _textureView.Alpha = 0.5f;
    }
    …
}
```

Le code ci-dessus crée une instance `TextureView` dans la méthode `OnCreate` de l’activité et définit l’activité comme `SurfaceTextureListener`de `TextureView`. Pour être le `SurfaceTextureListener`, l’activité implémente l’interface `TextureView.ISurfaceTextureListener`. Le système appellera la méthode `OnSurfaceTextAvailable` lorsque la `SurfaceTexture` est prête à être utilisée. Dans cette méthode, nous prenons le `SurfaceTexture` qui est transmis et vous le définissez sur la texture de l’aperçu de la caméra. Nous sommes ensuite libres d’effectuer des opérations d’affichage normales, telles que la définition des `Rotation` et des `Alpha`, comme dans l’exemple ci-dessus. L’application résultante, exécutée sur un appareil, est illustrée ci-dessous :

[![exemple de l’application en cours d’exécution sur un appareil, affichant une image](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Pour utiliser le `TextureView`, l’accélération matérielle doit être activée, ce qui est le cas par défaut au niveau de l’API 14. En outre, étant donné que cet exemple utilise l’appareil photo, l’autorisation `android.permission.CAMERA` et la fonctionnalité `android.hardware.camera` doivent être définies dans **fichier AndroidManifest. xml**.

## <a name="related-links"></a>Liens connexes

- [TextureViewDemo (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)/)
