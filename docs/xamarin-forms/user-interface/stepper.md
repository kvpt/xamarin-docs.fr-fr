---
title: Exécution pas à pas de Xamarin.Forms
description: L’exécution pas à pas Xamarin.Forms permet à un utilisateur de sélectionner une valeur numérique à partir d’une plage de valeurs. Il se compose de deux boutons nommés avec moins et les signes plus. Manipuler les deux boutons modifie la valeur sélectionnée de la façon incrémentielle.
ms.prod: xamarin
ms.assetid: 62571B3E-D84B-4F52-9FC7-C105D6733B16
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/17/2018
ms.openlocfilehash: 6c89f04b1d1d87fed8d86d50cb68527391a7f317
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656204"
---
# <a name="xamarinforms-stepper"></a>Exécution pas à pas de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)

_Utiliser une exécution pas à pas pour la sélection d’une valeur numérique à partir d’une plage de valeurs._

Xamarin.Forms [ `Stepper` ](xref:Xamarin.Forms.Stepper) se compose de deux boutons nommés avec moins et les signes plus. Ces boutons peuvent être manipulées par l’utilisateur de sélectionner de manière incrémentielle un `double` valeur à partir d’une plage de valeurs.

Le [ `Stepper` ](xref:Xamarin.Forms.Stepper) définit quatre propriétés de type `double`:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) correspond à la quantité pour modifier la valeur sélectionnée par, valeur par défaut est 1.
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) est la valeur minimale de la plage, avec une valeur par défaut 0.
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) est la valeur maximale de la plage, avec une valeur par défaut de 100.
- [`Value`](xref:Xamarin.Forms.Stepper.Value) est la valeur de l’exécution pas à pas, ce qui peut être comprise entre `Minimum` et `Maximum` et a la valeur par défaut 0.

