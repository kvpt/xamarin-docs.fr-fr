---
title: Xamarin. Android TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2017
ms.openlocfilehash: 2857033c5cd69e9696d2ce82feaf8212300da2c5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764809"
---
# <a name="xamarinandroid-textureview"></a>Xamarin. Android TextureView

La `TextureView` classe est une vue qui utilise le rendu 2D avec accélération matérielle pour permettre l’affichage d’un flux de contenu vidéo ou OpenGL. Par exemple, la capture d’écran suivante `TextureView` montre l’affichage d’un flux en direct à partir de l’appareil photo de l’appareil :

[![Exemple de capture d’écran d’une image en direct à partir de l’appareil photo de l’appareil](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

Contrairement à `SurfaceView` la classe, qui peut également être utilisée pour afficher du contenu OpenGL ou vidéo, le TextureView n’est pas rendu dans une fenêtre distincte.
Par conséquent `TextureView` , est en mesure de prendre en charge les transformations d’affichage comme n’importe quelle autre vue. Par exemple, la rotation `TextureView` d’un peut être effectuée en définissant simplement sa `Rotation` propriété, sa transparence `Alpha` en définissant sa propriété, et ainsi de suite.

Par conséquent, avec `TextureView` , nous pouvons maintenant effectuer des opérations telles que afficher un flux en direct à partir de l’appareil photo et le transformer, comme illustré dans le code suivant :

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

Le code ci-dessus `TextureView` crée une instance dans la `OnCreate` méthode de l’activité et `TextureView`définit `SurfaceTextureListener`l’activité comme étant. Pour être `SurfaceTextureListener`, l’activité implémente l' `TextureView.ISurfaceTextureListener` interface. Le système appellera la `OnSurfaceTextAvailable` méthode lorsque le `SurfaceTexture` est prêt à être utilisé. Dans cette méthode, nous prenons le `SurfaceTexture` qui est passé et nous le définissons sur la texture de l’aperçu de la caméra. Nous sommes ensuite libres d’effectuer des opérations d’affichage normales, telles que la `Rotation` définition de et `Alpha`, comme dans l’exemple ci-dessus. L’application résultante, exécutée sur un appareil, est illustrée ci-dessous :

[![Exemple d’application en cours d’exécution sur un appareil, affichant une image](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Pour utiliser, `TextureView`l’accélération matérielle doit être activée, ce qui correspondra par défaut au niveau d’API 14. En outre, étant donné que cet exemple utilise l’appareil `android.permission.CAMERA` photo, l' `android.hardware.camera` autorisation et la fonctionnalité doivent être définies dans le **fichier AndroidManifest. xml**.

## <a name="related-links"></a>Liens associés

- [TextureViewDemo (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)
- [Présentation du sandwich glacé](http://www.android.com/about/ice-cream-sandwich/)
- [Plateforme Android 4,0](https://developer.android.com/sdk/android-4.0.html)
