---
title: Suivi des doigts multipoint dans Xamarin. Android
description: Cette rubrique montre comment suivre les événements tactiles à partir de plusieurs doigts
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 8c2e06d2700cd7b61c16fc993d807ca4d042a063
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455116"
---
# <a name="multi-touch-finger-tracking"></a>Suivi des doigts multipoint

_Cette rubrique montre comment suivre les événements tactiles à partir de plusieurs doigts_

Il arrive parfois qu’une application multipoint doive suivre les doigts individuels lorsqu’ils se déplacent simultanément sur l’écran. Une application classique est un programme de peinture par doigt. Vous souhaitez que l’utilisateur soit en mesure de dessiner avec un seul doigt, mais également de dessiner avec plusieurs doigts à la fois. À mesure que votre programme traite plusieurs événements tactiles, il doit distinguer les événements qui correspondent à chaque doigt. Android fournit un code d’ID à cet effet, mais l’obtention et la gestion de ce code peuvent être un peu délicates.

Pour tous les événements associés à un doigt particulier, le code d’ID reste le même. Le code d’ID est affecté lorsqu’un doigt touche d’abord l’écran et devient non valide après que le doigt s’est levé à partir de l’écran.
Ces codes d’ID sont généralement très petits entiers et Android les réutilise pour les événements tactiles ultérieurs.

Presque toujours, un programme qui suit des doigts individuels conserve un dictionnaire pour le suivi tactile. La clé du dictionnaire est le code d’ID qui identifie un doigt particulier. La valeur du dictionnaire dépend de l’application. Dans le programme [FingerPaint](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) , chaque trait de doigt (du toucher au lancement) est associé à un objet qui contient toutes les informations nécessaires pour afficher la ligne dessinée avec ce doigt. Le programme définit une petite `FingerPaintPolyline` classe à cet effet :

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new Path();
    }

    public Color Color { set; get; }

    public float StrokeWidth { set; get; }

    public Path Path { private set; get; }
}
```

Chaque polyligne a une couleur, une largeur de trait et un objet graphique Android [`Path`](xref:Android.Graphics.Path) pour accumuler et afficher plusieurs points de la ligne lorsqu’elle est dessinée.

Le reste du code présenté ci-dessous est contenu dans une `View` dérivée nommée `FingerPaintCanvasView` . Cette classe gère un dictionnaire d’objets de type `FingerPaintPolyline` pendant la durée pendant laquelle ils sont activement dessinés par un ou plusieurs doigts :

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Ce dictionnaire permet à la vue d’obtenir rapidement les `FingerPaintPolyline` informations associées à un doigt particulier.

La `FingerPaintCanvasView` classe gère également un `List` objet pour les polylignes qui ont été terminées :

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Les objets de ce `List` sont dans le même ordre que celui dans lequel ils ont été dessinés.

`FingerPaintCanvasView` remplace deux méthodes définies par `View` : [`OnDraw`](xref:Android.Views.View.OnDraw*)
et [`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*).
Dans sa `OnDraw` substitution, la vue dessine les polylignes terminées, puis dessine les polylignes en cours.