Ces propriétés sont soutenus par [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objets. Le [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriété a un mode de liaison par défaut de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), ce qui signifie qu’il est approprié comme source de liaison dans une application qui utilise le [ Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) architecture.

> [!WARNING]
> En interne, le [ `Stepper` ](xref:Xamarin.Forms.Stepper) garantit que [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) est inférieure à [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum). Si `Minimum` ou `Maximum` sont déjà définies afin que `Minimum` est non inférieur à `Maximum`, une exception est levée. Pour plus d’informations sur la configuration de la `Minimum` et `Maximum` propriétés, consultez [précautions](#precautions) section.

Le [ `Stepper` ](xref:Xamarin.Forms.Stepper) force le [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriété afin qu’il soit entre [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) et [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum), inclusif. Si le `Minimum` propriété est définie sur une valeur supérieure à la `Value` propriété, le `Stepper` définit le `Value` propriété `Minimum`. De même, si `Maximum` est défini sur une valeur inférieure à `Value`, puis `Stepper` définit le `Value` propriété `Maximum`.

[`Stepper`](xref:Xamarin.Forms.Stepper) définit un [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) événement est déclenché quand le [ `Value` ](xref:Xamarin.Forms.Stepper.Value) modifications, soit par le biais de manipulation d’utilisateur le `Stepper` ou lorsque l’application définit le `Value` propriété directement. Un `ValueChanged` événement est également déclenché quand le `Value` propriété est convertie comme décrit dans le paragraphe précédent.

Le [ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) objet qui accompagne le [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) événement a deux propriétés, tous deux de type `double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) et [ `NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). Au moment où l’événement est déclenché, la valeur de `NewValue` est identique à la [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriété de la [ `Stepper` ](xref:Xamarin.Forms.Stepper) objet.

## <a name="basic-stepper-code-and-markup"></a>Balisage et code d’exécution pas à pas de base

Le [ **StepperDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) exemple contient trois pages qui sont fonctionnellement identiques, mais sont implémentées de différentes façons. La première page utilise uniquement C# code, la deuxième utilise XAML avec un gestionnaire d’événements dans le code et de la troisième est possible d’éviter le Gestionnaire d’événements à l’aide de la liaison de données dans le fichier XAML.

### <a name="creating-a-stepper-in-code"></a>Création d’une exécution pas à pas dans le code

Le **Code d’exécution pas à pas de base** page dans le [ **StepperDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) exemple montre comment créer un [ `Stepper` ](xref:Xamarin.Forms.Stepper) et deux [ `Label` ](xref:Xamarin.Forms.Label) objets dans le code :

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

Le [ `Stepper` ](xref:Xamarin.Forms.Stepper) est initialisé pour avoir un [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) propriété 360 et un [ `Increment` ](xref:Xamarin.Forms.Stepper.Increment) propriété 30. Manipulation de la `Stepper` modifie la valeur sélectionnée de façon incrémentielle entre [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) à `Maximum` selon la valeur de la `Increment` propriété. Le [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) Gestionnaire de la `Stepper` utilise le [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriété de la `stepper` objet à définir le [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriété de la première [ `Label` ](xref:Xamarin.Forms.Label) et utilise le `string.Format` méthode avec le `NewValue` propriété des arguments d’événement pour définir le [ `Text` ](xref:Xamarin.Forms.Label.Text) propriété de la deuxième `Label`. Ces deux approches pour obtenir la valeur actuelle de la `Stepper` sont interchangeables.

L’émission des captures d’écran suivante le **Code d’exécution pas à pas de base** page :

[![Code de l’exécution pas à pas base](stepper-images/basic-stepper-code.png "Code de base exécution pas à pas")](stepper-images/basic-stepper-code-large.png#lightbox)

La seconde [ `Label` ](xref:Xamarin.Forms.Label) affiche le texte « (non initialisé) » jusqu'à ce que le [ `Stepper` ](xref:Xamarin.Forms.Stepper) est manipulé, ce qui entraîne la première [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) événement pour être déclenché.

### <a name="creating-a-stepper-in-xaml"></a>Création d’une exécution pas à pas dans XAML

Le **base XAML d’exécution pas à pas** page est fonctionnellement identique **Code d’exécution pas à pas de base** mais implémentée principalement dans XAML :

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

Le fichier code-behind contient le gestionnaire pour le [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) événement :

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

Il est également possible pour le Gestionnaire d’événements obtenir le [ `Stepper` ](xref:Xamarin.Forms.Stepper) qui déclenche l’événement via le `sender` argument. Le [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriété contient la valeur actuelle :

```csharp
double value = ((Stepper)sender).Value;
```

Si le [ `Stepper` ](xref:Xamarin.Forms.Stepper) objet ont été donnés un nom dans le fichier XAML avec un `x:Name` attribut (par exemple, « exécution pas à pas »), puis le Gestionnaire d’événements peut référencer cet objet directement :

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>L’exécution pas à pas de liaison de données

Le **base liaisons d’exécution pas à pas** page montre comment écrire une application presque équivalente qui élimine le [ `Value` ](xref:Xamarin.Forms.Stepper.Value) Gestionnaire d’événements à l’aide de [une liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md):

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

Le [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriété du premier [ `Label` ](xref:Xamarin.Forms.Label) est lié à la [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriété de la [ `Stepper` ](xref:Xamarin.Forms.Stepper), comme est le [ `Text` ](xref:Xamarin.Forms.Label.Text) propriété du deuxième `Label` avec un `StringFormat` spécification. La page des liaisons de pas à pas de **base** fonctionne un peu différemment des deux pages précédentes: Lorsque la page s’affiche pour la première `Label` fois, la deuxième affiche la chaîne de texte avec la valeur. Il s’agit d’un avantage de l’utilisation de la liaison de données. Pour afficher un texte sans liaison de données, vous devez initialiser spécifiquement le `Text` propriété de la `Label` ou simuler le déclenchement de la [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) événement en appelant le Gestionnaire d’événements à partir du constructeur de classe .

## <a name="precautions"></a>Précautions

La valeur de la [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) propriété doit toujours être inférieure à la valeur de la [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) propriété. Le code suivant extrait de code entraîne la [ `Stepper` ](xref:Xamarin.Forms.Stepper) pour lever une exception :

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

Le C# compilateur génère du code qui définit ces deux propriétés dans la séquence, et à quel moment le [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) propriété a la valeur 180, il est supérieur à la valeur par défaut [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) la valeur 100. Vous pouvez éviter l’exception dans ce cas en définissant le `Maximum` propriété premier :

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Paramètre [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) sur 360 n’est pas un problème, car il est supérieur à la valeur par défaut [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) la valeur 0. Lorsque `Minimum` est défini, la valeur est inférieure à la `Maximum` valeur 360.

Le même problème se produit dans XAML. Définir les propriétés dans un ordre qui garantit que [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) est toujours supérieure à `Minimum`:

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

Vous pouvez définir le [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) et [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) des valeurs pour les nombres négatifs, mais uniquement dans un ordre dans lequel `Minimum` est toujours inférieure à `Maximum`:

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

Le [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriété est toujours supérieure ou égale à la [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) valeur et inférieure ou égale à [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum). Si `Value` est défini sur une valeur en dehors de cette plage, la valeur sera être convertie pour être comprises entre, mais aucune exception n’est levée. Par exemple, ce code vous sera *pas* lever une exception :

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

Au lieu de cela, le [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriété est convertie en la [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) la valeur 100.

Voici un extrait de code ci-dessus :

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Lorsque [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) a la valeur 180, puis [ `Value` ](xref:Xamarin.Forms.Stepper.Value) est également défini sur 180.

Si un [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) Gestionnaire d’événements a été attaché au moment qui le [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriété est forcée sur autre chose que sa valeur par défaut de 0, puis un `ValueChanged` événement est déclenché. Voici un extrait du XAML :

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

Lorsque [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) a la valeur 180, [ `Value` ](xref:Xamarin.Forms.Stepper.Value) a également la valeur 180 et le [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) événement est déclenché. Cela peut se produire avant que le reste de la page a été construit, et le gestionnaire peut tenter de référencer d’autres éléments sur la page qui n’ont pas encore été créées. Vous souhaiterez peut-être ajouter du code pour le `ValueChanged` gestionnaire vérifie pour `null` les valeurs des autres éléments sur la page. Ou, vous pouvez définir le `ValueChanged` Gestionnaire d’événements après le [ `Stepper` ](xref:Xamarin.Forms.Stepper) valeurs ont été initialisés.

## <a name="related-links"></a>Liens associés

- [Exemple de démonstrations d’exécution pas à pas](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)
- [Exécution pas à pas API](xref:Xamarin.Forms.Stepper)
