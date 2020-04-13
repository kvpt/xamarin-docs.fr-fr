---
title: Ajout d’un module de reconnaissance des appuis
description: Cet article explique comment utiliser le geste d’appui pour la détection des appuis dans une application Xamarin.Forms. La détection des appuis est implémentée avec la classe TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 95f25dbce55e2b960f604b6e304ffb6e8ed775e0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771335"
---
# <a name="adding-a-tap-gesture-recognizer"></a>Ajout d’un module de reconnaissance des appuis

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)

_Le geste d’appui est utilisé pour la détection des appuis, il est implémentée avec la classe TapGestureRecognizer._

Pour rendre un élément d’interface utilisateur cliquable avec le geste de robinet, créez une [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) instance, gérez l’événement [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) et ajoutez le nouveau gestuelle à la [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) collection sur l’élément interface utilisateur. L’exemple de `TapGestureRecognizer` code suivant [`Image`](xref:Xamarin.Forms.Image) montre un élément attaché à un élément :

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Par défaut, l’image répond aux appuis uniques. Réglez la [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) propriété pour attendre un double-tap (ou plus de robinets si nécessaire).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Lorsqu’il [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) est réglé au-dessus d’un, le gestionnaire d’événements ne sera exécuté que si les robinets se produisent dans un délai défini (cette période n’est pas configurable). Si les appuis suivants (le deuxième ou les autres) ne se produisent pas dans cette période, ils sont effectivement ignorés et le « nombre d’appuis » redémarre.

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Utilisation de Xaml

Vous pouvez ajouter un module de reconnaissance de geste à un contrôle en Xaml à l’aide des propriétés jointes. La syntaxe [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) pour ajouter une image est montrée ci-dessous (dans ce cas définissant un double *événement de robinet)* :

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

Les applications qui utilisent modèle-vue-vue modèle (MVVM) se servent généralement d’`ICommand` plutôt que des gestionnaires d’événements. La [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) boîte `ICommand` peut facilement prendre en charge soit en définissant la liaison dans le code:

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

## <a name="related-links"></a>Liens connexes

- [TapGesture (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
