---
title: Graphisme et animation
description: Android fournit une infrastructure très riche et diversifiée pour la prise en charge des graphiques 2D et des animations. Cette rubrique présente ces infrastructures et explique comment créer des graphiques et des animations personnalisés à utiliser dans une application Xamarin. Android.
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: eeee9b7d694d9380c653fb87c24171bcaf79389d
ms.sourcegitcommit: 9ab907e053c57fc96419149f83187bc3e8983a6b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75655285"
---
# <a name="android-graphics-and-animation"></a>Graphiques et animations Android

_Android fournit une infrastructure très riche et diversifiée pour la prise en charge des graphiques 2D et des animations. Cette rubrique présente ces infrastructures et explique comment créer des graphiques et des animations personnalisés à utiliser dans une application Xamarin. Android._

## <a name="overview"></a>Vue d'ensemble de

En dépit de leur exécution sur des appareils qui sont traditionnellement de la puissance limitée, les applications mobiles les plus importantes ont souvent une expérience utilisateur sophistiquée, avec des graphiques et des animations de haute qualité qui offrent une apparence intuitive, réactive et dynamique. Étant donné que les applications mobiles sont de plus en plus sophistiquées, les utilisateurs ont commencé à s’attendre à un plus grand nombre d’applications.

Heureusement pour nous, les plateformes mobiles modernes possèdent des infrastructures très puissantes pour créer des animations sophistiquées et des graphiques personnalisés tout en conservant la simplicité d’utilisation. Cela permet aux développeurs d’ajouter une interactivité riche avec très peu d’efforts.

Les infrastructures d’API d’interface utilisateur dans Android peuvent être divisées en deux catégories : les graphiques et les animations.

Les graphiques sont encore divisés en différentes approches pour les graphiques 2D et 3D. les graphiques 3D sont disponibles via un certain nombre d’infrastructures intégrées, telles que OpenGL ES (une version spécifique d’OpenGL) et des frameworks tiers, tels que le jeu d’outils multiplateforme (plateforme multiplateforme compatible avec XNA Toolkit). Bien que les graphiques 3D ne se trouvent pas dans le cadre de cet article, nous allons examiner les techniques de dessin 2D intégrées.

Android fournit deux API différentes pour la création de graphiques 2D. L’une est une approche déclarative de haut niveau et l’autre une API de bas niveau de programmation :

- Les **ressources dessinables** &ndash; elles sont utilisées pour créer des graphiques personnalisés soit par programmation, soit (plus généralement) en incorporant des instructions de dessin dans des fichiers XML. Les ressources pouvant être dessinées sont généralement définies en tant que fichiers XML qui contiennent des instructions ou des actions pour Android pour le rendu d’un graphique 2D. 

- **Canvas** &ndash; il s’agit d’une API de bas niveau qui implique un dessin direct sur une bitmap sous-jacente. Il offre un contrôle très précis sur ce qui est affiché. 

En plus de ces techniques graphiques 2D, Android offre également différentes façons de créer des animations :

- **Animations dessinables** &ndash; Android prend également en charge les animations frame par image appelées *animation dessinable*. Il s’agit de l’API d’animation la plus simple. Android charge et affiche séquentiellement des ressources Dessinables dans l’ordre (un peu comme un dessin animé).

- **Afficher les animations** &ndash; *afficher les animations* sont les API d’animation d’origine dans Android et sont disponibles dans toutes les versions d’Android. Cette API est limitée dans la mesure où elle ne fonctionne qu’avec les objets de vue et ne peut effectuer que des transformations simples sur ces vues.
    Les animations de vue sont généralement définies dans des fichiers XML qui se trouvent dans le dossier `/Resources/anim`.

- Les **animations de propriété** &ndash; Android 3,0 ont introduit un nouvel ensemble d’API d’animation appelées « animations de *Propriétés*». Ces nouvelles API ont introduit un système extensible et flexible qui peut être utilisé pour animer les propriétés de n’importe quel objet, et pas seulement les objets de vue. Cette flexibilité permet aux animations d’être encapsulées dans des classes distinctes qui facilitent le partage de code.

