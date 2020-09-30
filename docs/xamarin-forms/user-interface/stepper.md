---
title: Xamarin.Forms Pas à pas
description: L’exécution pas Xamarin.Forms à pas permet à un utilisateur de sélectionner une valeur numérique à partir d’une plage de valeurs. Il se compose de deux boutons étiquetés avec les signes moins et plus. La manipulation des deux boutons modifie la valeur sélectionnée de façon incrémentielle.
ms.prod: xamarin
ms.assetid: 62571B3E-D84B-4F52-9FC7-C105D6733B16
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 390bca8cb74fd2da725724769956b164e0264173
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558756"
---
# <a name="no-locxamarinforms-stepper"></a>Xamarin.Forms Pas à pas

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)

_Utilisez une exécution pas à pas pour sélectionner une valeur numérique à partir d’une plage de valeurs._

Le Xamarin.Forms [`Stepper`](xref:Xamarin.Forms.Stepper) se compose de deux boutons étiquetés avec les signes moins et plus. Ces boutons peuvent être manipulés par l’utilisateur pour sélectionner de manière incrémentielle une `double` valeur dans une plage de valeurs.

[`Stepper`](xref:Xamarin.Forms.Stepper)Définit quatre propriétés de type `double` :

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) est la valeur de modification de la valeur sélectionnée, avec 1 comme valeur par défaut.
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) est le minimum de la plage, avec 0 comme valeur par défaut.
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) correspond au maximum de la plage, avec une valeur par défaut de 100.
- [`Value`](xref:Xamarin.Forms.Stepper.Value) est la valeur de l’exécution pas à pas, qui peut être comprise entre `Minimum` et `Maximum` et a une valeur par défaut de 0.

