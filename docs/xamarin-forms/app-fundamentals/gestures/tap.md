---
title: Ajouter un module de reconnaissance de mouvement TAP
description: Cet article explique comment utiliser le geste TAP pour la détection de TAP dans une Xamarin.Forms application. La détection des appuis est implémentée avec la classe TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fef28c88c0de0bd14eb1ab5868dc6433456184e1
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563430"
---
# <a name="add-a-tap-gesture-recognizer"></a>Ajouter un module de reconnaissance de mouvement TAP

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)

_Le geste d’appui est utilisé pour la détection des appuis, il est implémentée avec la classe TapGestureRecognizer._

Pour créer un élément d’interface utilisateur avec le mouvement TAP, créez une [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) instance, gérez l' [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) événement et ajoutez le nouveau module de reconnaissance de mouvement à la [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) collection sur l’élément d’interface utilisateur. L’exemple de code suivant montre un `TapGestureRecognizer` attaché à un [`Image`](xref:Xamarin.Forms.Image) élément :

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Par défaut, l’image répond aux appuis uniques. Définissez la [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) propriété pour qu’elle attende un double-clic (ou plusieurs taraudions si nécessaire).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Lorsque [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) est défini au-dessus d’un, le gestionnaire d’événements est exécuté uniquement si les clics se produisent dans un laps de temps défini (cette période n’est pas configurable). Si les appuis suivants (le deuxième ou les autres) ne se produisent pas dans cette période, ils sont effectivement ignorés et le « nombre d’appuis » redémarre.

## <a name="using-xaml"></a>Utilisation de Xaml

Vous pouvez ajouter un module de reconnaissance de geste à un contrôle en Xaml à l’aide des propriétés jointes. La syntaxe permettant d’ajouter un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) à une image est indiquée ci-dessous (dans ce cas, la définition d’un événement *double tap* ) :

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

Le code du gestionnaire d’événements (dans l’exemple) incrémente un compteur et remplace l’image en couleur par une image en noir et blanc.

```csharp
void OnTapGestureRecognizerTapped(object sender, EventArgs args)
{
    tapCount++;
    var imageSender = (Image)sender;
    // watch the monkey go from color to black&white!
    if (tapCount % 2 == 0) {
        imageSender.Source = "tapped.jpg";
    } else {
        imageSender.Source = "tapped_bw.jpg";
    }
}
```

## <a name="using-icommand"></a>Utilisation de ICommand

Les applications qui utilisent modèle-vue-vue modèle (MVVM) se servent généralement d’`ICommand` plutôt que des gestionnaires d’événements. [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)Peut facilement être pris en charge `ICommand` en définissant la liaison dans le code :

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

ou en utilisant Xaml :

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

Le code complet pour ce modèle de vue se trouve dans l’exemple. Les informations pertinentes sur l’implémentation de `Command` sont présentées ci-dessous :

```csharp
public class TapViewModel : INotifyPropertyChanged
{
    int taps = 0;
    ICommand tapCommand;
    public TapViewModel () {
        // configure the TapCommand with a method
        tapCommand = new Command (OnTapped);
    }
    public ICommand TapCommand {
        get { return tapCommand; }
    }
    void OnTapped (object s)  {
        taps++;
        Debug.WriteLine ("parameter: " + s);
    }
    //region INotifyPropertyChanged code omitted
}
```

## <a name="related-links"></a>Liens associés

- [TapGesture (exemple)](/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)