Les animations de vue sont plus appropriées pour les applications qui doivent prendre en charge les anciennes API 3,0 pré-Android (niveau d’API 11). Sinon, les applications doivent utiliser les API d’animation de propriétés plus récentes pour les raisons mentionnées ci-dessus.

Toutes ces infrastructures sont des options viables. Toutefois, dans la mesure du possible, la préférence doit être donnée aux animations de propriété, car il s’agit d’une API plus flexible à utiliser. Les animations de propriété permettent d’encapsuler la logique d’animation dans des classes distinctes qui facilitent le partage de code et simplifie la maintenance du code.

## <a name="accessibility"></a>Accessibilité

Les graphiques et les animations permettent de rendre les applications Android attrayantes et amusantes à utiliser. Toutefois, il est important de se souvenir que certaines interactions sont effectuées via lecteurs, d’autres périphériques d’entrée ou avec le zoom assisté.
En outre, certaines interactions peuvent se produire sans fonctionnalités audio.

Les applications sont plus utilisables dans ces situations si elles ont été conçues avec l’accessibilité à l’esprit : fournir des conseils et une assistance de navigation dans l’interface utilisateur, et s’assurer qu’il existe du contenu texte ou des descriptions pour les éléments de l’interface utilisateur.

Reportez-vous au [Guide d’accessibilité de Google](https://developer.android.com/guide/topics/ui/accessibility/) pour plus d’informations sur l’utilisation des API d’accessibilité d’Android.

## <a name="2d-graphics"></a>Graphiques 2D

Les ressources dessinables sont une technique répandue dans les applications Android. Comme pour les autres ressources, les ressources Dessinables sont déclaratives &ndash; elles sont définies dans des fichiers XML. Cette approche permet une séparation nette du code des ressources. Cela peut simplifier le développement et la maintenance, car il n’est pas nécessaire de modifier le code pour mettre à jour ou modifier les graphiques dans une application Android. Toutefois, bien que les ressources Dessinables soient utiles pour de nombreuses exigences graphiques simples et courantes, elles ne disposent pas de la puissance et du contrôle de l’API Canvas.

L’autre technique, à l’aide de l’objet [Canvas](xref:Android.Graphics.Canvas) , est très similaire à d’autres infrastructures d’API traditionnelles, telles que System. Drawing ou le dessin principal d’iOS. L’utilisation de l’objet Canvas permet de mieux contrôler la façon dont les graphiques 2D sont créés. Il convient pour les situations où une ressource qui peut être dessinée ne fonctionnera pas ou sera difficile à utiliser. Par exemple, il peut être nécessaire de dessiner un contrôle Slider personnalisé dont l’apparence sera modifiée en fonction des calculs liés à la valeur du curseur.

Examinons d’abord les ressources Dessinables. Ils sont plus simples et couvrent les cas de dessin personnalisés les plus courants.

### <a name="drawable-resources"></a>Ressources dessinables

Les ressources qui peuvent être dessinées sont définies dans un fichier XML dans le répertoire `/Resources/drawable`. Contrairement à l’incorporation de fichiers PNG ou JPEG, il n’est pas nécessaire de fournir des versions spécifiques de la densité des ressources Dessinables.
Lors de l’exécution, une application Android chargera ces ressources et utilisera les instructions contenues dans ces fichiers XML pour créer des graphiques 2D.
Android définit plusieurs types de ressources Dessinables :

- [ShapeDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; il s’agit d’un objet qui dessine une forme géométrique primitive et qui applique un ensemble limité d’effets graphiques sur cette forme. Ils sont très utiles pour des tâches telles que la personnalisation des boutons ou la définition de l’arrière-plan de TextViews. Nous verrons un exemple d’utilisation d’une `ShapeDrawable` plus loin dans cet article.

- [StateListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; il s’agit d’une ressource qui peut être dessinée qui changera d’apparence en fonction de l’état d’un widget/contrôle. Par exemple, un bouton peut modifier son apparence selon qu’il est enfoncé ou non.

- [LayerDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; cette ressource pouvant être dessinée, qui empile plusieurs autres drawables. Un exemple de *LayerDrawable* est illustré dans la capture d’écran suivante :

    ![Exemple LayerDrawable](graphics-and-animation-images/image1.png)

- [TransitionDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; il s’agit d’un *LayerDrawable* mais d’une différence. Un *TransitionDrawable* est en mesure d’animer une couche affichée au-dessus d’une autre.

- [LevelListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; cela est très similaire à un *StateListDrawable* en ce qu’il affichera une image en fonction de certaines conditions. Toutefois, contrairement à un *StateListDrawable*, *LevelListDrawable* affiche une image basée sur une valeur entière. Un exemple de *LevelListDrawable* serait d’afficher la puissance d’un signal WiFi. À mesure que la puissance du signal WiFi change, le dessinable qui s’affiche est modifié en conséquence.

- [ScaleDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash; comme son nom l’indique, ces Drawables fournissent des fonctionnalités de mise à l’échelle et de découpage. Le *ScaleDrawable* mettra à l’échelle un autre dessin, tandis que le *ClipDrawable* va détourer un autre dessin.

- [InsetDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; ce dessinable appliquera des infinies sur les côtés d’une autre ressource qui peut être dessinée. Elle est utilisée lorsqu’une vue a besoin d’un arrière-plan qui est plus petit que les limites réelles de la vue.

- [BitmapDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) XML &ndash;ce fichier est un ensemble d’instructions, en XML, qui doivent être exécutées sur une image bitmap réelle. Les actions qu’Android peut effectuer sont la juxtaposition, le tramage et l’anticrénelage. L’une des utilisations les plus courantes consiste à juxtaposer une image bitmap sur l’arrière-plan d’une disposition.

#### <a name="drawable-example"></a>Exemple de dessinable

Examinons un exemple rapide de création d’un graphique 2D à l’aide d’un `ShapeDrawable`. Une `ShapeDrawable` peut définir l’une des quatre formes de base : rectangle, ovale, ligne et anneau. Il est également possible d’appliquer des effets de base, tels que le dégradé, la couleur et la taille. Le code XML suivant est un `ShapeDrawable` qui se trouve dans le projet compagnon *AnimationsDemo* (dans le fichier `Resources/drawable/shape_rounded_blue_rect.xml`).
Il définit un rectangle avec un arrière-plan dégradé violet et des angles arrondis :

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
<!-- Specify a gradient for the background -->
<gradient android:angle="45"
          android:startColor="#55000066"
          android:centerColor="#00000000"
          android:endColor="#00000000"
          android:centerX="0.75" />

<padding android:left="5dp"
          android:right="5dp"
          android:top="5dp"
          android:bottom="5dp" />

<corners android:topLeftRadius="10dp"
          android:topRightRadius="10dp"
          android:bottomLeftRadius="10dp"
          android:bottomRightRadius="10dp" />
</shape>
```

Nous pouvons faire référence à cette ressource pouvant être dessinée de façon déclarative dans une disposition ou autre dessinable, comme indiqué dans le code XML suivant :

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="#33000000">
    <TextView android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:layout_centerInParent="true"
              android:background="@drawable/shape_rounded_blue_rect"
              android:text="@string/message_shapedrawable" />
</RelativeLayout>
```

Les ressources pouvant être dessinées peuvent également être appliquées par programme. L’extrait de code suivant montre comment définir par programmation l’arrière-plan d’un TextView :

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

Pour voir à quoi cela ressemble, exécutez le projet *AnimationsDemo* et sélectionnez l’élément de forme dessinable dans le menu principal. Nous devrions voir un résultat similaire à la capture d’écran suivante :

[![TextView avec un arrière-plan personnalisé, dessinable avec un dégradé et des angles arrondis](graphics-and-animation-images/image2-sml.png)](graphics-and-animation-images/image2.png#lightbox)

Pour plus d’informations sur les éléments XML et la syntaxe des ressources Dessinables, consultez la [documentation de Google](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape).

### <a name="using-the-canvas-drawing-api"></a>Utilisation de l’API de dessin Canvas

Les Drawables sont puissants, mais leurs limites sont limitées. Certains éléments ne sont pas possibles ou très complexes (par exemple, l’application d’un filtre à une image prise par un appareil photo sur l’appareil). Il serait très difficile d’appliquer une réduction des yeux rouges à l’aide d’une ressource qui peut être dessinée.
Au lieu de cela, l’API Canvas permet à une application d’avoir un contrôle très précis pour modifier de manière sélective les couleurs dans une partie spécifique de l’image.

Une classe qui est couramment utilisée avec Canvas est la classe [Paint](xref:Android.Graphics.Paint) . Cette classe contient des informations sur la couleur et le style de dessin. Elle est utilisée pour fournir des éléments tels qu’une couleur et une transparence.

L’API Canvas utilise le *modèle de l’outil peintre* pour dessiner des graphiques 2D.
Les opérations sont appliquées dans des couches successives les unes sur les autres. Chaque opération couvre une partie de la bitmap sous-jacente. Lorsque la zone chevauche une zone précédemment peinte, la nouvelle peinture masque partiellement ou complètement l’ancien. Il s’agit de la même façon que de nombreuses autres API de dessin, telles que les graphiques de base System. Drawing et iOS, fonctionnent.

Il existe deux façons d’obtenir un objet `Canvas`. La première consiste à définir un objet [bitmap](xref:Android.Graphics.Bitmap) , puis à instancier un objet `Canvas`. Par exemple, l’extrait de code suivant crée un nouveau canevas avec une bitmap sous-jacente :

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

L’autre façon d’obtenir un objet `Canvas` est que la méthode de rappel [OnDraw](xref:Android.Views.View.OnDraw*) qui est fournie par la classe de base [View](xref:Android.Views.View) . Android appelle cette méthode lorsqu’il décide qu’une vue doit se dessiner lui-même et passe un objet `Canvas` pour que la vue fonctionne.

La classe Canvas expose des méthodes pour fournir par programme les instructions Draw. Par exemple :

- [Canvas. DrawPaint](xref:Android.Graphics.Canvas.DrawPaint*) &ndash; remplit l’image bitmap de la totalité du canevas avec la peinture spécifiée.

- [Canvas. DrawPath](xref:Android.Graphics.Canvas.DrawPath*) &ndash; dessine la forme géométrique spécifiée à l’aide de la peinture spécifiée.

- [Canvas. DrawText](xref:Android.Graphics.Canvas.DrawText*) &ndash; dessine le texte sur la zone de dessin avec la couleur spécifiée. Le texte est dessiné à l’emplacement `x,y`.

#### <a name="drawing-with-the-canvas-api"></a>Dessin avec l’API Canvas

Voici un exemple de l’API Canvas en action. L’extrait de code suivant montre comment dessiner une vue :

```csharp
public class MyView : View
{
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        Paint green = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0x99, 0xcc, 0),
        };
        green.SetStyle(Paint.Style.FillAndStroke);

        Paint red = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0xff, 0x44, 0x44)
        };
        red.SetStyle(Paint.Style.FillAndStroke);

        float middle = canvas.Width * 0.25f;
        canvas.DrawPaint(red);
        canvas.DrawRect(0, 0, middle, canvas.Height, green);
    }
}
```

Ce code ci-dessus crée d’abord un dessin rouge et un objet Paint vert. Elle remplit le contenu du canevas en rouge, puis indique au canevas de dessiner un rectangle vert de 25% de la largeur de la zone de dessin. Par exemple, dans `AnimationsDemo` projet qui est inclus dans le code source de cet article. En démarrant l’application et en sélectionnant l’élément de dessin dans le menu principal, nous devrions obtenir un écran similaire à ce qui suit :

[Écran ![avec peinture rouge et objets de peinture verte](graphics-and-animation-images/image3-sml.png)](graphics-and-animation-images/image3.png#lightbox)

## <a name="animation"></a>Animation

Les utilisateurs comme les éléments qui se déplacent dans leurs applications. Les animations sont un excellent moyen d’améliorer l’expérience utilisateur d’une application et de l’aider à en sortir. Les meilleures animations sont celles que les utilisateurs ne remarquent pas, car elles semblent naturelles. Android fournit les trois API suivantes pour les animations :

- **Afficher l’Animation** &ndash; il s’agit de l’API d’origine. Ces animations sont liées à une vue spécifique et peuvent effectuer des transformations simples sur le contenu de la vue. En raison de sa simplicité, cette API est toujours utile pour des éléments tels que les animations alpha, les rotations, etc.

- L' **animation de propriété** &ndash; animations de propriété ont été introduites dans Android 3,0. Elles permettent à une application d’animer presque n’importe quoi. Les animations de propriété peuvent être utilisées pour modifier toute propriété d’un objet, même si cet objet n’est pas visible à l’écran.

- **Animation dessinable** &ndash; cette ressource dessinable spéciale qui est utilisée pour appliquer un effet d’animation très simple aux dispositions.

En général, l’animation de propriétés est le système préféré à utiliser, car elle est plus flexible et offre plus de fonctionnalités.

### <a name="view-animations"></a>Afficher les animations

Les animations de vue sont limitées aux vues et peuvent uniquement exécuter des animations sur des valeurs telles que les points de début et de fin, la taille, la rotation et la transparence.
Ces types d’animations sont généralement appelés animations d' *interpolation*. Les animations de vue peuvent être définies de deux façons &ndash; par programmation dans du code ou à l’aide de fichiers XML. Les fichiers XML constituent la meilleure façon de déclarer les animations de vue, car elles sont plus lisibles et plus faciles à gérer.

Les fichiers d’animation XML sont stockés dans le répertoire `/Resources/anim` d’un projet Xamarin. Android. Ce fichier doit avoir l’un des éléments suivants comme élément racine :

- `alpha` &ndash; une animation d’atténuation ou de fondu.

- `rotate` &ndash; une animation de rotation.

- `scale` &ndash; une animation de redimensionnement.

- `translate` &ndash; un mouvement horizontal et/ou vertical.

- `set` &ndash; un conteneur qui peut contenir un ou plusieurs autres éléments d’animation.

Par défaut, toutes les animations d’un fichier XML sont appliquées simultanément. Pour faire en sorte que les animations se produisent de façon séquentielle, définissez l’attribut `android:startOffset` sur l’un des éléments définis ci-dessus.

Il est possible d’affecter le taux de modification d’une animation à l’aide d’un *interpolateur*. Un interpolateur permet d’accélérer, de répéter ou de ralentir les effets d’animation. L’infrastructure Android fournit plusieurs interpolateurs prêts à l’emploi, tels que (mais non limités à) :

- `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; ces interpolateurs augmentent ou diminuent le taux de modification d’une animation.

- `BounceInterpolator` &ndash; la modification est rebondie à la fin.

- `LinearInterpolator` &ndash; le taux de modifications est constant.

Le code XML suivant montre un exemple de fichier d’animation qui combine certains de ces éléments :

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android=http://schemas.android.com/apk/res/android
     android:shareInterpolator="false">

    <scale android:interpolator="@android:anim/accelerate_decelerate_interpolator"
           android:fromXScale="1.0"
           android:toXScale="1.4"
           android:fromYScale="1.0"
           android:toYScale="0.6"
           android:pivotX="50%"
           android:pivotY="50%"
           android:fillEnabled="true"
           android:fillAfter="false"
           android:duration="700" />

    <set android:interpolator="@android:anim/accelerate_interpolator">
        <scale android:fromXScale="1.4"
               android:toXScale="0.0"
               android:fromYScale="0.6"
               android:toYScale="0.0"
               android:pivotX="50%"
               android:pivotY="50%"
               android:fillEnabled="true"
               android:fillBefore="false"
               android:fillAfter="true"
               android:startOffset="700"
               android:duration="400" />

        <rotate android:fromDegrees="0"
                android:toDegrees="-45"
                android:toYScale="0.0"
                android:pivotX="50%"
                android:pivotY="50%"
                android:fillEnabled="true"
                android:fillBefore="false"
                android:fillAfter="true"
                android:startOffset="700"
                android:duration="400" />
    </set>
</set>
```

Cette animation effectue toutes les animations simultanément. La première animation de mise à l’échelle étire l’image horizontalement et la réduit verticalement, puis l’image pivote simultanément de 45 degrés dans le sens des aiguilles d’une montre et de la réduction, disparition de l’écran.

L’animation peut être appliquée par programmation à une vue en la gonflant, puis en l’appliquant à une vue. Android fournit la classe d’assistance `Android.Views.Animations.AnimationUtils` qui gonflera une ressource d’animation et renverra une instance de `Android.Views.Animations.Animation`. Cet objet est appliqué à une vue en appelant `StartAnimation` et en passant l’objet `Animation`. L’extrait de code suivant en montre un exemple :

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

Maintenant que nous avons une compréhension fondamentale de la façon dont les animations de vue fonctionnent, vous pouvez passer aux animations de propriétés.

### <a name="property-animations"></a>Animations de propriétés

Les animateurs de propriété sont une nouvelle API qui a été introduite dans Android 3,0.
Ils fournissent une API plus extensible qui peut être utilisée pour animer n’importe quelle propriété sur n’importe quel objet.

Toutes les animations de propriétés sont créées par des instances de la sous-classe d' [animation](xref:Android.Animation.Animator) . Les applications n’utilisent pas directement cette classe, mais elles utilisent l’une de ses sous-classes :

- [ValueAnimator](xref:Android.Animation.ValueAnimator) &ndash; cette classe est la classe la plus importante de l’API d’animation de propriété entière. Elle calcule les valeurs des propriétés qui doivent être modifiées. Le `ViewAnimator` ne met pas à jour ces valeurs directement ; au lieu de cela, il déclenche des événements qui peuvent être utilisés pour mettre à jour des objets animés.

- [ObjectAnimator](xref:Android.Animation.ObjectAnimator) &ndash; cette classe est une sous-classe de `ValueAnimator`. Il est conçu pour simplifier le processus d’animation des objets en acceptant un objet cible et une propriété à mettre à jour.

- [AnimationSet](xref:Android.Animation.AnimatorSet) &ndash; cette classe est chargée d’orchestrer la manière dont les animations s’exécutent les unes par rapport aux autres. Les animations peuvent s’exécuter simultanément, séquentiellement ou avec un délai spécifié entre elles.

Les *évaluateurs* sont des classes spéciales qui sont utilisées par les animateurs pour calculer les nouvelles valeurs pendant une animation. En préversion, Android fournit les évaluateurs suivants :

- [IntEvaluator](xref:Android.Animation.IntEvaluator) &ndash; calcule les valeurs des propriétés de type entier.

- [FloatEvaluator](xref:Android.Animation.FloatEvaluator) &ndash; calcule les valeurs des propriétés float.

- [ArgbEvaluator](xref:Android.Animation.ArgbEvaluator) &ndash; calcule les valeurs des propriétés de couleur.

Si la propriété qui est animée n’est pas un `float`, `int` ou Color, les applications peuvent créer leur propre évaluateur en implémentant l’interface `ITypeEvaluator`. (L’implémentation d’évaluateurs personnalisés dépasse le cadre de cette rubrique.)

#### <a name="using-the-valueanimator"></a>Utilisation de ValueAnimator

Il y a deux parties à une animation : calculant des valeurs animées, puis définition de ces valeurs sur des propriétés sur un objet. 
[ValueAnimator](xref:Android.Animation.ValueAnimator) calcule uniquement les valeurs, mais ne fonctionne pas directement sur les objets. Au lieu de cela, les objets sont mis à jour dans les gestionnaires d’événements qui seront appelés pendant la durée de vie de l’animation. Cette conception permet de mettre à jour plusieurs propriétés à partir d’une valeur animée.

Vous obtenez une instance de `ValueAnimator` en appelant l’une des méthodes de fabrique suivantes :

- `ValueAnimator.OfInt`
- `ValueAnimator.OfFloat`
- `ValueAnimator.OfObject`

Une fois cette opération terminée, la durée de l’instance de `ValueAnimator` doit être définie, puis elle peut être démarrée. L’exemple suivant montre comment animer une valeur de 0 à 1 sur l’étendue de 1000 millisecondes :

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

Toutefois, l’extrait de code ci-dessus n’est pas très utile &ndash; l’animateur s’exécutera, mais il n’y a aucune cible pour la valeur mise à jour. La classe `Animator` déclenche l’événement Update lorsqu’il décide qu’il est nécessaire d’informer les écouteurs d’une nouvelle valeur. Les applications peuvent fournir un gestionnaire d’événements pour répondre à cet événement, comme indiqué dans l’extrait de code suivant :

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

animator.Update += (object sender, ValueAnimator.AnimatorUpdateEventArgs e) =>
{
    int newValue = (int) e.Animation.AnimatedValue;
    // Apply this new value to the object being animated.
    myObj.SomeIntegerValue = newValue;
};
```

Maintenant que nous avons une compréhension des `ValueAnimator`, vous pouvez en savoir plus sur le `ObjectAnimator`.

#### <a name="using-the-objectanimator"></a>Utilisation de ObjectAnimator

[ObjectAnimator](xref:Android.Animation.ObjectAnimator) est une sous-classe de `ViewAnimator` qui associe le moteur de minutage et le calcul de valeur du `ValueAnimator` à la logique requise pour connecter les gestionnaires d’événements. Le `ValueAnimator` nécessite que les applications associent explicitement un gestionnaire d’événements &ndash; `ObjectAnimator` s’occupe de cette étape pour nous.

L’API pour `ObjectAnimator` est très similaire à l’API de `ViewAnimator`, mais exige que vous fournissiez l’objet et le nom de la propriété à mettre à jour. L’exemple suivant montre un exemple d’utilisation de `ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

Comme vous pouvez le voir à partir de l’extrait de code précédent, `ObjectAnimator` pouvez réduire et simplifier le code qui est nécessaire pour animer un objet.

### <a name="drawable-animations"></a>Animations dessinables

L’API d’animation finale est l’API d’animation dessinable. Les animations dessinables chargent une série de ressources dessinées l’une après l’autre et les affichent de manière séquentielle, comme dans un dessin animé.

Les ressources pouvant être dessinées sont définies dans un fichier XML qui a un élément `<animation-list>` en tant qu’élément racine et une série d’éléments `<item>` qui définissent chaque frame de l’animation. Ce fichier XML est stocké dans le dossier `/Resource/drawable` de l’application. Le code XML suivant est un exemple d’animation dessinable :

```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:drawable="@drawable/asteroid01" android:duration="100" />
  <item android:drawable="@drawable/asteroid02" android:duration="100" />
  <item android:drawable="@drawable/asteroid03" android:duration="100" />
  <item android:drawable="@drawable/asteroid04" android:duration="100" />
  <item android:drawable="@drawable/asteroid05" android:duration="100" />
  <item android:drawable="@drawable/asteroid06" android:duration="100" />
</animation-list>
```

Cette animation s’exécutera à l’aide de six frames. L’attribut `android:duration` déclare la durée pendant laquelle chaque image sera affichée. L’extrait de code suivant montre un exemple de création d’une animation dessinable et de son démarrage lorsque l’utilisateur clique sur un bouton à l’écran :

```csharp
AnimationDrawable _asteroidDrawable;

protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    _asteroidDrawable = (Android.Graphics.Drawables.AnimationDrawable)
    Resources.GetDrawable(Resource.Drawable.spinning_asteroid);

    ImageView asteroidImage = FindViewById<ImageView>(Resource.Id.imageView2);
    asteroidImage.SetImageDrawable((Android.Graphics.Drawables.Drawable) _asteroidDrawable);

    Button asteroidButton = FindViewById<Button>(Resource.Id.spinAsteroid);
    asteroidButton.Click += (sender, e) =>
    {
        _asteroidDrawable.Start();
    };
}
```

À ce stade, nous avons abordé les bases des API d’animation disponibles dans une application Android.

## <a name="summary"></a>Récapitulatif

Cet article a introduit de nombreux nouveaux concepts et API pour vous aider à ajouter des graphiques à une application Android. Tout d’abord, il a abordé les différentes API graphiques 2D et a démontré comment Android permet aux applications de dessiner directement à l’écran à l’aide d’un objet Canvas. Nous avons également vu des techniques alternatives qui permettent de créer des graphiques de manière déclarative à l’aide de fichiers XML. Ensuite, nous avons abordé les anciennes et nouvelles API pour créer des animations dans Android.

## <a name="related-links"></a>Liens associés

- [Démonstration d’animation (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/animationdemo)
- [Animation et graphiques](https://developer.android.com/guide/topics/graphics/index.html)
- [Utilisation d’animations pour donner vie à vos Mobile Apps](https://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](xref:Android.Graphics.Drawables.AnimationDrawable)
- [Canvas](xref:Android.Graphics.Canvas)
- [Animateur d’objets](xref:Android.Animation.ObjectAnimator)
- [Animation de valeur](xref:Android.Animation.ValueAnimator)