Toutes ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets. La [`Value`](xref:Xamarin.Forms.Stepper.Value) propriété a un mode de liaison par défaut de, ce qui [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) signifie qu’elle est appropriée comme source de liaison dans une application qui utilise l’architecture [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

> [!WARNING]
> En interne, la [`Stepper`](xref:Xamarin.Forms.Stepper) s’assure que [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) est inférieur à [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) . Si `Minimum` ou `Maximum` sont toujours définis de sorte que `Minimum` n’est pas inférieur à `Maximum` , une exception est levée. Pour plus d’informations sur la définition des `Minimum` `Maximum` Propriétés et, consultez la section de [précautions](#precautions) .

Le [`Stepper`](xref:Xamarin.Forms.Stepper) force la [`Value`](xref:Xamarin.Forms.Stepper.Value) propriété afin qu’elle soit comprise entre [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) et [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) , inclus. Si la `Minimum` propriété est définie sur une valeur supérieure à la `Value` propriété, la `Stepper` propriété affecte la valeur `Value` à la propriété `Minimum` . De même, si `Maximum` a une valeur inférieure à `Value` , `Stepper` affecte à la propriété la valeur `Value` `Maximum` .

[`Stepper`](xref:Xamarin.Forms.Stepper) définit un [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) événement qui est déclenché lorsque le [`Value`](xref:Xamarin.Forms.Stepper.Value) change, soit par manipulation de l’utilisateur du `Stepper` ou lorsque l’application définit la `Value` propriété directement. Un `ValueChanged` événement est également déclenché lorsque la `Value` propriété est forcée, comme décrit dans le paragraphe précédent.

L' [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) objet qui accompagne l' [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) événement a deux propriétés, de type `double` : [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) et [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) . Au moment où l’événement est déclenché, la valeur de `NewValue` est identique à la [`Value`](xref:Xamarin.Forms.Stepper.Value) propriété de l' [`Stepper`](xref:Xamarin.Forms.Stepper) objet.

## <a name="basic-stepper-code-and-markup"></a>Code et balises de pas à pas de base

L’exemple [**StepperDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) contient trois pages qui sont fonctionnellement identiques, mais qui sont implémentées de différentes façons. La première page utilise uniquement du code C#, la seconde utilise XAML avec un gestionnaire d’événements dans le code, et la troisième est en mesure d’éviter le gestionnaire d’événements à l’aide de la liaison de données dans le fichier XAML.

### <a name="creating-a-stepper-in-code"></a>Création d’un pas à pas dans le code

La page de codes de pas à pas de **base** de l’exemple [**StepperDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) montre comment créer un [`Stepper`](xref:Xamarin.Forms.Stepper) et deux [`Label`](xref:Xamarin.Forms.Label) objets dans le code :

```csharp
public class BasicStepperCodePage : ContentPage
{
    public BasicStepperCodePage()
    {
        Label rotationLabel = new Label
        {
            Text = "ROTATING TEXT",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Label displayLabel = new Label
        {
            Text = "(uninitialized)",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Stepper stepper = new Stepper
        {
            Maximum = 360,
            Increment = 30,
            HorizontalOptions = LayoutOptions.Center
        };
        stepper.ValueChanged += (sender, e) =>
        {
            rotationLabel.Rotation = stepper.Value;
            displayLabel.Text = string.Format("The Stepper value is {0}", e.NewValue);
        };

        Title = "Basic Stepper Code";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { rotationLabel, stepper, displayLabel }
        };
    }
}
```

[`Stepper`](xref:Xamarin.Forms.Stepper)Est initialisé pour avoir une [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) propriété de 360 et une [`Increment`](xref:Xamarin.Forms.Stepper.Increment) propriété de 30. La manipulation `Stepper` de modifie la valeur sélectionnée de façon incrémentielle entre [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) à `Maximum` , en fonction de la valeur de la `Increment` propriété. Le [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) Gestionnaire du `Stepper` utilise la [`Value`](xref:Xamarin.Forms.Stepper.Value) propriété de l' `stepper` objet pour définir la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriété du premier [`Label`](xref:Xamarin.Forms.Label)  et utilise la `string.Format` méthode avec la `NewValue` propriété des arguments d’événement pour définir la [`Text`](xref:Xamarin.Forms.Label.Text) propriété du deuxième `Label` . Ces deux approches pour obtenir la valeur actuelle de `Stepper` sont interchangeables.

Les captures d’écran suivantes illustrent la page de codes de pas à pas de **base** :

[![Code de pas à pas de base](stepper-images/basic-stepper-code.png "Code de pas à pas de base")](stepper-images/basic-stepper-code-large.png#lightbox)

Le deuxième [`Label`](xref:Xamarin.Forms.Label)  affiche le texte « (non initialisé) » jusqu’à ce que le [`Stepper`](xref:Xamarin.Forms.Stepper) soit manipulé, ce qui entraîne le déclenchement du premier [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) événement.

### <a name="creating-a-stepper-in-xaml"></a>Création d’un pas à pas dans XAML

La page **XAML de base** du code pas à pas est fonctionnellement identique au code de pas à pas de **base** , mais implémentée principalement en XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperXAMLPage"
             Title="Basic Stepper XAML">
    <StackLayout Margin="20">
        <Label x:Name="_rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center"
                 ValueChanged="OnStepperValueChanged" />
        <Label x:Name="_displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />        
    </StackLayout>
</ContentPage>
```

Le fichier code-behind contient le gestionnaire de l' [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) événement :

```csharp
public partial class BasicStepperXAMLPage : ContentPage
{
    public BasicStepperXAMLPage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs e)
    {
        double value = e.NewValue;
        _rotatingLabel.Rotation = value;
        _displayLabel.Text = string.Format("The Stepper value is {0}", value);
    }
}
```

Il est également possible que le gestionnaire d’événements obtienne le [`Stepper`](xref:Xamarin.Forms.Stepper) qui déclenche l’événement à l’aide de l' `sender` argument. La [`Value`](xref:Xamarin.Forms.Stepper.Value) propriété contient la valeur actuelle :

```csharp
double value = ((Stepper)sender).Value;
```

Si l' [`Stepper`](xref:Xamarin.Forms.Stepper) objet a reçu un nom dans le fichier XAML avec un `x:Name` attribut (par exemple, « pas à pas »), le gestionnaire d’événements peut référencer directement cet objet :

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>Exécution pas à pas de la liaison de données

La page des liaisons de pas à pas de **base** montre comment écrire une application presque équivalente qui élimine le [`Value`](xref:Xamarin.Forms.Stepper.Value) Gestionnaire d’événements à l’aide de la [liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperBindingsPage"
             Title="Basic Stepper Bindings">
    <StackLayout Margin="20">
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference _stepper}, Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper x:Name="_stepper"
                 Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center" />
        <Label Text="{Binding Source={x:Reference _stepper}, Path=Value, StringFormat='The Stepper value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

La [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriété du premier [`Label`](xref:Xamarin.Forms.Label) est liée à la [`Value`](xref:Xamarin.Forms.Stepper.Value) propriété de [`Stepper`](xref:Xamarin.Forms.Stepper) , tout comme la [`Text`](xref:Xamarin.Forms.Label.Text) propriété de la seconde `Label` avec une `StringFormat` spécification. La page des liaisons de pas à pas de **base** fonctionne un peu différemment des deux pages précédentes : lorsque la page s’affiche pour la première fois, la deuxième `Label` affiche la chaîne de texte avec la valeur. Il s’agit d’un avantage de l’utilisation de la liaison de données. Pour afficher du texte sans liaison de données, vous devez initialiser spécifiquement la `Text` propriété de `Label` ou simuler un déclenchement de l' [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) événement en appelant le gestionnaire d’événements à partir du constructeur de classe.

## <a name="precautions"></a>Précautions

La valeur de la [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) propriété doit toujours être inférieure à la valeur de la [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) propriété. L’extrait de code suivant provoque la [`Stepper`](xref:Xamarin.Forms.Stepper) levée d’une exception par le.

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

Le compilateur C# génère du code qui définit ces deux propriétés en séquence, et lorsque la [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) propriété est définie sur 180, elle est supérieure à la [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) valeur par défaut 100. Dans ce cas, vous pouvez éviter l’exception en définissant la `Maximum` propriété en premier :

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

[`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)La définition de sur 360 n’est pas un problème, car elle est supérieure à la valeur par défaut [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 0. Lorsque `Minimum` est défini, la valeur est inférieure à la `Maximum` valeur de 360.

Le même problème existe dans XAML. Définissez les propriétés dans un ordre qui garantit que [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) est toujours supérieur à `Minimum` :

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

Vous pouvez définir les [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) valeurs et sur des nombres négatifs, mais uniquement dans un ordre où `Minimum` est toujours inférieur à `Maximum` :

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

La [`Value`](xref:Xamarin.Forms.Stepper.Value) propriété est toujours supérieure ou égale à la [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) valeur et inférieure ou égale à [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) . Si `Value` est défini sur une valeur en dehors de cette plage, la valeur sera forcée à se trouver dans la plage, mais aucune exception n’est levée. Par exemple, ce code ne lève *pas* d’exception :

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

Au lieu de cela, la [`Value`](xref:Xamarin.Forms.Stepper.Value) propriété est forcée à la [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) valeur 100.

Voici un extrait de code ci-dessus :

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Lorsque [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) a la valeur 180, [`Value`](xref:Xamarin.Forms.Stepper.Value) est également défini sur 180.

Si un [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) Gestionnaire d’événements a été attaché au moment où la [`Value`](xref:Xamarin.Forms.Stepper.Value) propriété est forcée à une valeur autre que sa valeur par défaut 0, un `ValueChanged` événement est déclenché. Voici un extrait de code XAML :

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

Lorsque [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) a la valeur 180, [`Value`](xref:Xamarin.Forms.Stepper.Value) est également défini sur 180, et l' [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) événement est déclenché. Cela peut se produire avant la construction du reste de la page, et le gestionnaire peut tenter de faire référence à d’autres éléments de la page qui n’ont pas encore été créés. Vous souhaiterez peut-être ajouter du code au `ValueChanged` gestionnaire qui vérifie les `null` valeurs d’autres éléments sur la page. Ou bien, vous pouvez définir le `ValueChanged` Gestionnaire d’événements une fois que les [`Stepper`](xref:Xamarin.Forms.Stepper) valeurs ont été initialisées.

## <a name="related-links"></a>Liens associés

- [Exemples de démonstrations de pas à pas](/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)
- [API de pas à pas](xref:Xamarin.Forms.Stepper)