La substitution de la `OnTouchEvent` méthode commence par l’obtention d' `pointerIndex` une valeur de la `ActionIndex` propriété. Cette `ActionIndex` valeur fait la distinction entre plusieurs doigts, mais elle n’est pas cohérente entre plusieurs événements. Pour cette raison, vous utilisez `pointerIndex` pour obtenir la `id` valeur de pointeur à partir de la `GetPointerId` méthode. Cet ID *est* cohérent sur plusieurs événements :

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        // ...
    }

    // Invalidate to update the view
    Invalidate();

    // Request continued touch input
    return true;
}
```

Notez que la substitution utilise la `ActionMasked` propriété dans l' `switch` instruction au lieu de la `Action` propriété. Voici pourquoi :

Lorsque vous travaillez sur le multipoint, la `Action` propriété a la valeur `MotionEventsAction.Down` pour que le premier doigt touche l’écran, puis les valeurs de `Pointer2Down` et du `Pointer3Down` deuxième et du troisième doigts touchent également l’écran. Étant donné que les quatrième et cinquième doigts rendent contact, la `Action` propriété a des valeurs numériques qui ne correspondent même pas aux membres de l' `MotionEventsAction` énumération ! Vous devez examiner les valeurs des indicateurs binaires dans les valeurs pour interpréter ce qu’ils signifient.

De même, comme les doigts laissent contact avec l’écran, la `Action` propriété a les valeurs `Pointer2Up` et `Pointer3Up` pour les deuxième et troisième doigts, et `Up` pour le premier doigt.

La `ActionMasked` propriété prend moins de valeurs, car elle est destinée à être utilisée conjointement avec la `ActionIndex` propriété pour faire la distinction entre plusieurs doigts. Lorsque les doigts touchent l’écran, la propriété peut uniquement être égale `MotionEventActions.Down` pour le premier doigt et `PointerDown` pour les doigts suivants. À mesure que les doigts laissent l’écran, `ActionMasked` a des valeurs `Pointer1Up` pour les doigts suivants et `Up` pour le premier doigt.

Lors de l’utilisation de `ActionMasked` , la fait la `ActionIndex` distinction entre les doigts suivants et laisse l’écran, mais vous n’avez généralement pas besoin d’utiliser cette valeur, sauf en tant qu’argument pour d’autres méthodes de l' `MotionEvent` objet. Pour le multipoint, l’une des plus importantes de ces méthodes est `GetPointerId` appelée dans le code ci-dessus. Cette méthode retourne une valeur que vous pouvez utiliser pour une clé de dictionnaire pour associer des événements particuliers à des doigts.

Le `OnTouchEvent` remplacement dans le programme [FingerPaint](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) traite les `MotionEventActions.Down` événements et de la même façon `PointerDown` en créant un nouvel `FingerPaintPolyline` objet et en l’ajoutant au dictionnaire :

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:

            // Create a Polyline, set the initial point, and store it
            FingerPaintPolyline polyline = new FingerPaintPolyline
            {
                Color = StrokeColor,
                StrokeWidth = StrokeWidth
            };

            polyline.Path.MoveTo(args.GetX(pointerIndex),
                                 args.GetY(pointerIndex));

            inProgressPolylines.Add(id, polyline);
            break;
        // ...
    }
    // ...        
}
```

Notez que `pointerIndex` est également utilisé pour obtenir la position du doigt dans la vue. Toutes les informations tactiles sont associées à la `pointerIndex` valeur. Le `id` identifie de manière unique les doigts sur plusieurs messages, ce qui permet de créer l’entrée de dictionnaire.

De même, le `OnTouchEvent` remplacement gère également et de `MotionEventActions.Up` la `Pointer1Up` même façon en transférant la polyligne terminée à la `completedPolylines` collection afin qu’elles puissent être dessinées pendant la `OnDraw` substitution. Le code supprime également l' `id` entrée du dictionnaire :

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Up:
        case MotionEventActions.Pointer1Up:

            inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                args.GetY(pointerIndex));

            // Transfer the in-progress polyline to a completed polyline
            completedPolylines.Add(inProgressPolylines[id]);
            inProgressPolylines.Remove(id);
            break;

        case MotionEventActions.Cancel:
            inProgressPolylines.Remove(id);
            break;
    }
    // ...        
}
```

Maintenant, pour une partie délicate.

En général, il existe de nombreux événements entre les événements de descendant et de haut `MotionEventActions.Move` . Celles-ci sont regroupées dans un seul appel à `OnTouchEvent` , et elles doivent être gérées différemment des `Down` `Up` événements et. La `pointerIndex` valeur obtenue précédemment à partir de la `ActionIndex` propriété doit être ignorée. Au lieu de cela, la méthode doit obtenir plusieurs `pointerIndex` valeurs en effectuant une boucle entre 0 et la `PointerCount` propriété, puis obtenir un `id` pour chacune de ces `pointerIndex` valeurs :

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Move:

            // Multiple Move events are bundled, so handle them differently
            for (pointerIndex = 0; pointerIndex < args.PointerCount; pointerIndex++)
            {
                id = args.GetPointerId(pointerIndex);

                inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                    args.GetY(pointerIndex));
            }
            break;
        // ...
    }
    // ...        
}
```

Ce type de traitement permet au programme [FingerPaint](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) de suivre des doigts individuels et de dessiner les résultats à l’écran :

[![Exemple de capture d’écran de l’exemple FingerPaint](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Vous avez maintenant vu comment vous pouvez suivre des doigts individuels sur l’écran et les distinguer.

## <a name="related-links"></a>Liens associés

- [Guide Xamarin iOS équivalent](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (exemple)](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)